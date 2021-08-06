# Electron

## Screen share

- https://github.com/electron/electron/issues/16513#issuecomment-602070250

## updater

- https://github.com/iffy/electron-updater-example/blob/master/main.js
- https://www.electron.build/auto-update
- https://blog.alexdevero.com/electron-app-pt3/

## releaseNotes:

- https://github.com/electron-userland/electron-builder/issues/1511

## abort upload

- https://github.com/electron-userland/electron-builder/issues/1150

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

## Media devices

https://github.com/electron/electron/issues/16513
https://github.com/electron/electron/issues/4931

## Electron and HiDPI displays

Electron has some “issues” with HiDPI displays on Windows and there are some things you can do to make things better. Here’s our story.

We did an Electron application with React and which loads a video element from third party library (Node addon). On macOS everything looked fine but on HiDPI display on Windows 10 the video element didn’t cover the whole screen when the system hiDPI scale setting was e.g. 125%. The issue was discussed on Electron Github https://github.com/electron/electron/issues/9417#issuecomment-300538488 which also led to correct solution.

```javascript
if (process.platform === 'win32') {
  app.commandLine.appendSwitch('high-dpi-support', 1)
}
```

But that wasn’t quite enough in our case and setting the suggested app.commandLine.appendSwitch('force-device-scale-factor', 1) was not a good option. The remaining issue was still the zoom level of the video element inside a div. Fortunately the JavaScript’s window object has devicePixelRatio property which you can use to set correct zoom level.

In our renderer’s React code we set the style as following

```javascript
const devicePixelRatio = (isNaN(window.devicePixelRatio) || window.devicePixelRatio == 0) ? 1 : window.devicePixelRatio;
const style = {
  zoom: 1 / devicePixelRatio,
}
```

You can also check the correct scale factor of the display in main process and pass it to renderer side as a prop.

```javascript
const { screen } = require('electron');

let scaleFactor = 1;
if (process.platform === 'win32') {
  scaleFactor = screen.getPrimaryDisplay().scaleFactor;
}
```

Also during debugging this issue and how the different ways of setting zoom levels work with React and the video element I got across to a “feature” where the previous zoomFactor is getting cached, unless you manually reset browserWindow's zoom https://github.com/electron/electron/issues/10572. The workaround is not to set zoomFactor in webPreferences when you create the BrowserWindow but after the 'ready-to-show’ event and only then showing the window. This was a tricky thing to find when testing different approaches and then suddenly the React elements were zoomed although they shouldn’t have been.
