# Recording Engine

The rrweb recording engine is the core component responsible for capturing all DOM and user interaction events on a web page. By calling the `record` function, you can start a recording session and receive a stream of events that precisely describe everything happening in the user's browser. This section provides a detailed guide to the various configuration options available to tailor the recording process to your specific needs, including data privacy, performance optimization, and custom event handling.

### Basic Usage

To begin, import the `record` function from the `@rrweb/record` package and provide an `emit` function. The `emit` function is a callback that receives events as they are captured. It is your responsibility to store or transmit these events.

```javascript Basic Recording Setup icon=logos:javascript
import { record } from '@rrweb/record';

let events = [];

// Start recording
const stopFn = record({
  emit(event) {
    // Push the event to the events array
    events.push(event);
  },
});

// To stop recording, call the function returned by record()
// stopFn();
```

The `record` function returns a `stop` function that, when called, will disconnect all observers and cease recording.

## Configuration Options

The `record` function accepts a configuration object with numerous options to control the recording behavior. Below is a comprehensive list of these options.

### Core Configuration

These are the fundamental options for controlling event emission and snapshotting.

<x-field-group>
  <x-field data-name="emit" data-type="(event: T, isCheckout?: boolean) => void" data-required="true">
    <x-field-desc markdown>The callback function that receives recorded events. This is the only required option. `isCheckout` is `true` when the event is a full snapshot triggered by a checkout.</x-field-desc>
  </x-field>
  <x-field data-name="checkoutEveryNth" data-type="number" data-required="false">
    <x-field-desc markdown>Specifies how many incremental events to record before taking a new full snapshot. For example, if set to `100`, a full snapshot will be generated after every 100 incremental events.</x-field-desc>
  </x-field>
  <x-field data-name="checkoutEveryNms" data-type="number" data-required="false">
    <x-field-desc markdown>Specifies the time interval in milliseconds after which a new full snapshot should be taken. For instance, a value of `60000` (1 minute) will ensure a full snapshot is captured if the last one was more than a minute ago.</x-field-desc>
  </x-field>
</x-field-group>

### Data Masking and Privacy

rrweb provides robust features to prevent sensitive user data from being recorded. You can block elements, mask text, and selectively mask input fields.

<x-field-group>
  <x-field data-name="blockClass" data-type="string | RegExp" data-default="'rr-block'" data-required="false">
    <x-field-desc markdown>Elements with this class will be blocked. Blocked elements will be rendered as a placeholder and will not capture any user interaction. This applies to the element and all its children.</x-field-desc>
  </x-field>
  <x-field data-name="blockSelector" data-type="string" data-required="false">
    <x-field-desc markdown>A CSS selector to specify elements to block. This provides more flexibility than `blockClass`. All matching elements and their children will be blocked.</x-field-desc>
  </x-field>
  <x-field data-name="ignoreClass" data-type="string" data-default="'rr-ignore'" data-required="false">
    <x-field-desc markdown>Elements with this class will be ignored. Ignored elements will not record any mutations that occur within them. This is useful for dynamic content that is not relevant to the replay, like ads.</x-field-desc>
  </x-field>
  <x-field data-name="ignoreSelector" data-type="string" data-required="false">
    <x-field-desc markdown>A CSS selector for elements to ignore, similar to `ignoreClass`.</x-field-desc>
  </x-field>
  <x-field data-name="maskTextClass" data-type="string | RegExp" data-default="'rr-mask'" data-required="false">
    <x-field-desc markdown>Text content within elements that have this class will be replaced with asterisks (`*`).</x-field-desc>
  </x-field>
  <x-field data-name="maskTextSelector" data-type="string" data-required="false">
    <x-field-desc markdown>A CSS selector to specify text elements to mask.</x-field-desc>
  </x-field>
  <x-field data-name="maskAllInputs" data-type="boolean" data-default="false" data-required="false">
    <x-field-desc markdown>If `true`, masks the value of all input elements. By default, only `password` inputs are masked.</x-field-desc>
  </x-field>
  <x-field data-name="maskInputOptions" data-type="MaskInputOptions" data-required="false">
    <x-field-desc markdown>An object to specify which input types should be masked. If `maskAllInputs` is `true`, this option is ignored. By default, it is `{ password: true }`.</x-field-desc>
    <x-field data-name="password" data-type="boolean" data-desc="Mask password inputs."></x-field>
    <x-field data-name="text" data-type="boolean" data-desc="Mask text inputs."></x-field>
    <x-field data-name="textarea" data-type="boolean" data-desc="Mask textarea elements."></x-field>
    <x-field data-name="select" data-type="boolean" data-desc="Mask select elements."></x-field>
  </x-field>
  <x-field data-name="maskInputFn" data-type="(text: string, element: HTMLElement) => string" data-required="false">
    <x-field-desc markdown>A custom function to mask input values. This provides full control over how input data is sanitized before being recorded.</x-field-desc>
  </x-field>
  <x-field data-name="maskTextFn" data-type="(text: string, element: HTMLElement) => string" data-required="false">
    <x-field-desc markdown>A custom function to mask text content. This allows for more sophisticated masking logic than simple replacement with asterisks.</x-field-desc>
  </x-field>
</x-field-group>

### Performance and Data Optimization

These options help you manage the size of your recordings and optimize performance by sampling events and slimming down the DOM snapshot.

<x-field-group>
  <x-field data-name="sampling" data-type="object" data-required="false">
    <x-field-desc markdown>An object to configure event sampling rates. Sampling reduces the number of events recorded, which can significantly decrease the data size.</x-field-desc>
    <x-field data-name="mousemove" data-type="number | boolean" data-desc="Throttle mouse movement events. A number value specifies the minimum time in ms between events. 'true' uses a default. 'false' disables."></x-field>
    <x-field data-name="scroll" data-type="number" data-desc="Throttle scroll events to at most one event per specified number of milliseconds."></x-field>
    <x-field data-name="media" data-type="number" data-desc="Throttle media interaction events (e.g., play, pause) to at most one event per specified number of milliseconds."></x-field>
    <x-field data-name="input" data-type="string" data-desc="Defines when to record input events. 'all' records all events, while 'last' only records the final value."></x-field>
    <x-field data-name="canvas" data-type="number" data-desc="Specify the frames per second (FPS) for canvas recording."></x-field>
  </x-field>
  <x-field data-name="slimDOMOptions" data-type="SlimDOMOptions | 'all' | true" data-required="false">
    <x-field-desc markdown>Reduces the size of the DOM snapshot by excluding non-essential elements like `<script>`, comments, and certain `<meta>` tags. Setting to `true` uses a balanced preset, while `'all'` enables maximum slimming.</x-field-desc>
  </x-field>
  <x-field data-name="packFn" data-type="(event: eventWithTime) => packedEvent" data-required="false">
    <x-field-desc markdown>A function to apply custom compression to the event data before it is emitted. You will need a corresponding `unpackFn` in the replayer.</x-field-desc>
  </x-field>
</x-field-group>

### Content and Asset Handling

Control how external assets like stylesheets, images, and fonts are captured.

<x-field-group>
  <x-field data-name="inlineStylesheet" data-type="boolean" data-default="true" data-required="false">
    <x-field-desc markdown>When `true`, the content of external stylesheets is fetched and embedded into the recording. This ensures that styles are replayed accurately even if the original CSS files are no longer available.</x-field-desc>
  </x-field>
  <x-field data-name="inlineImages" data-type="boolean" data-default="false" data-required="false">
    <x-field-desc markdown>When `true`, images are converted to data URLs and embedded in the recording. This is useful for capturing images that may not be accessible during replay.</x-field-desc>
  </x-field>
  <x-field data-name="collectFonts" data-type="boolean" data-default="false" data-required="false">
    <x-field-desc markdown>When `true`, captures `@font-face` rules and font data to ensure custom fonts are rendered correctly during replay.</x-field-desc>
  </x-field>
  <x-field data-name="dataURLOptions" data-type="object" data-required="false">
    <x-field-desc markdown>An object containing options for `HTMLCanvasElement.toDataURL()`, used when `recordCanvas` or `inlineImages` is enabled. You can specify `type` and `quality`.</x-field-desc>
  </x-field>
  <x-field data-name="keepIframeSrcFn" data-type="(src: string) => boolean" data-default="() => false" data-required="false">
    <x-field-desc markdown>A predicate function that determines whether an iframe's `src` attribute should be preserved. By default, all iframe `src` attributes are removed for security.</x-field-desc>
  </x-field>
</x-field-group>

### Advanced Recording Control

Fine-tune what gets recorded and when the recording process starts.

<x-field-group>
  <x-field data-name="recordDOM" data-type="boolean" data-default="true" data-required="false">
    <x-field-desc markdown>Set to `false` to disable all DOM recording. Useful if you only want to use plugins to record custom events.</x-field-desc>
  </x-field>
  <x-field data-name="recordCanvas" data-type="boolean" data-default="false" data-required="false">
    <x-field-desc markdown>Set to `true` to enable recording of `<canvas>` elements. See the [Canvas Recording](./advanced-guides-canvas-recording.md) guide for more details.</x-field-desc>
  </x-field>
  <x-field data-name="recordCrossOriginIframes" data-type="boolean" data-default="false" data-required="false">
    <x-field-desc markdown>Enables recording of cross-origin iframes. This requires special setup in both the parent page and the embedded iframe. Refer to the [Cross-Origin Iframes](./advanced-guides-cross-origin-iframes.md) guide.</x-field-desc>
  </x-field>
  <x-field data-name="recordAfter" data-type="'DOMContentLoaded' | 'load'" data-default="'load'" data-required="false">
    <x-field-desc markdown>Specifies whether to start recording after the `DOMContentLoaded` event or the `load` event. The default is `load` to ensure all assets are loaded before the initial snapshot.</x-field-desc>
  </x-field>
  <x-field data-name="userTriggeredOnInput" data-type="boolean" data-default="false" data-required="false">
    <x-field-desc markdown>If `true`, input events will only be recorded if they are preceded by a user interaction event, such as a click or keypress. This helps filter out programmatic input changes.</x-field-desc>
  </x-field>
</x-field-group>

### Extensibility

Extend rrweb's functionality with custom hooks and plugins.

<x-field-group>
  <x-field data-name="hooks" data-type="object" data-required="false">
    <x-field-desc markdown>An object containing lifecycle hooks that allow you to intercept and modify events at various stages of the recording process (e.g., `mutation`, `beforeSnapshot`).</x-field-desc>
  </x-field>
  <x-field data-name="plugins" data-type="RecordPlugin[]" data-required="false">
    <x-field-desc markdown>An array of rrweb plugins to extend recording capabilities. For example, the [Console Plugin](./plugins-console.md) can be used to capture console log messages. See the [Using Plugins](./plugins-using-plugins.md) guide for more information.</x-field-desc>
  </x-field>
  <x-field data-name="errorHandler" data-type="(error: unknown) => void | boolean" data-required="false">
    <x-field-desc markdown>A callback function to handle errors that occur within the recorder. If the function returns `true`, the error will be re-thrown.</x-field-desc>
  </x-field>
</x-field-group>

## Static Methods

The `record` object also provides several static methods to interact with an active recording session.

### `record.addCustomEvent<T>(tag: string, payload: T)`

Inject a custom event into the recording stream. This is useful for logging application-specific state or events that are not captured automatically.

```javascript Adding a Custom Event icon=logos:javascript
import { record } from '@rrweb/record';

record({
  emit(event) { /* ... */ }
});

// Add a custom event when a user completes a purchase
record.addCustomEvent('purchase-complete', {
  productId: 'prod_123',
  price: 29.99,
});
```

### `record.takeFullSnapshot(isCheckout?: boolean)`

Manually trigger a full DOM snapshot. This is useful after significant, client-side UI changes (e.g., navigating to a new view in a single-page application) to ensure the replay is synchronized.

### `record.freezePage()`

Temporarily stops the `MutationObserver` from collecting DOM changes. This can be useful when you are about to perform a large number of DOM manipulations that you do not want to record. Call `unfreeze` on the mutation buffer when ready to resume.

### `record.mirror`

The `mirror` is an internal data structure that maintains a mapping between DOM nodes and the integer IDs assigned to them. It is exposed for advanced use cases, such as building custom plugins that need to interact with rrweb's internal representation of the DOM.