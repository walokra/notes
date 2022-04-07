# Using CASL and roles with persisted permissions

Implementing Attributes-based access control (ABAC) with CASL and roles (RBAC).

RBAC
RBAC is a simple and efficient solution to manage user permissions in an organization.
We have to create roles for each user group and assign permissions for certain operations to each role.

ABAC
ABAC is an authorization mechanism granting access dynamically based on user characteristics, environment context, action types, and more.
The difference between ABAC and RBAC is that ABAC supports Boolean logic, if the user meets this condition he is allowed to do that action.

Attribute-based Access Control (ABAC) enables more flexible control of access permissions as you can grant, adjust or revoke permissions instantly.

CASL
CASL is a robust Authorization JavaScript library and including RBAC it also supports ABAC implementation by adding condition expression to the permission.

Authorization is of paramount importance in every computer system. RBAC is a simple but sufficient solution to manage access in an uncomplicated system. However, it is not flexible and scalable and that is why ABAC becomes an efficient approach in this case. In the contrast, ABAC may be costly to implement. There is no all-in-one solution and it depends on your business requirements. Remember that use the right tool for the right job.

Resources:

- [CASL Cookbook](https://casl.js.org/v5/en/cookbook/roles-with-persisted-permissions)
- [Nest.js and CASL example](https://mfi.engineering/extensible-and-secure-authorization-with-nestjs-and-casl-c6f6d1ceefd5)

## Prerequirements

- Multitenant environment
- Organizations have needs to define different permissions for custom groups
- So we need roles and groups

## Table structure

- We have global Users table
- Users are grouped by tenant in TenantUsers
- TenantUsers have a role or roles
- Users can be added to a group
- Groups and roles are tenant specific
- Permissions can be added to a role

Table structure in https://dbdiagram.io/ syntax

```
Table Users {
  id uuid [pk, not null, unique]
  givenName string
  familyName string
  email string
  phone string
  extId string
  created_at timestamp [not null]
}

Table TenantUsers {
  TenantId uuid [ref: > Tenants.id]
  UserId uuid [ref: > Users.id]
  Roles array [ref: > Roles.id]
  created_at timestamp [not null]
}

Table Tenants {
  id uuid [pk]
  created_at timestamp [not null]
}

Table Roles {
  id int [pk, increment]
  TenantId uuid [ref: > Tenants.id]
  type int
  createdAt date
  updatedAt date
  value string
}

Table Permissions {
  id int [pk, increment]
  action string
  subject string
  fields array
  conditions string
  inverted boolean
  system boolean
  createdAt date
  updatedAt date
}

Table RolePermissions {
  RoleId int [ref: > Roles.id]
  PermissionId int [ref: > Permissions.id]
}

Table Groups {
  id int [pk, increment]
  TenantsId uuid [ref: > Tenants.id]
  Roles array [ref: > Roles.id]
}

Table GroupUsers {
  id int [pk, increment]
  GroupId int [ref: > Groups.id]
  UserId uuid [ref: > Users.id]
}

```

## Defining CASL permissions to user

In this example we are adding permissions directly to the JWT we are generating and signing so we don't have to fetch user's permissions every time from the database (or cache) when user makes a request. We get the claims.role from the authentication token.

`auth.js`

```JavaScript
let permissions = [];
if (claims.role) {
  const rolePermissions = await RolePermissionsService.get(claims.role);
  permissions = rolePermissions.map((p) => ({
    action: p.Permission.action,
    subject: p.Permission.subject,
    fields: p.Permission.fields,
    conditions: p.Permission.conditions,
  }));
}
```

Our service for fetching permissions uses Sequelize.

`services/rolePermissions.js`

```JavaScript
const getAll = async (options) => {
  const where = {};
  if (options?.where) {
    Object.assign(where, options.where);
  }
  const rolePermissions = await RolePermission.findAll({
    include: [
      {
        association: RolePermission.Role,
        attributes: ['value'],
      },
      {
        association: RolePermission.Permission,
        attributes: ['action', 'subject', 'fields', 'conditions'],
        where,
      },
    ],
  });

  return rolePermissions;
};
```

`models/rolepermissions.js`

```JavaScript
const { Model } = require('sequelize');

module.exports = (sequelize, DataTypes) => {
  class RolePermission extends Model {
    static associate(models) {
      this.Permission = this.belongsTo(models.Permission);
      this.Role = this.belongsTo(models.Role);
    }

    toJSON() {
      return {
        ...this.Permission?.toJSON(),
        role: this.Role?.value,
      };
    }
  }

  RolePermission.init(
    {
      RoleId: {
        type: DataTypes.INTEGER,
        allowNull: false,
        references: {
          model: 'Role',
          key: 'id',
        },
      },
      PermissionId: {
        type: DataTypes.INTEGER,
        allowNull: false,
        references: {
          model: 'Permission',
          key: 'id',
        },
      },
    },
    {
      sequelize,
      modelName: 'RolePermission',
      timestamps: false,
    },
  );

  return RolePermission;
};
```

`models/role.js`

```JavaScript
const { Model } = require('sequelize');

module.exports = (sequelize, DataTypes) => {
  class Role extends Model {
    static associate() {}

    static get Type() {
      return {
        Admin: 1,
      };
    }

    toJSON() {
      return {
        type: this.type,
        value: this.value,
      };
    }
  }

  Role.init(
    {
      type: DataTypes.INTEGER,
      value: DataTypes.STRING,
    },
    {
      sequelize,
      modelName: 'Role',
    },
  );

  return Role;
};
```

`models/permission.js`

```JavaScript
const { Model } = require('sequelize');

module.exports = (sequelize, DataTypes) => {
  class Permission extends Model {
    static associate() {}

    toJSON() {
      return {
        id: this.id,
        action: this.action,
        subject: this.subject,
        fields: this.fields,
        conditions: this.conditions,
        inverted: this.inverted,
        system: this.system,
      };
    }
  }

  Permission.init(
    {
      id: {
        type: DataTypes.INTEGER,
        primaryKey: true,
        allowNull: false,
      },
      action: DataTypes.STRING,
      subject: DataTypes.STRING,
      fields: DataTypes.ARRAY(DataTypes.TEXT),
      conditions: DataTypes.JSON,
      inverted: DataTypes.BOOLEAN,
      system: DataTypes.BOOLEAN,
      createdAt: DataTypes.DATE,
      updatedAt: DataTypes.DATE,
    },
    {
      sequelize,
      modelName: 'Permission',
    },
  );

  return Permission;
};
```

`middleware/abilities.js`

```JavaScript
const { AbilityBuilder, Ability } = require('@casl/ability');
const { User } = require('../models');

// See original source: https://github.com/stalniy/casl-persisted-permissions-example/blob/master/src/helpers/interpolate.ts
const parseCondition = (template, vars) => {
  if (!template) {
    return null;
  }

  // Our rules are as JSON on database, so for interpolation we stringify it first
  JSON.parse(JSON.stringify(template), (_, rawValue) => {
    if (rawValue[0] !== '$') {
      return rawValue;
    }

    const name = rawValue.slice(2, -1);
    const value = get(vars, name);

    if (typeof value === 'undefined') {
      throw new ReferenceError(`Variable ${name} is not defined`);
    }

    return value;
  });

  return null;
};

const defineAbilitiesFor = (user) => {
  const { can: allow, build } = new AbilityBuilder(Ability);

  user.permissions.forEach((item) => {
    const parsedConditions = parseCondition(item.conditions, { user });
    allow(item.action, item.subject, item.fields, parsedConditions);
  });

  return build();
};

module.exports = {
  defineAbilitiesFor,
};
```

```JavaScript
const express = require('express');
const passport = require('passport');
const { defineAbilitiesFor } = require('./middleware/abilities');

const app = express();

app.use(express.json());

app.use(
  passport.authenticate('signed-jwt', { session: false }),
  (req, _, next) => {
    req.abilities = defineAbilitiesFor(req.user);
    next();
  },
  ...
  <your routes here>
  ...
);
```
