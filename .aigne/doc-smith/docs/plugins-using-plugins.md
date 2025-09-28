# Using Plugins

The rrweb plugin API is designed to extend the core functionality of rrweb without increasing its size or complexity. Plugins allow you to capture and replay additional types of data, such as console logs or canvas animations, by integrating seamlessly into the recording and replaying processes.

This guide provides an overview of how to use existing plugins and the basic structure for creating your own.

## Available Plugins

Several official plugins are available to extend rrweb's capabilities. These are distributed as separate packages:

| Plugin Package | Description | Type |
| --- | --- | --- |
| `@rrweb/rrweb-plugin-console-record` | Records browser console output (`log`, `warn`, `error`, etc.). | Record |
| `@rrweb/rrweb-plugin-console-replay` | Replays the captured console logs during session playback. | Replay |
| `@rrweb/rrweb-plugin-sequential-id-record` | Adds a sequential ID to each recorded event. | Record |
| `@rrweb/rrweb-plugin-sequential-id-replay` | Handles events with sequential IDs during replay. | Replay |
| `@rrweb/rrweb-plugin-canvas-webrtc-record` | Streams `<canvas>` element changes in real-time using WebRTC. | Record |
| `@rrweb/rrweb-plugin-canvas-webrtc-replay` | Replays the streamed `<canvas>` content via WebRTC. | Replay |

For more detailed information on specific plugins, please refer to their individual documentation, such as the [Console Plugin](./plugins-console.md) guide.

## Integrating Plugins

Plugins are integrated by passing them into the configuration options for recording and replaying.

### For Recording

To use a plugin during a recording session, pass an instance of it in the `plugins` array of the `rrweb.record` options.

```javascript icon=logos:javascript title="recorder.js"
// Import the core library and the desired plugin
import rrweb from 'rrweb';
import { rrwebConsoleRecord } from '@rrweb/rrweb-plugin-console-record';

// Start recording with the console record plugin enabled
const stopFn = rrweb.record({
  emit(event) {
    // The `event` object will now include custom events from the plugin
    console.log(event);
  },
  plugins: [
    rrwebConsoleRecord(),
    // You can add more recording plugins here
  ],
});
```

A recording plugin works by observing specific activities and using a callback function to `emit` custom events. These events are standardized with `type: 6` (the `Plugin` event type) and contain the plugin's unique name and its data payload.

Here is an example of an event emitted by a custom plugin:

```json
{
  "type": 6,
  "data": {
    "plugin": "my-scope/example@1",
    "payload": {
      "foo": "bar",
      "timestamp": 1624693882345
    }
  },
  "timestamp": 1624693882345
}
```

### For Replaying

Similarly, to use a replay plugin, pass it into the `plugins` array in the `rrweb.Replayer` constructor's configuration.

```javascript icon=logos:javascript title="replayer.js"
// Import the replayer and the corresponding replay plugin
import { Replayer } from 'rrweb';
import { rrwebConsoleReplay } from '@rrweb/rrweb-plugin-console-replay';

// Assume `events` is an array of rrweb events captured with the record plugin
const replayer = new Replayer(events, {
  plugins: [
    rrwebConsoleReplay(),
    // You can add more replay plugins here
  ],
});

replayer.play();
```

The replay plugin's `handler` function will be called for each event during playback. It can identify custom events by checking the `event.type` and the `event.data.plugin` name, and then execute logic to visualize the data. It also receives access to the replayer instance via the `context` argument, allowing for more complex interactions.

## Plugin Development

If you need to extend rrweb with custom functionality, you can create your own plugins.

### Record Plugin Interface

A record plugin is an object that defines how to observe and capture data.

<x-field-group>
  <x-field data-name="name" data-type="string" data-required="true">
    <x-field-desc markdown>A unique identifier for the plugin. It's crucial to follow a proper [naming convention](#plugin-naming-convention) to avoid conflicts.</x-field-desc>
  </x-field>
  <x-field data-name="observer" data-type="(cb: Function, options: TOptions) => listenerHandler" data-required="true">
    <x-field-desc markdown>A function that sets up the observation logic (e.g., `setInterval`, `MutationObserver`). It receives a callback `cb` to emit custom events and the plugin's `options`. It must return a `listenerHandler` function to tear down the observer when the recording stops.</x-field-desc>
  </x-field>
  <x-field data-name="options" data-type="TOptions" data-required="true">
    <x-field-desc markdown>A configuration object containing any options the plugin needs to operate.</x-field-desc>
  </x-field>
</x-field-group>

### Replay Plugin Interface

A replay plugin is an object with handlers that hook into the replayer's lifecycle.

<x-field-group>
  <x-field data-name="handler" data-type="(event, isSync, context) => void" data-required="false">
    <x-field-desc markdown>The primary function that processes events during playback. It's called for every event in the session. You can filter for your plugin's custom events and execute replay logic.</x-field-desc>
  </x-field>
  <x-field data-name="onBuild" data-type="(node, context) => void" data-required="false">
    <x-field-desc markdown>A callback that fires whenever a DOM node is built or rebuilt by the replayer. Useful for attaching custom logic to specific elements.</x-field-desc>
  </x-field>
  <x-field data-name="getMirror" data-type="(mirrors) => void" data-required="false">
    <x-field-desc markdown>A function that provides access to the replayer's internal `nodeMirror`, which maps element IDs to actual DOM nodes.</x-field-desc>
  </x-field>
</x-field-group>

## Plugin Naming Convention

To prevent naming conflicts between official plugins and custom plugins developed by the community, we strongly recommend adopting the following format for your plugin's `name` field:

**`scope/name@version`**

-   **scope**: A unique identifier, such as your organization or GitHub username (e.g., `rrweb`, `my-company`).
-   **name**: The name of the plugin (e.g., `console`, `feature-tracker`).
-   **version**: The version of the plugin's data format (e.g., `@1`, `@2`).

For example, `rrweb/console@1` is the name for the official console plugin, version 1. A custom plugin might be named `my-org/jira-integration@1`.