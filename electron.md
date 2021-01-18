# Electron

## Electron and logs

- electron-log which allows to route explicit logs to a file
- electron-undhandled which allows to catch unhandled exceptions and route them to the console or a dialog; but this still would require setting up an own file log
- electron-timber which allows to log renderer messages to the main process (or to a stream)

## Extensions on Electron

```javascript
const installExtensions = async () => {
  const {
    default: installExtension,
    REACT_DEVELOPER_TOOLS,
    // eslint-disable-next-line import/no-extraneous-dependencies
  } = require('electron-devtools-installer');

  installExtension([REACT_DEVELOPER_TOOLS])
    .then((name) => console.log(`Added Extension:  ${name}`))
    .catch((err) => console.error('An error occurred: ', err));
};
```
