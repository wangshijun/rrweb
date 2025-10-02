# Plugins

The rrweb plugin API provides a powerful way to extend the core functionality of rrweb without increasing the complexity and bundle size of the main library. Plugins allow you to capture additional custom data during a recording session and process that data during replay, enabling features tailored to your specific needs.

This section gives a high-level overview of the plugin system, its interfaces, and how to create your own plugins. For detailed instructions on integrating existing plugins, please see the following guides:

<x-cards>
  <x-card data-title="Using Plugins" data-href="/plugins/using-plugins" data-icon="lucide:puzzle">
    A general guide on how to integrate and configure plugins for both recording and replaying.
  </x-card>
  <x-card data-title="Console Plugin" data-href="/plugins/console" data-icon="lucide:terminal">
    Learn how to capture and replay browser console logs alongside user interactions.
  </x-card>
</x-cards>

## Available Official Plugins

rrweb offers several official plugins to handle common use cases. These are available as separate packages.

| Plugin Package                                | Description                                               |
| --------------------------------------------- | --------------------------------------------------------- |
| `@rrweb/rrweb-plugin-console-record`          | Records browser console activity.                         |
| `@rrweb/rrweb-plugin-console-replay`          | Replays browser console activity.                         |
| `@rrweb/rrweb-plugin-sequential-id-record`    | Adds a sequential ID to each recorded event.              |
| `@rrweb/rrweb-plugin-sequential-id-replay`    | Processes events with sequential IDs during replay.       |
| `@rrweb/rrweb-plugin-canvas-webrtc-record`    | Records `<canvas>` animations by streaming via WebRTC.    |
| `@rrweb/rrweb-plugin-canvas-webrtc-replay`    | Replays `<canvas>` animations streamed via WebRTC.        |

## Plugin API Interfaces

Plugins can be implemented for the recording process, the replaying process, or both. Each has a distinct interface.

### Record Plugin Interface

A record plugin observes application state and emits custom events.

```typescript RecordPlugin Interface
export type RecordPlugin<TOptions = unknown> = {
  name: string;
  observer: (cb: Function, options: TOptions) => listenerHandler;
  options: TOptions;
};
```

<x-field-group>
  <x-field data-name="name" data-type="string" data-required="true" data-desc="A unique name for the plugin. This is stored in the event data."></x-field>
  <x-field data-name="observer" data-type="function" data-required="true" data-desc="A function that sets up the observation logic. It receives a callback `cb` to emit data and the plugin's `options`. It must return a function to stop the observation."></x-field>
  <x-field data-name="options" data-type="object" data-required="true" data-desc="A configuration object for the plugin."></x-field>
</x-field-group>

### Replay Plugin Interface

A replay plugin listens for events during playback and can interact with the replayer.

```typescript ReplayPlugin Interface
export type ReplayPlugin = {
  handler: (
    event: eventWithTime,
    isSync: boolean,
    context: { replayer: Replayer },
  ) => void;
};
```

<x-field data-name="handler" data-type="function" data-required="true">
  <x-field-desc markdown>A function that is called for each event during replay. It receives the `event`, a flag `isSync` indicating if the event is part of the initial synchronous setup, and a `context` object containing the `replayer` instance.</x-field-desc>
</x-field>

## Usage Examples

Here are practical examples of how to create and use custom plugins.

### Record Plugin Example

This example creates a simple record plugin that emits a custom event with a timestamp every second.

```typescript Creating a Record Plugin icon=logos:typescript
const exampleRecordPlugin: RecordPlugin<{ foo: string }> = {
  name: 'my-scope/example@1',
  observer(cb, options) {
    const timer = setInterval(() => {
      cb({
        foo: options.foo,
        timestamp: Date.now(),
      });
    }, 1000);
    // Return a function to stop the timer
    return () => clearInterval(timer);
  },
  options: {
    foo: 'bar',
  },
};

// To use the plugin, pass it into the record options
rrweb.record({
  emit: (event) => {
    // The custom event will be emitted here
  },
  plugins: [exampleRecordPlugin],
});
```

The plugin will emit events with a `type` of `6` (Plugin). The event structure is as follows:

```json Emitted Plugin Event icon=mdi:code-json
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

### Replay Plugin Example

This replay plugin listens for events and specifically handles the data from our `my-scope/example@1` plugin.

```typescript Creating a Replay Plugin icon=logos:typescript
const exampleReplayPlugin: ReplayPlugin = {
  handler(event, isSync, context) {
    // Check if the event is a plugin event
    if (event.type === 6 && event.data.plugin === 'my-scope/example@1') {
      // Access the custom payload
      const payload = event.data.payload;
      console.log('Handling custom plugin event:', payload);
      // You can also interact with the replayer via context.replayer
    }
  },
};

// To use the plugin, pass it into the Replayer options
const replayer = new rrweb.Replayer(events, {
  plugins: [exampleReplayPlugin],
});
```

## Naming Convention

To prevent naming conflicts between official plugins and custom plugins created by users, it is strongly recommended to adopt a standardized naming convention for the `name` property.

The recommended format is:

```
scope/name@version
```

For example, an official plugin might be named `rrweb/console@1`, while a custom plugin could be `my-company/feature-tracker@2`.