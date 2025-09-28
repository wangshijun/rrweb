# Recording Engine

The `rrweb.record` function is the core of rrweb's recording capabilities. It attaches listeners to the DOM and captures a comprehensive stream of events, including user interactions, DOM mutations, and other browser state changes. This engine is highly configurable, allowing you to tailor the recording process to meet specific needs for privacy, performance, and feature scope.

Once you have captured a session, you can play it back using the [Player Component](./core-packages-player-component.md).

## Basic Usage

To begin recording, you must call the `record` function and provide an `emit` callback. This function is responsible for receiving the recorded events, which you can then store or send to a server.

```javascript Basic Recording Setup icon=logos:javascript
import { record } from 'rrweb';

// This function will be called every time a new event is captured.
const events = [];

// Start recording
const stopFn = record({
  emit(event) {
    // Push the event to the events array
    events.push(event);
  },
});

// To stop recording, call the function returned by record()
document.getElementById('stop-button').addEventListener('click', () => {
  stopFn();
  // Now you can save the 'events' array to a file or send it to a server.
  console.log('Recording stopped. Total events:', events.length);
});
```

The `record` function returns a `listenerHandler` function that, when called, will disconnect all observers and stop the recording process.

## Configuration Options

The `record` function accepts a single options object to customize its behavior. Below is a detailed list of all available options.

### Core Configuration

These options control the fundamental behavior of the recording engine, such as how events are emitted and when full snapshots are taken.

<x-field-group>
  <x-field data-name="emit" data-type="(event, isCheckout?) => void" data-required="true" data-desc="The callback function that receives recorded events. This is the only required option."></x-field>
  <x-field data-name="checkoutEveryNth" data-type="number" data-desc="Specifies how often a full snapshot should be taken based on the number of incremental events. For example, a value of 100 will trigger a full snapshot every 100 events."></x-field>
  <x-field data-name="checkoutEveryNms" data-type="number" data-desc="Specifies how often a full snapshot should be taken based on a time interval (in milliseconds). For example, a value of 60000 will trigger a full snapshot if the last one was more than a minute ago."></x-field>
</x-field-group>

### Privacy and Data Masking

rrweb provides robust options to prevent sensitive user data from being recorded. You can block entire elements, mask text content, or ignore interactions on specific inputs.

<x-field-group>
  <x-field data-name="blockClass" data-type="string | RegExp" data-default="'rr-block'" data-desc="Elements with this class will be recorded as a placeholder and their content will not be captured."></x-field>
  <x-field data-name="blockSelector" data-type="string" data-desc="A CSS selector for elements to block. This provides more flexibility than `blockClass`."></x-field>
  <x-field data-name="ignoreClass" data-type="string" data-default="'rr-ignore'" data-desc="Input events on elements with this class will not be recorded."></x-field>
  <x-field data-name="ignoreSelector" data-type="string" data-desc="A CSS selector for elements whose input events should be ignored."></x-field>
  <x-field data-name="maskTextClass" data-type="string | RegExp" data-default="'rr-mask'" data-desc="Text content within elements having this class will be replaced with asterisks (*)."></x-field>
  <x-field data-name="maskTextSelector" data-type="string" data-desc="A CSS selector for elements whose text content should be masked."></x-field>
  <x-field data-name="maskAllInputs" data-type="boolean" data-default="false" data-desc="If true, masks the value of all input elements."></x-field>
  <x-field data-name="maskInputOptions" data-type="object">
    <x-field-desc markdown>An object to specify which input types should be masked. By default, only passwords are masked. `maskAllInputs: true` is a shorthand for enabling all of these.</x-field-desc>
    <x-field data-name="color" data-type="boolean"></x-field>
    <x-field data-name="date" data-type="boolean"></x-field>
    <x-field data-name="datetime-local" data-type="boolean"></x-field>
    <x-field data-name="email" data-type="boolean"></x-field>
    <x-field data-name="month" data-type="boolean"></x-field>
    <x-field data-name="number" data-type="boolean"></x-field>
    <x-field data-name="range" data-type="boolean"></x-field>
    <x-field data-name="search" data-type="boolean"></x-field>
    <x-field data-name="tel" data-type="boolean"></x-field>
    <x-field data-name="text" data-type="boolean"></x-field>
    <x-field data-name="time" data-type="boolean"></x-field>
    <x-field data-name="url" data-type="boolean"></x-field>
    <x-field data-name="week" data-type="boolean"></x-field>
    <x-field data-name="textarea" data-type="boolean"></x-field>
    <x-field data-name="select" data-type="boolean"></x-field>
    <x-field data-name="password" data-type="boolean" data-default="true"></x-field>
  </x-field>
  <x-field data-name="maskTextFn" data-type="(text: string) => string" data-desc="A function to provide custom logic for masking text content."></x-field>
  <x-field data-name="maskInputFn" data-type="(text: string, element: HTMLElement) => string" data-desc="A function to provide custom logic for masking input values."></x-field>
</x-field-group>

### Performance and Optimization

These options help you control the volume of data being generated, which is critical for performance and storage.

<x-field-group>
  <x-field data-name="sampling" data-type="object" data-desc="Defines sampling strategies for various event types to reduce data volume.">
    <x-field data-name="mousemove" data-type="number | [number, number]" data-desc="Throttle mouse movement events. A single number sets the time interval (ms). An array `[rate, max]` sets a sample rate and max number of events."></x-field>
    <x-field data-name="scroll" data-type="number" data-desc="Throttle scroll events to at most one every `n` milliseconds."></x-field>
    <x-field data-name="input" data-type="string | number" data-desc="Set to `'last'` to only record the final value of an input within a given timeframe. A number throttles the events."></x-field>
    <x-field data-name="canvas" data-type="number" data-desc="Throttle canvas mutation events. `0` means manual snapshot, `1` means `requestAnimationFrame`, and `n > 1` means a delay of `n` ms."></x-field>
  </x-field>
  <x-field data-name="slimDOMOptions" data-type="object | 'all' | true" data-desc="Strips out non-essential elements from the DOM snapshot to reduce its size. Set to `true` for a balanced set of optimizations or `'all'` for maximum reduction.">
    <x-field data-name="script" data-type="boolean" data-default="true"></x-field>
    <x-field data-name="comment" data-type="boolean" data-default="true"></x-field>
    <x-field data-name="headFavicon" data-type="boolean" data-default="true"></x-field>
    <x-field data-name="headWhitespace" data-type="boolean" data-default="true"></x-field>
    <x-field data-name="headMetaSocial" data-type="boolean" data-default="true"></x-field>
    <x-field data-name="headMetaRobots" data-type="boolean" data-default="true"></x-field>
    <x-field data-name="headMetaHttpEquiv" data-type="boolean" data-default="true"></x-field>
    <x-field data-name="headMetaVerification" data-type="boolean" data-default="true"></x-field>
  </x-field>
  <x-field data-name="packFn" data-type="(event) => packedEvent" data-desc="A function to apply custom compression logic to event data before it is emitted."></x-field>
</x-field-group>

### Feature Toggles

Enable or disable specific recording features.

<x-field-group>
  <x-field data-name="recordDOM" data-type="boolean" data-default="true" data-desc="Set to `false` to disable all DOM recording."></x-field>
  <x-field data-name="recordCanvas" data-type="boolean" data-default="false" data-desc="Enable recording of `<canvas>` elements."></x-field>
  <x-field data-name="recordCrossOriginIframes" data-type="boolean" data-default="false" data-desc="Enable recording of content inside cross-origin iframes. This requires special setup on both the parent and child frames."></x-field>
  <x-field data-name="inlineStylesheet" data-type="boolean" data-default="true" data-desc="Determines whether external CSS stylesheets are inlined into the recording. Disabling this can reduce data size but may cause rendering issues during replay if the original stylesheets are unavailable."></x-field>
  <x-field data-name="inlineImages" data-type="boolean" data-default="false" data-desc="Embed images as data URLs in the recording. This increases data size but ensures images are available during replay."></x-field>
  <x-field data-name="collectFonts" data-type="boolean" data-default="false" data-desc="Enable collection of `@font-face` rules to ensure custom fonts are rendered correctly during replay."></x-field>
  <x-field data-name="userTriggeredOnInput" data-type="boolean" data-default="false" data-desc="If true, `input` events are only recorded when triggered by a user interaction."></x-field>
</x-field-group>

### Advanced Customization

For more complex scenarios, you can use hooks, plugins, and custom functions.

<x-field-group>
  <x-field data-name="hooks" data-type="object" data-desc="Lifecycle hooks to intercept and modify behavior at different stages of the recording process (e.g., `mutation`, `mousemove`)."></x-field>
  <x-field data-name="plugins" data-type="RecordPlugin[]" data-desc="An array of rrweb plugins to extend core functionality, such as capturing console logs or canvas interactions."></x-field>
  <x-field data-name="keepIframeSrcFn" data-type="(src: string) => boolean" data-default="() => false" data-desc="A function to determine whether an iframe's `src` attribute should be preserved in the recording."></x-field>
  <x-field data-name="ignoreCSSAttributes" data-type="Set<string>" data-desc="A set of CSS attribute names to ignore when recording style changes."></x-field>
  <x-field data-name="errorHandler" data-type="(error) => void" data-desc="A callback function to handle any internal errors that occur during recording."></x-field>
</x-field-group>

## Static Methods

The `record` object also exposes several static methods for controlling the recording session programmatically.

### `record.addCustomEvent<T>(tag, payload)`

Injects a custom event into the event stream. This is useful for logging application-specific state or actions that are not automatically captured by rrweb.

<x-field-group>
  <x-field data-name="tag" data-type="string" data-required="true" data-desc="A string identifier for your custom event."></x-field>
  <x-field data-name="payload" data-type="T" data-required="true" data-desc="The data associated with the event. This must be serializable."></x-field>
</x-field-group>

```javascript Adding a Custom Event icon=logos:javascript
import { record } from 'rrweb';

record({
  emit(event) { /* ... */ }
});

// Example: Log when a user completes a purchase
record.addCustomEvent('purchase-complete', {
  productId: 'item-123',
  price: 29.99,
  timestamp: Date.now(),
});
```

### `record.takeFullSnapshot(isCheckout?)`

Manually triggers a full snapshot of the current DOM state. This can be useful to establish a new baseline after significant application state changes.

<x-field data-name="isCheckout" data-type="boolean" data-desc="If true, this snapshot will be treated as a checkout point, similar to `checkoutEveryNth` and `checkoutEveryNms`."></x-field>

### `record.freezePage()`

Pauses the observation of DOM mutations. While frozen, user-initiated events like clicks or inputs will still be captured. When the next such event occurs, all buffered mutations will be processed and emitted before the user-initiated event.

This is useful for scenarios where you are performing a large number of DOM manipulations and want to avoid recording each intermediate step.

## Summary

The recording engine is a powerful and flexible tool for capturing user sessions. By leveraging its extensive configuration options, you can balance detailed recording with user privacy and performance considerations. 

For more advanced use cases, consider exploring the following:

<x-cards>
  <x-card data-title="Player Component" data-href="/core-packages/player-component" data-icon="lucide:play-circle">
    Learn how to replay the events captured by the recording engine.
  </x-card>
  <x-card data-title="Performance and Storage" data-href="/advanced-guides/performance-and-storage" data-icon="lucide:database">
    Discover advanced strategies for optimizing the size and performance of your recordings.
  </x-card>
</x-cards>