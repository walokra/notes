# GraphQL

## Books

[Production Ready GraphQL](https://book.productionreadygraphql.com/)
"Learn how to design and build predictable, performant, and secure GraphQL APIs at scale."

## Apollo Client, retrying

Refresh token and retry failed call:
- <https://able.bio/AnasT/apollo-graphql-async-access-token-refresh--470t1c8#>
- <https://github.com/AnasT/jwt-auth-poc/blob/master/client/src/graphql/index.ts>

## Validating and linting GraphQL

* [eslint-plugin-graphql](https://github.com/apollographql/eslint-plugin-graphql): 
checks tagged query strings inside JavaScript, or queries inside .graphql files, against a GraphQL schema.
* [graphql-schema-linter](https://github.com/cjoudrey/graphql-schema-linter): 
command line tool to validate GraphQL schema definitions against a set of rules.
* [graphql-eslint](https://github.com/dotansimha/graphql-eslint): 
 Lints both GraphQL schema and GraphQL operations.

GraphQL APIs are required to be self-documenting and every GraphQL API needs to respond to queries about its own structure. 
This system is called introspection. 
The key for GraphQL validation is doing the introspection query and getting the schema in GraphQL Schema Definition Language format. 
The query gets you the JSON representation of the shape of the API which you can convert to SDL.
See [more about GraphQL Schemas](https://www.apollographql.com/blog/backend/schema-design/three-ways-to-represent-your-graphql-schema/).

You can use the [Apollo tooling](https://github.com/apollographql/apollo-tooling) for the introspection.

```
npx apollo client:download-schema --endpoint=http://localhost:4444/graphql schema.graphql
```

### Using graphql-schema-linter

Create config `graphql-schema-linter.config.js` in your project

```javascript
module.exports = {
  rules: ['enum-values-sorted-alphabetically'],
  schemaPaths: ['path/to/my/schema/files/**.graphql'],
  customRulePaths: ['path/to/my/custom/rules/*.js'],
  rulesOptions: {
    'enum-values-sorted-alphabetically': { sortOrder: 'lexicographical' }
  }
};
```

And run `$ graphql-schema-linter`

Results e.g.

```javascript
1320:1 The type `Upload` is defined in the schema but not used anywhere.                      defined-types-are-used
74:3   The field `Company.phone_number` is not camel cased.                                   fields-are-camel-cased
```

### Running graphql-schema-linter in CI/CD

Example with GitLab CI

```
run_test:
  image: docker:19.03.1
  stage: test
  services:
    - docker:19.03.1-dind
  before_script:
    - apk add --no-cache docker-compose npm
  script:
    - "# Start GQL"
    - docker-compose -f docker-compose-introspection.yml up -d
    - "# Get introspection schema"
    - cd /tmp
    - npx apollo client:download-schema --endpoint=http://localhost:4000/graphql schema.graphql
    - cd $CI_PROJECT_DIR
    - docker-compose -f docker-compose-introspection.yml stop
    - docker-compose -f docker-compose-introspection.yml rm -f
    - "# Run tests"
    - npx graphql-schema-linter /tmp/schema.graphql
```
