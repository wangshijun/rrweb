# Player Component

The `rrweb-player` is a feature-rich UI component designed for replaying sessions recorded by rrweb. While the core `rrweb.Replayer` API provides the engine for playback, `rrweb-player` wraps it with a complete user interface, including a timeline, play/pause controls, speed adjustments, and more.

This guide covers how to install, configure, and programmatically control the `rrweb-player` component in your application.

## Installation

You can integrate `rrweb-player` into your project either through a CDN or by installing it from a package manager like npm.

### CDN

For quick integration, include the stylesheet and script directly in your HTML file.

```html HTML Setup icon=logos:html-5
<head>
  <!-- Player Stylesheet -->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/style.css"
  />
</head>
<body>
  <!-- Player Script -->
  <script src="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/index.umd.cjs"></script>
</body>
```

### npm/yarn

For projects using a build system, install the package and import it along with its CSS.

```shell NPM Installation icon=logos:npm-icon
npm install --save rrweb-player
```

Then, import the player and its styles into your JavaScript or TypeScript file.

```javascript Importing the Player icon=logos:javascript
import rrwebPlayer from 'rrweb-player';
import 'rrweb-player/dist/style.css';
```

## Basic Usage

To use the player, you need a target DOM element to mount it on and an array of rrweb events to replay.

```javascript Basic Player Initialization icon=logos:javascript
// Assuming 'events' is an array of rrweb events you have recorded

const player = new rrwebPlayer({
  target: document.body, // The element where the player will be mounted
  props: {
    events, // The array of events to replay
  },
});
```

This will render the player in the specified `target` element and automatically start playing the session.

## Configuration (Props)

The behavior and appearance of the `rrweb-player` can be customized through its `props`. Below are the available options.

<x-field-group>
  <x-field data-name="events" data-type="eventWithTime[]" data-required="true" data-default="[]" data-desc="The array of rrweb events to be replayed."></x-field>
  <x-field data-name="width" data-type="number" data-default="1024" data-desc="The width of the replayer viewport."></x-field>
  <x-field data-name="height" data-type="number" data-default="576" data-desc="The height of the replayer viewport."></x-field>
  <x-field data-name="maxScale" data-type="number" data-default="1" data-desc="The maximum zoom scale of the replayer. Set to 0 for unlimited scaling."></x-field>
  <x-field data-name="autoPlay" data-type="boolean" data-default="true" data-desc="If true, the player will start playing automatically upon initialization."></x-field>
  <x-field data-name="speed" data-type="number" data-default="1" data-desc="The default playback speed."></x-field>
  <x-field data-name="speedOption" data-type="number[]" data-default="[1, 2, 4, 8]" data-desc="An array of available playback speed options to display in the controller UI."></x-field>
  <x-field data-name="showController" data-type="boolean" data-default="true" data-desc="If false, the player's default controller UI (timeline, buttons) will be hidden."></x-field>
  <x-field data-name="skipInactive" data-type="boolean" data-default="false" data-desc="If true, periods of user inactivity will be skipped during playback."></x-field>
  <x-field data-name="tags" data-type="Record<string, string>" data-default="{}" data-desc="A key-value map to customize the styling of custom events on the timeline."></x-field>
  <x-field data-name="inactiveColor" data-type="string" data-default="#D4D4D4" data-desc="A valid CSS color string for the inactive periods indicator on the progress bar."></x-field>
</x-field-group>

Any additional options provided in `props` will be passed directly to the underlying `rrweb.Replayer` instance. For a full list of available replayer options, please refer to the [rrweb Replayer options documentation](https://github.com/rrweb-io/rrweb/blob/master/guide.md#options-1).

## Programmatic Control (API)

In addition to the visual controller, you can interact with the `rrweb-player` instance programmatically. This is particularly useful when building a custom user interface.

### Methods

The player instance exposes several methods for controlling playback.

| Method | Description |
|---|---|
| `play()` | Starts or resumes playback. |
| `pause()` | Pauses playback. |
| `toggle()` | Toggles between play and pause states. |
| `goto(timeOffset: number, play?: boolean)` | Jumps to a specific point in time (in milliseconds from the start). If `play` is true, it will start playing from that point. |
| `setSpeed(speed: number)` | Sets the playback speed. |
| `toggleSkipInactive()` | Toggles the skipping of inactive periods. |
| `addEvent(event: eventWithTime)` | Adds a new event to the timeline dynamically. |
| `getMetaData()` | Returns metadata about the session, including `startTime`, `endTime`, and `totalTime`. |
| `getReplayer()` | Returns the underlying `rrweb.Replayer` instance. |
| `triggerResize()` | Manually triggers the player to recalculate its dimensions. Call this after changing the container's size. |

### Listening to Events

The player emits events that allow you to monitor its state. You can listen to these events using the `addEventListener` method.

```javascript Listening to Player Events icon=logos:javascript
const player = new rrwebPlayer({
  target: document.body,
  props: { events },
});

// Listen for updates to the current playback time
player.addEventListener('ui-update-current-time', (event) => {
  console.log('Current time:', event.payload);
});

// Listen for changes in the player's state (e.g., playing, paused)
player.addEventListener('ui-update-player-state', (event) => {
  console.log('Player state:', event.payload);
});
```

Key events include:
- `ui-update-current-time`: Fires repeatedly with the current time offset as the payload.
- `ui-update-player-state`: Fires when the player state changes (e.g., 'playing', 'paused', 'finished').
- `ui-update-progress`: Fires when the user interacts with the progress bar.

## Example: Building a Custom Controller

You can hide the default controller and build your own by combining props and API methods.

First, set up your HTML with a target for the player and your custom control buttons.

```html Custom Controls HTML icon=logos:html-5
<div id="player-container"></div>
<div id="custom-controls">
  <button id="play-btn">Play</button>
  <button id="pause-btn">Pause</button>
  <button id="goto-btn">Go to 15s</button>
</div>
```

Next, in your JavaScript, initialize the player with `showController: false` and attach event listeners to your buttons.

```javascript Custom Controller Logic icon=logos:javascript
const playerContainer = document.getElementById('player-container');

const player = new rrwebPlayer({
  target: playerContainer,
  props: {
    events,
    showController: false, // Hide the default UI
  },
});

document.getElementById('play-btn').addEventListener('click', () => {
  player.play();
});

document.getElementById('pause-btn').addEventListener('click', () => {
  player.pause();
});

document.getElementById('goto-btn').addEventListener('click', () => {
  // Go to 15000 milliseconds (15 seconds)
  player.goto(15000);
});
```

## Summary

The `rrweb-player` provides a powerful and customizable way to replay user sessions. You can use it out-of-the-box for a complete UI solution or leverage its comprehensive API to build a fully custom playback experience.

To better understand the data that `rrweb-player` consumes, you can learn more about how rrweb creates a [DOM Snapshot](./core-packages-dom-snapshot.md).
