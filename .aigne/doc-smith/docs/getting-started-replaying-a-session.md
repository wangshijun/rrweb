# Replaying a Session

Once you have recorded a session and have an array of events, `rrweb` provides two primary ways to play it back. You can use the core `rrweb.Replayer` for direct, programmatic control, or the `rrweb-player` component for a pre-built user interface with playback controls.

This guide will walk you through both methods.

## Method 1: Programmatic Replay with `rrweb.Replayer`

The `rrweb.Replayer` class is the core engine for replaying sessions. It provides a low-level API to control playback within an iframe that it creates. This approach is ideal when you need to build a custom player UI or control the replay programmatically without any default UI components.

### Basic Usage

To use the `Replayer`, you need to instantiate it with the array of recorded events. The replayer will then create an `<iframe>` inside a specified root element and begin rebuilding the DOM and applying changes.

Here is a complete, minimal example:

```html Replay with rrweb.Replayer icon=logos:html-5
<!DOCTYPE html>
<html>
<head>
  <title>rrweb Replayer Example</title>
  <!-- Include rrweb library from a CDN -->
  <script src="https://cdn.jsdelivr.net/npm/rrweb@latest/dist/rrweb.min.js"></script>
  <style>
    /* Basic styling for the replayer container */
    #replayer-wrapper {
      margin: 20px auto;
      border: 1px solid #ccc;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
  </style>
</head>
<body>

  <h2>Session Replay Container</h2>
  <div id="replayer-wrapper"></div>

  <script>
    // Assume 'events' is the array of events you captured during recording.
    // For this demo, we'll use a placeholder.
    const events = GET_YOUR_EVENTS;

    if (events && events.length > 1) {
      const replayer = new rrweb.Replayer(events, {
        // The root element to mount the replayer
        root: document.getElementById('replayer-wrapper'),
      });

      // Start playing the recording
      replayer.play();
    } else {
      console.log('Not enough events to replay.');
    }
  </script>

</body>
</html>
```

In this example, we create a new `Replayer` instance, passing it the array of events. The second argument is a configuration object where we specify the `root` DOM element. Calling `replayer.play()` starts the playback.

## Method 2: Using the `rrweb-player` Component

`rrweb-player` is a feature-rich UI component built on top of `rrweb.Replayer`. It provides a complete playback interface, including a timeline, play/pause buttons, speed controls, and more. This is the quickest way to integrate a fully functional session replay feature into your application.

### Installation

You can install `rrweb-player` via a package manager like npm or include it directly from a CDN.

**NPM**

```bash NPM Installation icon=logos:npm-icon
npm install --save rrweb-player
```

Then, import it into your project:

```javascript icon=logos:javascript
import rrwebPlayer from 'rrweb-player';
import 'rrweb-player/dist/style.css';
```

**CDN**

For quick prototyping or simple projects, you can use the CDN version.

```html CDN Setup icon=logos:html-5
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/style.css"
/>
<script src="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/index.umd.cjs"></script>
```

**Important**: Remember to include the CSS file for proper styling.

### Basic Usage

Instantiating the player is straightforward. You need to provide a target element and pass the events in the `props` object.

```javascript Player Initialization icon=logos:javascript
// Assuming 'events' is your array of recorded events.
const events = GET_YOUR_EVENTS;

new rrwebPlayer({
  target: document.body, // The element where the player will be mounted
  props: {
    events,
    width: 1024,
    height: 576,
    autoPlay: true,
  },
});
```

### Configuration Options

The `rrweb-player` component is highly customizable. Here are some of the most common options you can pass in the `props` object:

<x-field-group>
  <x-field data-name="events" data-type="array" data-required="true" data-desc="The array of rrweb events to be replayed."></x-field>
  <x-field data-name="width" data-type="number" data-default="1024" data-desc="The width of the player UI."></x-field>
  <x-field data-name="height" data-type="number" data-default="576" data-desc="The height of the player UI."></x-field>
  <x-field data-name="autoPlay" data-type="boolean" data-default="true" data-desc="Whether to start playing automatically upon initialization."></x-field>
  <x-field data-name="speed" data-type="number" data-default="1" data-desc="The default playback speed (e.g., 1 for normal, 2 for 2x speed)."></x-field>
  <x-field data-name="speedOption" data-type="number[]" data-default="[1, 2, 4, 8]" data-desc="An array of available playback speeds for the speed control UI."></x-field>
  <x-field data-name="showController" data-type="boolean" data-default="true" data-desc="Whether to display the playback controller UI (timeline, buttons, etc.)."></x-field>
</x-field-group>

All other configuration options for the core `rrweb.Replayer` (like `skipInactive`) can also be passed directly in the `props`.

## Summary

You have now learned the two main methods for replaying a recorded session:

-   **`rrweb.Replayer`**: Best for custom implementations where you need full programmatic control over the playback process.
-   **`rrweb-player`**: The recommended choice for quickly adding a full-featured, interactive player to your application.

With these fundamentals, you can now successfully record and replay user sessions. To dive deeper into the player's features and API, proceed to the [Player Component](./core-packages-player-component.md) guide.