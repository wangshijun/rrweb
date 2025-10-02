# Player Component

The `rrweb-player` is a pre-built UI component that provides a feature-rich playback interface for rrweb sessions. It utilizes the core `rrweb.Replayer` engine internally and adds essential controls like a progress bar, play/pause buttons, and speed adjustments. This component is designed to be a drop-in solution for replaying recorded events with a user-friendly interface.

## Installation

You can integrate `rrweb-player` into your project either through a CDN or by installing it from the NPM registry.

### Using a CDN

For quick integration, include the stylesheet and script directly in your HTML file. This method requires no build setup.

```html HTML Setup icon=logos:html-5
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/style.css"
/>
<script src="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/index.umd.cjs"></script>
```

### Using NPM

For projects with a build process, install the package using npm or yarn.

```shell NPM Installation icon=logos:npm-icon
npm install --save rrweb-player
```

After installation, import the player and its required CSS into your application.

```javascript Importing the Player icon=logos:javascript
import rrwebPlayer from 'rrweb-player';
import 'rrweb-player/dist/style.css';
```

## Basic Usage

To use the player, instantiate it with a target DOM element and provide the recorded events through the `props` object. The player will mount itself onto the specified target.

```javascript Basic Player Initialization icon=logos:javascript
// Assuming 'events' is an array of recorded rrweb events
new rrwebPlayer({
  target: document.body, // The element where the player will be mounted
  props: {
    events,
  },
});
```

## Configuration (Props)

The `rrweb-player` component can be configured through a set of properties passed during initialization. The following options allow you to customize its appearance and behavior.

<x-field-group>
  <x-field data-name="events" data-type="eventWithTime[]" data-required="true" data-default="[]">
    <x-field-desc markdown>An array of `rrweb` events to be replayed.</x-field-desc>
  </x-field>
  <x-field data-name="width" data-type="number" data-default="1024">
    <x-field-desc markdown>The width of the player container in pixels.</x-field-desc>
  </x-field>
  <x-field data-name="height" data-type="number" data-default="576">
    <x-field-desc markdown>The height of the player container in pixels.</x-field-desc>
  </x-field>
  <x-field data-name="maxScale" data-type="number" data-default="1">
    <x-field-desc markdown>The maximum scale of the replayer content (e.g., `1` = 100%). Set to `0` for an unlimited scale.</x-field-desc>
  </x-field>
  <x-field data-name="autoPlay" data-type="boolean" data-default="true">
    <x-field-desc markdown>Determines if the playback should start automatically upon initialization.</x-field-desc>
  </x-field>
  <x-field data-name="speed" data-type="number" data-default="1">
    <x-field-desc markdown>The default playback speed.</x-field-desc>
  </x-field>
  <x-field data-name="speedOption" data-type="number[]" data-default="[1, 2, 4, 8]">
    <x-field-desc markdown>An array of available playback speed options to display in the controller UI.</x-field-desc>
  </x-field>
  <x-field data-name="showController" data-type="boolean" data-default="true">
    <x-field-desc markdown>If set to `false`, the default player controls (progress bar, buttons) will be hidden. This is useful for creating a custom UI.</x-field-desc>
  </x-field>
  <x-field data-name="tags" data-type="Record<string, string>" data-default="{}">
    <x-field-desc markdown>A key-value map to customize the styling of custom events displayed on the progress bar.</x-field-desc>
  </x-field>
  <x-field data-name="inactiveColor" data-type="string" data-default="#D4D4D4">
    <x-field-desc markdown>A valid CSS color string to customize the color of the inactive time indicator on the progress bar.</x-field-desc>
  </x-field>
</x-field-group>

In addition to these options, all configuration options for the core [`rrweb.Replayer`](https://github.com/rrweb-io/rrweb/blob/master/guide.md#options-1) can be passed directly within the `props` object to customize the underlying replay engine.

## Programmatic Control (API)

The `rrweb-player` instance exposes several methods that allow for programmatic control over the playback. This is particularly useful when building a custom controller UI.

| Method Signature                                                                       | Description                                                                 |
| -------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `play(): void`                                                                         | Starts or resumes playback.                                                 |
| `pause(): void`                                                                        | Pauses playback.                                                            |
| `toggle(): void`                                                                       | Toggles the playback state between play and pause.                          |
| `goto(timeOffset: number, play?: boolean): void`                                       | Jumps to a specific time offset (in milliseconds). Optionally starts playing from there. |
| `setSpeed(speed: number): void`                                                        | Sets the playback speed.                                                    |
| `toggleSkipInactive(): void`                                                           | Toggles whether to skip periods of user inactivity.                         |
| `addEvent(event: eventWithTime): void`                                                 | Adds a new event to the events list.                                        |
| `getMetaData(): { startTime: number, endTime: number, totalTime: number }`             | Returns metadata about the session, including start, end, and total time.   |
| `getReplayer(): Replayer`                                                              | Returns the underlying `rrweb.Replayer` instance.                           |
| `getMirror(): Mirror`                                                                  | Returns the `Mirror` instance used for mapping IDs to nodes.              |
| `triggerResize(): void`                                                                | Manually triggers a resize calculation for the player.                      |
| `playRange(timeOffset: number, endTimeOffset: number, startLooping?: boolean, afterHook?: () => void): void` | Plays a specific time range within the session, with an option to loop.     |

## Listening to Player Events

To monitor the player's state, you can subscribe to events using the `addEventListener` method. This allows you to react to state changes and update your custom UI accordingly.

```javascript Listening to Events icon=logos:javascript
const playerInstance = new rrwebPlayer({
  target: document.body,
  props: {
    events,
    showController: false, // Hiding default UI
  },
});

// Fired when the player's state (e.g., Playing, Paused) changes
playerInstance.addEventListener('ui-update-player-state', (event) => {
  console.log('New player state:', event.payload);
});

// Fired continuously with the current time offset
playerInstance.addEventListener('ui-update-current-time', (event) => {
  // Useful for updating a custom time display
  const currentTime = Math.floor(event.payload / 1000); // convert ms to seconds
  console.log('Current time:', currentTime);
});

// Fired when playback is complete
playerInstance.addEventListener('finish', () => {
  console.log('Playback finished.');
});
```

Key events include:

-   **`ui-update-player-state`**: Fires when the player state changes (e.g., from `Playing` to `Paused`).
-   **`ui-update-current-time`**: Fires repeatedly with the current time offset in milliseconds.
-   **`ui-update-progress`**: Fires with the current progress as a percentage.
-   **`finish`**: Fires once the entire session has been replayed.

## Summary

The `rrweb-player` component offers a robust and configurable solution for replaying sessions. By leveraging its properties, API, and events, you can embed a full-featured player directly into your application or build a completely custom playback UI tailored to your specific needs. For more details on the recording process, see [Recording a Session](./getting-started-recording-a-session.md).