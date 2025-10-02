# Canvas Recording

By default, rrweb does not record the content of `<canvas>` elements because they are immediate-mode rendering surfaces, meaning their visual state is not retained in the DOM like other elements. To capture canvas interactions, you must explicitly enable one of the available recording methods.

This guide details the different strategies rrweb provides for recording canvas content, helping you choose the best approach for your specific use case.

## Enabling Canvas Recording and Replay

To begin recording canvas elements, you must set the `recordCanvas` option to `true` in the `rrweb.record()` configuration. Correspondingly, you need to enable `UNSAFE_replayCanvas` in the Replayer's configuration.

```javascript title="recorder.js" icon=logos:javascript
rrweb.record({
  emit(event) {
    // store the event in any way you like
  },
  recordCanvas: true,
});
```

```javascript title="replayer.js" icon=logos:javascript
const replayer = new rrweb.Replayer(events, {
  UNSAFE_replayCanvas: true,
});

replayer.play();
```

:::danger Security Warning
Enabling `UNSAFE_replayCanvas` is required for replaying canvas events. This option removes the replayer's sandbox environment to allow canvas APIs to function correctly. Be aware that this introduces a potential security risk if you are replaying sessions from untrusted sources, as it could allow malicious code to be executed.
:::

## Recording Methods

rrweb offers two primary methods for recording canvas content: intercepting individual draw calls and taking periodic image snapshots. The method used depends on the `sampling.canvas` configuration option.

### Method 1: Recording Draw Calls (Default)

This is the default method when `recordCanvas` is set to `true` without any specific canvas sampling rate. It works by intercepting and serializing the arguments of every function call made on a canvas rendering context (`2D`, `WebGL`, and `WebGL2`).

-   **How it works**: It patches the prototypes of `CanvasRenderingContext2D`, `WebGLRenderingContext`, and `WebGL2RenderingContext` to record each command (e.g., `fillRect`, `drawImage`, `gl.drawArrays`) and its arguments.
-   **Best for**: Applications with relatively simple and infrequent canvas updates, such as drawing charts or basic animations. It can be very data-efficient if the canvas does not change often.
-   **Configuration**: Simply enable `recordCanvas`.

```javascript icon=logos:javascript
rrweb.record({
  emit(event) {},
  recordCanvas: true, // Uses the draw call recording method by default
});
```

### Method 2: Image Snapshotting

This method captures the canvas content as a series of images at a specified frame rate (FPS). It is more robust for complex or high-frequency canvas updates, such as those in games or detailed animations.

-   **How it works**: At a fixed interval determined by the FPS, rrweb captures a snapshot of the canvas using `createImageBitmap` and sends it as an event. A web worker is used to process the image data, minimizing the performance impact on the main thread.
-   **Best for**: Complex WebGL applications, games, or any canvas with a high rate of change where recording individual draw calls would be too performance-intensive or generate excessive data.
-   **Configuration**: Set `sampling.canvas` to your desired frames per second.

```javascript icon=logos:javascript
rrweb.record({
  emit(event) {},
  recordCanvas: true,
  // Capture the canvas at a maximum of 15 frames per second
  sampling: {
    canvas: 15,
  },
  // Optional: Configure image format and quality for optimization
  dataURLOptions: {
    type: 'image/webp',
    quality: 0.6,
  },
});
```

## Advanced Alternative: WebRTC Streaming

For highly dynamic canvas content, such as real-time games or video editing applications, both methods above may have limitations in performance or data size. In these scenarios, you can use official plugins to stream canvas content via WebRTC.

This approach captures the canvas as a video stream, which is highly efficient for high-framerate content.

To learn more, please see the documentation for the canvas WebRTC plugins:
-   [rrweb-plugin-canvas-webrtc-record](https://github.com/rrweb-io/rrweb/tree/master/packages/plugins/rrweb-plugin-canvas-webrtc-record)
-   [rrweb-plugin-canvas-webrtc-replay](https://github.com/rrweb-io/rrweb/tree/master/packages/plugins/rrweb-plugin-canvas-webrtc-replay)

## Summary

Choosing the right canvas recording method is crucial for balancing performance, data size, and visual fidelity. Here is a summary to guide your decision:

| Method                  | `sampling.canvas` Value | Use Case                                               | Pros                                                              | Cons                                                |
| ----------------------- | ----------------------- | ------------------------------------------------------ | ----------------------------------------------------------------- | --------------------------------------------------- |
| **Draw Call Recording** | `undefined` (default)   | Simple charts, diagrams, infrequent animations.        | High fidelity, potentially small data size.                       | Can have performance overhead with complex scenes.  |
| **Image Snapshotting**  | `number` (e.g., `15`)   | Complex animations, WebGL applications, data viewers.  | Low overhead, accurately captures final visual output.            | Can generate large amounts of data.                 |
| **WebRTC Streaming**    | (Plugin-based)          | Real-time games, video applications, interactive art. | High performance, optimized for high-framerate video.             | Requires more complex setup with plugins.           |
