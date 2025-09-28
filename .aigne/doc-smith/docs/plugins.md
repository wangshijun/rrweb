# Plugins

The rrweb plugin API is designed to extend core functionality without increasing the size and complexity of the main library. Plugins allow you to capture additional data, such as console logs or canvas streams, and integrate custom behaviors into both the recording and replaying processes.

This page provides an overview of the plugin system. For detailed implementation guides, please refer to the following sections:

<x-cards>
  <x-card data-title="Using Plugins" data-icon="lucide:puzzle" data-href="/plugins/using-plugins">
    A general guide on how to integrate and configure plugins for both recording and replaying to extend rrweb's core capabilities.
  </x-card>
  <x-card data-title="Console Plugin" data-icon="lucide:terminal" data-href="/plugins/console">
    Learn how to use the console plugin to capture and replay browser console logs (e.g., log, warn, error) alongside user interactions.
  </x-card>
</x-cards>

## Available Official Plugins

rrweb provides a set of official plugins to handle common use cases. Each feature is typically split into a record and a replay package.

| Feature | Description | Record Package | Replay Package |
|---|---|---|---|
| Console Logs | Records and replays `console.log`, `console.warn`, etc. | `@rrweb/rrweb-plugin-console-record` | `@rrweb/rrweb-plugin-console-replay` |
| Sequential ID | Adds a sequential, incrementing ID to each event. | `@rrweb/rrweb-plugin-sequential-id-record` | `@rrweb/rrweb-plugin-sequential-id-replay` |
| Canvas WebRTC | Streams `<canvas>` animations via WebRTC for high-fidelity recording. | `@rrweb/rrweb-plugin-canvas-webrtc-record` | `@rrweb/rrweb-plugin-canvas-webrtc-replay` |

## Plugin Interface

A plugin can implement functionality for recording, replaying, or both. They must conform to the following TypeScript interfaces.

### Record Plugin Interface

A record plugin observes browser activity and uses a callback function to emit custom events.

```typescript RecordPlugin Interface icon=logos:typescript
export type RecordPlugin<TOptions = unknown> = {
  // A unique name for the plugin.
  name: string;
  // The observer function that watches for changes and emits events.
  observer: (cb: Function, options: TOptions) => listenerHandler;
  // Default options for the plugin.
  options: TOptions;
};
```

### Replay Plugin Interface

A replay plugin provides a handler that processes events during playback. It can interact with the replayer instance via the `context` argument.

```typescript ReplayPlugin Interface icon=logos:typescript
export type ReplayPlugin = {
  handler: (
    event: eventWithTime,
    isSync: boolean,
    context: { replayer: Replayer },
  ) => void;
};
```

## Example Implementation

Here are basic examples of how to create and use custom plugins.

### Record Plugin Example

This example record plugin emits a custom event with a payload every second.

```javascript Record Plugin Example icon=logos:javascript
const exampleRecordPlugin: RecordPlugin<{ foo: string }> = {
  name: 'my-scope/example@1',
  observer(cb, options) {
    const timer = setInterval(() => {
      cb({
        foo: options.foo,
        timestamp: Date.now(),
      });
    }, 1000);
    // Return a function to stop the observer.
    return () => clearInterval(timer);
  },
  options: {
    foo: 'bar',
  },
};

rrweb.record({
  emit: (event) => {
    // The emitted event will be passed here.
  },
  plugins: [exampleRecordPlugin],
});
```

When using this plugin, rrweb will generate events with a `type` of `6` (Plugin), containing the plugin's name and its custom payload.

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

This replay plugin listens for events and processes the payload from the corresponding record plugin.

```javascript Replay Plugin Example icon=logos:javascript
const exampleReplayPlugin: ReplayPlugin = {
  handler(event, isSync, context) {
    // Check if the event is a plugin event.
    if (event.type === 6 && event.data.plugin === 'my-scope/example@1') {
      // Handle the custom payload from the record plugin.
      console.log('Custom plugin event:', event.data.payload);
    }
  },
};

const replayer = new rrweb.Replayer(events, {
  plugins: [exampleReplayPlugin],
});
```

## Plugin Naming Convention

A record plugin must have a unique name, which is stored in the events it emits. To avoid naming conflicts between official plugins and user-created ones, we strongly recommend the following format:

> `scope/name@version`

For example, an official plugin might be named `rrweb/console@1`, while a custom internal plugin could be `github/pr@2`.