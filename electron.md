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
  } = require("electron-devtools-installer");

  installExtension([REACT_DEVELOPER_TOOLS])
    .then((name) => console.log(`Added Extension:  ${name}`))
    .catch((err) => console.error("An error occurred: ", err));
};
```

## Media devices

https://github.com/electron/electron/issues/16513
https://github.com/electron/electron/issues/4931

## Electron and HiDPI displays

Electron has some “issues” with HiDPI displays on Windows and there are some things you can do to make things better. Here’s our story.

We did an Electron application with React and which loads a video element from third party library (Node addon). On macOS everything looked fine but on HiDPI display on Windows 10 the video element didn’t cover the whole screen when the system hiDPI scale setting was e.g. 125%. The issue was discussed on Electron Github https://github.com/electron/electron/issues/9417#issuecomment-300538488 which also led to correct solution.

```javascript
if (process.platform === "win32") {
  app.commandLine.appendSwitch("high-dpi-support", 1);
}
```

But that wasn’t quite enough in our case and setting the suggested app.commandLine.appendSwitch('force-device-scale-factor', 1) was not a good option. The remaining issue was still the zoom level of the video element inside a div. Fortunately the JavaScript’s window object has devicePixelRatio property which you can use to set correct zoom level.

In our renderer’s React code we set the style as following

```javascript
const devicePixelRatio = isNaN(window.devicePixelRatio) || window.devicePixelRatio == 0 ? 1 : window.devicePixelRatio;
const style = {
  zoom: 1 / devicePixelRatio,
};
```

You can also check the correct scale factor of the display in main process and pass it to renderer side as a prop.

```javascript
const { screen } = require("electron");

let scaleFactor = 1;
if (process.platform === "win32") {
  scaleFactor = screen.getPrimaryDisplay().scaleFactor;
}
```

Also during debugging this issue and how the different ways of setting zoom levels work with React and the video element I got across to a “feature” where the previous zoomFactor is getting cached, unless you manually reset browserWindow's zoom https://github.com/electron/electron/issues/10572. The workaround is not to set zoomFactor in webPreferences when you create the BrowserWindow but after the 'ready-to-show’ event and only then showing the window. This was a tricky thing to find when testing different approaches and then suddenly the React elements were zoomed although they shouldn’t have been.

## Cookies

```javascript
mainWindow.webContents.on("did-finish-load", () => {
  // Query all cookies.
  session.defaultSession.cookies
    .get({})
    .then((cookies) => {
      log.info("did-finish-load defaultSession 🍪");
      log.info(cookies);
    })
    .catch((error) => {
      log.info(error);
    });

  mainWindow.webContents.session.cookies
    .get({})
    .then((cookies) => {
      log.info("did-finish-load mainWindow.webContents 🍪");
      log.info(cookies);
    })
    .catch((error) => {
      log.info(error);
    });
});
```

```javascript
let ses = mainWindow.webContents.session;
ses.cookies.get({ url: "https://example.com" }, (error, cookies) => {
  console.log(cookies);
  let cookieStr = "";
  for (var i = 0; i < cookies.length; i++) {
    let info = cookies[i];
    cookieStr += `${info.name}=${info.value};`;
    log.info(info.value, info.name);
  }
  log.info(cookieStr);
});
```

## Net GET request

```javascript
const request = net.request({
  method: "GET",
  protocol: "https:",
  hostname: `<HOSTNAME>`,
  port: 443,
  path: "/api/endpoint",
});

request.on("response", (response) => {
  console.log(`STATUS: ${response.statusCode}`);
  console.log(`HEADERS: ${JSON.stringify(response.headers)}`);
  response.on("data", (chunk) => {
    console.log(`BODY: ${chunk}`);
  });
  response.on("end", () => {
    console.log("No more data in response.");
  });
});
request.end();
```

### POST request

```javascript

terminateConference() {
  let { ipcRenderer } = window;

  const data = {};
  // Send information to the main process
  ipcRenderer = ipcRenderer || this.props.ipcRenderer;
  ipcRenderer.send('request-post-action', data);
}

// Listener for sending API call on request-post-action
ipcMain.on('request-post-action', (event, arg) => {

  // Handle uncaught errors which might happen on ending conference
  process.on('uncaughtException', (err) => {
    console.error('API call failed!', err);
  });

  const variable = store.get('variable');

  // Create ClientRequest for sending API call file
  const request = net.request({
    method: 'POST',
    url: `https://${HOST}/api/endpoint`,
    session: mainWindow.webContents.session
  });

  let body = ''

  var postData = JSON.stringify({
    "data" : variable
  });
  console.log(postData);

  request.on('response', (response) => {
    // check response.statusCode to determine if the request succeeded
    console.log(`STATUS: ${response.statusCode}`);
    console.log(`HEADERS: ${JSON.stringify(response.headers)}`);

    // capture body of response
    // - can be called more than once for large result
    response.on('data', (chunk) => {
      console.log(`BODY: ${chunk}`);
      body += chunk.toString();
    })

    // when response is complete, print body
    response.on('end', () => {
      console.log(`BODY: ${body}`);
    })
  })

  request.write(postData);

  request.end();
});
```
