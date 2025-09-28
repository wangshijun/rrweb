# Console Plugin

The console plugin extends rrweb's capabilities by capturing and replaying browser console output, such as `console.log`, `console.warn`, and `console.error`. This functionality is invaluable for debugging, as it provides developers with a complete context of application logs alongside user interactions, helping to diagnose issues more efficiently.

This guide will detail the procedures for enabling and configuring the console plugin for both recording and replaying sessions.

## Recording Console Output

To begin recording console activity, you must import and add the `getRecordConsolePlugin` to the `plugins` array in your `rrweb.record` configuration.

### Basic Configuration

Here is a standard implementation to enable console recording with default settings:

```javascript Enabling Console Recording icon=logos:javascript
import rrweb from 'rrweb';
import { getRecordConsolePlugin } from '@rrweb/rrweb-plugin-console-record';

rrweb.record({
  emit(event) {
    // Note: To avoid infinite loops, do not use console.log directly here.
    const originalConsoleLog = console.log['__rrweb_original__'] || console.log;
    originalConsoleLog(event);
  },
  plugins: [
    // Enable the console record plugin with default options
    getRecordConsolePlugin(),
  ],
});
```

> **Important:** When defining the `emit` function, you must not call `console.log` or other console methods directly. Doing so will create an infinite loop, as the plugin will capture its own output. Instead, access the original console method via `console.log['__rrweb_original__']` to safely log the events.

### Advanced Configuration

You can customize the console recording behavior by passing an options object to `getRecordConsolePlugin`.

```javascript Customizing Console Recording icon=logos:javascript
import rrweb from 'rrweb';
import { getRecordConsolePlugin } from '@rrweb/rrweb-plugin-console-record';

rrweb.record({
  emit(event) {
    const originalConsoleLog = console.log['__rrweb_original__'] || console.log;
    originalConsoleLog(event);
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

#### Recording Options

The following options are available to configure the recording plugin:

<x-field-group>
  <x-field data-name="level" data-type="string[]" data-default='["assert", "clear", "count", ...]' data-required="false">
    <x-field-desc markdown>An array of console method names to record. By default, it includes all standard console levels. You can provide a smaller array to record only specific levels, e.g., `['warn', 'error']`.</x-field-desc>
  </x-field>
  <x-field data-name="lengthThreshold" data-type="number" data-default="1000" data-required="false">
    <x-field-desc markdown>The maximum number of console records to capture in a session. A warning is emitted when this threshold is reached.</x-field-desc>
  </x-field>
  <x-field data-name="stringifyOptions" data-type="object" data-required="false">
    <x-field-desc markdown>Configuration for serializing objects logged to the console. This helps manage the size of recorded events.</x-field-desc>
    <x-field data-name="stringLengthLimit" data-type="number" data-required="false" data-desc="Limits the string length of a single value."></x-field>
    <x-field data-name="numOfKeysLimit" data-type="number" data-default="50" data-required="false" data-desc="Limits the number of keys in an object before it is serialized as a string representation (e.g., '[Object]')."></x-field>
    <x-field data-name="depthOfLimit" data-type="number" data-default="4" data-required="false" data-desc="Limits the nesting depth for object serialization."></x-field>
  </x-field>
  <x-field data-name="logger" data-type="object | 'console'" data-default="window.console" data-required="false">
    <x-field-desc markdown>The console object to record. This allows you to capture logs from other execution environments, such as an iframe.</x-field-desc>
  </x-field>
</x-field-group>

## Replaying Console Output

If a recording contains console events, they will be replayed automatically when using the `rrweb.Replayer`. To configure the replay behavior, add the `getReplayConsolePlugin` to the replayer's `plugins` array.

```javascript Replaying Console Logs icon=logos:javascript
import rrweb from 'rrweb';
import { getReplayConsolePlugin } from '@rrweb/rrweb-plugin-console-replay';

// Assume 'events' is an array of recorded rrweb events
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

The following options are available for the replay plugin:

<x-field-group>
  <x-field data-name="level" data-type="string[]" data-default='["assert", "clear", "count", ...]' data-required="false">
    <x-field-desc markdown>Filters which console levels are replayed. Only logs matching the levels in this array will be output to the browser's console during replay.</x-field-desc>
  </x-field>
  <x-field data-name="replayLogger" data-type="ReplayLogger" data-required="false">
    <x-field-desc markdown>Allows you to provide a custom logger object to handle the replayed console messages. This is useful for displaying logs in a custom UI component instead of the browser console.</x-field-desc>
  </x-field>
</x-field-group>

---

By following these steps, you can effectively integrate console logging into your rrweb sessions. For more information on extending rrweb, please see the general guide on [Using Plugins](./plugins-using-plugins.md).