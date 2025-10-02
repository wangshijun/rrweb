# Console Plugin

The console plugin for rrweb allows you to capture and replay browser console activity, such as `console.log`, `console.warn`, and `console.error` messages, alongside standard user interactions. This provides developers with a richer context for debugging by showing what was happening in the console at the time of an issue.

This functionality has been available since version 1.0.0 and requires separate plugin configurations for recording and replaying.

## Recording Console Logs

To capture console output, you need to add the `getRecordConsolePlugin` to the `rrweb.record` configuration.

### Default Configuration

Here is a basic setup to enable console recording with default options:

```javascript Enabling Console Recording icon=logos:javascript
import rrweb from 'rrweb';
import { getRecordConsolePlugin } from '@rrweb/rrweb-plugin-console-record';

rrweb.record({
  emit: function emit(event) {
    // Note: To avoid a RangeError, use the original console.log.
    const originalConsoleLog = console.log['__rrweb_original__'] 
      ? console.log['__rrweb_original__'] 
      : console.log;
    originalConsoleLog(event);
  },
  // Add the console record plugin with default options
  plugins: [getRecordConsolePlugin()],
});
```

> **Important:** When processing recorded events within the `emit` function, you must not use the standard `console.log` (or other console methods) to output the event data. Doing so will cause the plugin to capture its own output, leading to an infinite loop and a `RangeError: Maximum call stack size exceeded`. Always access the original console method via the `__rrweb_original__` property, as shown in the example.

### Custom Configuration

You can customize the recording behavior by passing an options object to the `getRecordConsolePlugin`.

```javascript Custom Console Recording Options icon=logos:javascript
import rrweb from 'rrweb';
import { getRecordConsolePlugin } from '@rrweb/rrweb-plugin-console-record';

rrweb.record({
  emit: (event) => {
    // Event processing logic...
  },
  plugins: [
    getRecordConsolePlugin({
      level: ['info', 'log', 'warn', 'error'],
      lengthThreshold: 10000,
      stringifyOptions: {
        stringLengthLimit: 1000,
        numOfKeysLimit: 100,
        depthOfLimit: 1,
      },
      logger: window.console,
    }),
  ],
});
```

### Recording Options

The following options are available to configure the console recording plugin.

<x-field-group>
  <x-field data-name="level" data-type="string[]">
    <x-field-desc markdown>An array of console method names to record. By default, it includes all standard console levels: `assert`, `clear`, `count`, `countReset`, `debug`, `dir`, `dirxml`, `error`, `group`, `groupCollapsed`, `groupEnd`, `info`, `log`, `table`, `time`, `timeEnd`, `timeLog`, `trace`, and `warn`.</x-field-desc>
  </x-field>
  <x-field data-name="lengthThreshold" data-type="number" data-default="1000">
    <x-field-desc markdown>The maximum number of console records to capture in a session. A warning is emitted when this threshold is reached.</x-field-desc>
  </x-field>
  <x-field data-name="stringifyOptions" data-type="object">
    <x-field-desc markdown>Provides fine-grained control over how JavaScript objects are stringified to manage the size of the event payload.</x-field-desc>
    <x-field data-name="stringLengthLimit" data-type="number" data-desc="Limits the string length of a single value."></x-field>
    <x-field data-name="numOfKeysLimit" data-type="number" data-default="50" data-desc="Limits the number of keys in an object. If an object exceeds this limit, its name is saved instead of its contents."></x-field>
    <x-field data-name="depthOfLimit" data-type="number" data-default="4" data-desc="Limits the nesting depth for objects."></x-field>
  </x-field>
  <x-field data-name="logger" data-type="object" data-default="window.console">
    <x-field-desc markdown>The console object to record. This allows you to target a console from a different execution environment if needed.</x-field-desc>
  </x-field>
</x-field-group>

## Replaying Console Logs

If the recorded session events include console data, you can use the `getReplayConsolePlugin` to automatically play them back in the browser's console during replay.

### Enabling the Plugin

Add the plugin to the `rrweb.Replayer` configuration. It will automatically detect and handle console events.

```javascript Enabling Console Replay icon=logos:javascript
import rrweb from 'rrweb';
import { getReplayConsolePlugin } from '@rrweb/rrweb-plugin-console-replay';

// Assuming 'events' is an array of rrweb events
const replayer = new rrweb.Replayer(events, {
  plugins: [
    getReplayConsolePlugin({
      level: ['info', 'log', 'warn', 'error'],
    }),
  ],
});

replayer.play();
```

### Replay Options

The following options are available for the replay plugin.

<x-field-group>
  <x-field data-name="level" data-type="string[]">
    <x-field-desc markdown>An array specifying which console levels to replay. By default, it includes all standard console levels.</x-field-desc>
  </x-field>
  <x-field data-name="replayLogger" data-type="object">
    <x-field-desc markdown>Provides a custom logger object to handle the replayed console messages. This is useful if you want to display the logs in a simulated console UI within your application instead of the actual browser console.</x-field-desc>
  </x-field>
</x-field-group>

---

This guide covers the essentials of capturing and replaying console output. For more general information on how to integrate plugins, please see the [Using Plugins](./plugins-using-plugins.md) guide.