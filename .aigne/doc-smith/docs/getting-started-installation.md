# Installation

To begin recording and replaying web sessions, you first need to add `rrweb` to your project. You can do this either by using a package manager like npm or by including the library from a Content Delivery Network (CDN).

## Using a Package Manager

For most modern web development workflows, installing `rrweb` via a package manager is the recommended approach. This allows you to easily manage versions and integrate `rrweb` with bundlers like Vite or Webpack.

There are two main packages you'll typically work with:

*   **`rrweb`**: The core library for recording web sessions.
*   **`rrweb-player`**: A feature-rich player component with a user interface for replaying sessions.

### Install `rrweb` (for recording)

To capture user sessions, install the `rrweb` package.

```bash NPM
npm install rrweb
```

```bash Yarn
yarn add rrweb
```

After installation, you can import it into your project:

```javascript Importing rrweb icon=logos:javascript
import * as rrweb from 'rrweb';

// Or, if you prefer named imports:
import { record } from 'rrweb';
```

### Install `rrweb-player` (for replaying)

To replay sessions with a pre-built user interface, you'll need the `rrweb-player` package.

```bash NPM
npm install rrweb-player
```

```bash Yarn
yarn add rrweb-player
```

To use the player, you must import both the JavaScript module and its corresponding stylesheet.

```javascript Importing rrweb-player icon=logos:javascript
import rrwebPlayer from 'rrweb-player';

// Don't forget to import the CSS for the player UI
import 'rrweb-player/dist/style.css';
```

## Using a CDN

If you prefer not to use a build system or want to quickly prototype, you can include `rrweb` directly in your HTML file using `<script>` tags from a CDN like jsDelivr or unpkg.

### For Recording

Include the core `rrweb` script in your HTML. This will make the `rrweb` object available globally.

```html HTML Setup for Recording icon=mdi:language-html5
<!DOCTYPE html>
<html>
  <head>
    <title>rrweb Recording</title>
    <script src="https://cdn.jsdelivr.net/npm/rrweb@latest/dist/rrweb.umd.cjs"></script>
  </head>
  <body>
    <script>
      // The rrweb object is now available on the window
      window.rrweb.record({
        emit(event) {
          console.log(event);
        },
      });
    </script>
  </body>
</html>
```

### For Replaying with UI

To use the player, you need to include both its JavaScript and CSS files.

```html HTML Setup for Replaying icon=mdi:language-html5
<!DOCTYPE html>
<html>
  <head>
    <title>rrweb Replay</title>
    <!-- Player Stylesheet -->
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/style.css"
    />
  </head>
  <body>
    <!-- Player Script -->
    <script src="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/index.umd.cjs"></script>
    <script>
      const events = []; // Paste your recorded events here
      new rrwebPlayer({
        target: document.body,
        props: {
          events,
        },
      });
    </script>
  </body>
</html>
```

With `rrweb` installed, you are now ready to start capturing sessions. The next section will guide you through the recording process.
