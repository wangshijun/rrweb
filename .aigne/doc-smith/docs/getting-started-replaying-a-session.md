# Replaying a Session

After you have successfully recorded a series of events, the next step is to play them back. `rrweb` provides two primary methods for replaying sessions: a core `Replayer` for programmatic control and a feature-rich `rrweb-player` component that includes a full user interface.

This guide will walk you through both approaches.

## Using the Core `rrweb.Replayer`

The `rrweb.Replayer` is the foundational engine for playback. It operates directly on the recorded event data and renders the session into a target element, typically an iframe. This method is ideal when you need to build a custom player UI or control the playback programmatically without any default controls.

### Step 1: Prepare the HTML

First, you need an HTML element to serve as the container for the replayer. The replayer will attach its iframe to this element.

```html Replay Container icon=mdi:code-tags
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>rrweb Replay</title>
  </head>
  <body>
    <div id="replay-container"></div>
    <script src="https://cdn.jsdelivr.net/npm/rrweb@latest/dist/rrweb.min.js"></script>
    <script src="replay.js"></script>
  </body>
</html>
```

### Step 2: Initialize the Replayer

In your JavaScript file, you can initialize the `rrweb.Replayer` with the array of events you captured during the recording phase. The `root` option in the configuration specifies where the replayer should be mounted.

```javascript replay.js icon=logos:javascript
// Assume 'events' is an array of rrweb events captured from a recording.
// You would typically fetch these from a server or local storage.
const events = GET_YOUR_EVENTS;

// Find the container element
const replayContainer = document.getElementById('replay-container');

// Create a new Replayer instance
const replayer = new rrweb.Replayer(events, {
  root: replayContainer,
});

// Start playing the recording
replayer.play();
```

This will create an iframe inside your `#replay-container` div and begin replaying the user's session from the provided events.

## Using the `rrweb-player` Component

For a more complete out-of-the-box solution, `rrweb-player` provides a full-featured UI with a timeline, play/pause buttons, speed controls, and more. It uses the core `Replayer` internally but saves you the effort of building the user interface.

### Step 1: Installation

You can include `rrweb-player` from a CDN or install it via npm.

**CDN:**

Include the stylesheet and the script in your HTML file.

```html icon=mdi:code-tags
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/style.css"
/>
<script src="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/index.umd.cjs"></script>
```

**NPM:**

Install the package in your project.

```shell icon=mdi:bash
npm install --save rrweb-player
```

Then, import it into your JavaScript file.

```javascript icon=logos:javascript
import rrwebPlayer from 'rrweb-player';
import 'rrweb-player/dist/style.css';
```

### Step 2: Initialize the Player

Create an instance of `rrwebPlayer`, specifying a target element and passing the events array as a prop.

```javascript icon=logos:javascript
// Assume 'events' is an array of rrweb events.
const events = GET_YOUR_EVENTS;

new rrwebPlayer({
  target: document.body, // The container element for the player
  props: {
    events,
    width: 1024, // Optional: player width
    height: 576, // Optional: player height
    autoPlay: true, // Optional: automatically start playing
  },
});
```

This will render a complete player interface in the specified `target` element, ready for the user to interact with.

---

Now that you know how to record and replay a basic session, you can explore more advanced configurations. For a deeper look at tailoring the recording process, see the [Recording Engine](./core-packages-recording-engine.md) documentation.