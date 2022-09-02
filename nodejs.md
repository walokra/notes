# Node.js

## Cleanup

Delete all node_modules

`find . -name node_modules -type d -prune`
OR
`export LIST=$(mktemp); find . -name node_modules -type d -prune >| $LIST; $EDITOR $LIST && xargs -p rm -r < $LIST; rm $LIST`


## Console.log maxdepth

NODE_OPTIONS="--require util set-console-options.js" 

```
const util = require('util');
const arr = Array(101).fill(0);

console.log(arr); // Logs the truncated array
util.inspect.defaultOptions.maxArrayLength = null; //enable full object reproting in console.log
console.log(arr); // logs the full array
```

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


## Write to file

```
import fs from 'fs';

const writeFileSync = function (path: fs.PathLike, buffer: any, permission?: fs.Mode | null | undefined) {
    // eslint-disable-next-line no-param-reassign
    permission = permission || 0o777; // 0666

    let fileDescriptor;

    try {
        fileDescriptor = fs.openSync(path, 'w', permission);
    } catch (e) {
        fs.chmodSync(path, permission);
        fileDescriptor = fs.openSync(path, 'w', permission);
    }

    if (fileDescriptor) {
        fs.writeSync(fileDescriptor, buffer, 0, buffer.length, 0);
        fs.closeSync(fileDescriptor);
    }
};

writeFileSync('foo.pdf', putObjects[0].Body);

```
