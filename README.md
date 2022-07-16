# Getting Started with Create React App

Start creating a react app in the project directory:

`npx create-react-app <app-name>`

Or, if you want to use the directory as app directory just use:

`npx create-react-app .`

# Electron integration

## Install electron dependencies

Now, in your app directory use the follow command line:

`npm i -D electron electron-is-dev`

This command install a useful npm package called electron-is-dev used for
checking whether your electron app is in development or production. You used the
-D flag to install electron under dev dependencies.

## Create a main.js file

Now, you need to create a main.js (commonly named this way) file, to add the
Electron settings to it. You can create it in your public directory, but I will
create it in my src folder and I will restructure all directories within it,
this way:

![src folder structure](https://github.com/FilipeGarrido/arduino-serial-communication-react-electron-app/blob/main/src/Assets/src-structure.PNG?raw=true)

![src folder structure opened](https://github.com/FilipeGarrido/arduino-serial-communication-react-electron-app/blob/main/src/Assets/src-structure-opened.PNG?raw=true)

After you create your main.js file, paste this code into it:

```javascript
const path = require('path');

const { app, BrowserWindow } = require('electron');
const isDev = require('electron-is-dev');

function createWindow() {
  // Create the browser window.
  const win = new BrowserWindow({
    width: 800,
    height: 600
  });

  // and load the index.html of the app.
  // win.loadFile("index.html");
  win.loadURL(
    isDev
      ? 'http://localhost:3000'
      : `file://${path.join(__dirname, '../build/index.html')}`
  );
  // Open the DevTools.
  if (isDev) {
    win.webContents.openDevTools({ mode: 'detach' });
  }
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(createWindow);

// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.on('activate', () => {
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow();
  }
});

```

This code creates a Browserwindow instance provided by electron, which is used
to render the web contents. It then loads the HTML file in the directory on to
the Browserwindow.

It also handles other window events like closed when the window is closed, focus
when the window is in focus, ready-to-show when the web page has been rendered,
and window states like maximize, minimize, restore.

To read more on the settings, you can visit the
[electron quick start docs](https://www.electronjs.org/pt/docs/latest/tutorial/quick-start).

## Configuring package.JSON

You now have electron installed, but you still need to make some changes to the
package.json to sync the browser and desktop builds. First, update the project's
entry file.

In your package.json file, add this before your scripts:

`"main": "src/electron/main.js",`

This line of package.JSON will use the main.js file created earlier for the main
electron configurations.

Next, install the following packages, concurrently and wait-on. These packages
will listen to the app, and when it launches on the browser, it will launch as
an electron app instead.

`npm i -D concurrently wait-on`

The `concurrently` allows us to run multiple commands within one script and
`wait-on` will wait for port 3000 which is the default CRA port, to launch the
app.

In your scripts in the package.json file, add:

```json
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "dev": "concurrently -k \"BROWSER=none npm start\" \"npm:electron\"",
    "electron": "wait-on tcp:3000 && electron ."
  },

```

The flag, BROWSER=none that you passed in the dev script will prevent the
browser from launching once the React app compiles successfully.

So, you have everything set up. Running `npm run dev` should start an electron
application.

# References

[Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

[React documentation](https://reactjs.org/).

[React and Electron integration](https://www.section.io/engineering-education/desktop-application-with-react/).

[Electron docs](https://www.electronjs.org/pt/docs/latest).
