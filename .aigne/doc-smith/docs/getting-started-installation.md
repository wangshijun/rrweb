# Installation

This guide provides instructions for adding rrweb to your project. You can install rrweb using a package manager like npm or by including it directly in your HTML via a CDN. For most development workflows, using a package manager is the recommended approach.

## Using a Package Manager (npm/yarn)

If your project uses a build system like Vite, Webpack, or Rollup, you can install rrweb from the npm registry.

### 1. Install the Core Recorder

The `rrweb` package contains the core logic for recording session events.

```bash NPM icon=logos:npm-icon
npm install rrweb
```

```bash Yarn icon=logos:yarn
yarn add rrweb
```

### 2. Install the Player

To replay recorded sessions, you'll need the `rrweb-player`. It provides a full-featured UI with playback controls.

```bash NPM icon=logos:npm-icon
npm install rrweb-player
```

```bash Yarn icon=logos:yarn
yarn add rrweb-player
```

After installation, you can import the necessary modules and styles into your application.

```javascript Importing rrweb and rrweb-player icon=logos:javascript
// Import the recorder
import * as rrweb from 'rrweb';

// Import the player and its styles
import rrwebPlayer from 'rrweb-player';
import 'rrweb-player/dist/style.css';
```

### All-in-One Package

For convenience, the `@rrweb/all` package bundles the core `rrweb` recorder with the packer plugin, which can help reduce the size of the recorded data.

```bash NPM icon=logos:npm-icon
npm install @rrweb/all rrweb-player
```

```bash Yarn icon=logos:yarn
yarn add @rrweb/all rrweb-player
```


## Using a CDN

For quick prototypes, simple HTML pages, or online coding platforms like CodePen, you can use rrweb directly from a CDN.

Add the following scripts and stylesheet to your HTML file. The `rrweb` script provides the recording functionality, and the `rrweb-player` script and stylesheet provide the replaying UI.

```html HTML Setup icon=logos:html-5
<!-- rrweb Player Stylesheet -->
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/style.css"
/>

<!-- rrweb Core Recorder -->
<script src="https://cdn.jsdelivr.net/npm/rrweb@latest/dist/rrweb.umd.cjs"></script>

<!-- rrweb Player -->
<script src="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/index.umd.cjs"></script>
```

When included via CDN, `rrweb` and `rrwebPlayer` will be available as global variables on the `window` object.

```javascript Global Access icon=logos:javascript
const { record } = window.rrweb;
const { rrwebPlayer } = window;

// Now you can use record() and new rrwebPlayer()
```

---

With `rrweb` successfully installed, you are now ready to begin recording your first session. Proceed to the next section for a practical example.

**Next:** [Recording a Session](./getting-started-recording-a-session.md)