# Canvas Recording

By default, rrweb does not record the content of `<canvas>` elements. Since canvas content is drawn programmatically via JavaScript APIs rather than being part of the standard DOM structure, it requires special handling. rrweb provides two primary methods for capturing canvas interactions: instruction-based recording and image snapshot recording.

To begin, you must enable canvas recording in the recorder configuration.

```javascript title="Enable Canvas Recording"
rrweb.record({
  emit(event) {
    // Handle the emitted events
  },
  recordCanvas: true,
});
```

## Recording Methods

Once `recordCanvas` is enabled, you can choose between two distinct recording strategies depending on your needs for fidelity, performance, and data size.

### Method 1: Instruction-Based Recording (Default)

This is the default method when `recordCanvas: true` is set. It works by patching the canvas rendering context (`2D`, `WebGL`, and `WebGL2`) to intercept and record every drawing command (e.g., `fillRect`, `drawImage`) and property assignment. During replay, these commands are executed in the same order to reconstruct the canvas content.

-   **Pros:** High-fidelity replication of vector graphics. Can be very data-efficient if the drawing operations are simple or infrequent.
-   **Cons:** May introduce performance overhead if there are a high number of draw calls per frame. Perfect replication of complex WebGL scenes can be challenging.

To use this method, simply enable `recordCanvas`:

```javascript title="Instruction-Based Recording Configuration"
rrweb.record({
  emit(event) {},
  recordCanvas: true,
});
```

### Method 2: Image Snapshot Recording

Instead of recording individual draw calls, this method captures the canvas content as an image at a specified frequency (frames per second). This approach is ideal for complex or high-performance canvas applications, such as WebGL-based games or data visualizations, where visual accuracy is more important than capturing the underlying draw commands.

-   **Pros:** Guarantees pixel-perfect visual accuracy. Performance is predictable and decoupled from the complexity of the drawing logic.
-   **Cons:** Can generate a large amount of data, especially with large canvases or high frame rates.

You can configure the snapshot frequency using the `sampling.canvas` option. You can also optimize the data size by specifying the image format and quality with `dataURLOptions`.

```javascript title="Image Snapshot Recording Configuration"
rrweb.record({
  emit(event) {},
  recordCanvas: true,
  sampling: {
    // Capture canvas at a maximum of 15 frames per second
    canvas: 15,
  },
  // Optional: settings to control image format and quality
  dataURLOptions: {
    type: 'image/webp',
    quality: 0.8, // Value between 0 and 1
  },
});
```

## Enabling Canvas Replay

To replay canvas events, you must explicitly enable it in the replayer configuration. This is disabled by default due to important security considerations.

```javascript title="Enable Canvas Replay"
const replayer = new rrweb.Replayer(events, {
  UNSAFE_replayCanvas: true,
});

replayer.play();
```

> **Security Warning**
> Setting `UNSAFE_replayCanvas: true` will disable the replayer's iframe sandbox. The sandbox is a critical security feature that isolates the replayed session from the host page to prevent cross-site scripting (XSS) attacks. Disabling it is necessary for canvas replay because the replayer needs direct access to the canvas APIs to execute drawing commands. Only use this option if you fully trust the source of your recorded events.

## Alternative: WebRTC Streaming

For high-performance, real-time canvas recording and streaming, rrweb offers a plugin-based approach using WebRTC. This method is highly efficient but requires a more complex setup.

For more details, please refer to the documentation for the [canvas-webrtc plugins](https://github.com/rrweb-io/rrweb/tree/master/packages/plugins/rrweb-plugin-canvas-webrtc-record).