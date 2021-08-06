# Node.js

## Cleanup

Delete all node_modules

`find . -name node_modules -type d -prune`
OR
`export LIST=$(mktemp); find . -name node_modules -type d -prune >| $LIST; $EDITOR $LIST && xargs -p rm -r < $LIST; rm $LIST`

## Parsing commanline parameters

- [Minimist](https://github.com/substack/minimist): For minimal argument parsing. 
- [Commander.js](https://github.com/tj/commander.js): Most adopted module for argument parsing. 
- [Meow](https://github.com/sindresorhus/meow): Lighter alternative to Commander.js 
- [Yargs](https://github.com/yargs/yargs): More sophisticated argument parsing (heavy). 
- [Vorpal.js](https://github.com/dthree/vorpal): Mature / interactive command-line applications with argument parsing. 


### Vanilla javascript argument parsing: 

[Node.js](https://nodejs.org/docs/latest/api/process.html#process_process_argv)

```javascript
const args = process.argv;
console.log(args);

This returns: 

$ node server.js one two=three four
['node', '/home/server.js', 'one', 'two=three', 'four']
```
