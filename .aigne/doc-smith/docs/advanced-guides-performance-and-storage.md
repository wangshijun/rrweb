# Performance and Storage

Session recordings with rrweb can sometimes generate a significant amount of data, especially for applications with high user activity or complex interfaces. Managing this data volume is crucial for both performance and storage efficiency. This guide provides several practical strategies to optimize the size of your rrweb recordings.

We will cover the following optimization techniques:

- **Event Sampling**: Reducing the frequency of certain recorded events.
- **Blocking DOM Elements**: Excluding specific parts of the UI from the recording.
- **Data Compression**: Compressing event data in real-time or as a batch process.
- **Data Deduplication**: Removing redundant data, such as repeated CSS styles, across sessions.

## Event Sampling

One of the most effective ways to reduce data size is to sample events. The `sampling` configuration option allows you to either disable certain types of events entirely or limit their emission frequency.

### General Event Sampling

You can configure sampling for mouse movements, mouse interactions, scrolling, media interactions, and input events.

```javascript Configuring Event Sampling icon=logos:javascript
rrweb.record({
  emit(event) {
    // store the event
  },
  sampling: {
    // Disable mouse movement recording
    mousemove: false,
    // Disable all mouse interaction recording
    mouseInteraction: false,
    // Emit a scroll event at most once every 150ms
    scroll: 150, 
    // Emit a media interaction event at most once every 800ms
    media: 800,
    // For multiple characters typed in a short time, only record the final input value
    input: 'last', 
  },
});
```

### Fine-Grained Mouse Interaction Sampling

If you need more control over which mouse interactions are recorded, you can provide a detailed object for the `mouseInteraction` option.

```javascript Fine-Grained Mouse Interaction Sampling icon=logos:javascript
rrweb.record({
  emit(event) {
    // store the event
  },
  sampling: {
    mouseInteraction: {
      MouseUp: false,
      MouseDown: false,
      Click: true,
      ContextMenu: false,
      DblClick: true,
      Focus: false,
      Blur: false,
      TouchStart: false,
      TouchEnd: false,
    },
  },
});
```

## Blocking DOM Elements

Certain parts of your application may generate a large number of mutations without being critical to understanding the user's session. You can prevent rrweb from recording these elements by adding a specific block class.

Common sources of high event volume include:

-   Infinitely scrolling lists
-   Complex SVG animations
-   Elements with JavaScript-controlled animations
-   Canvas-based animations

By strategically blocking these elements, you can significantly reduce the recording's size and complexity.

## Data Compression

Compression is another powerful technique for minimizing storage requirements. You can apply compression either at the event level during recording or to the entire session data afterward.

### Event-Level Compression with `packFn`

rrweb provides the `@rrweb/packer` package, which uses `fflate` to compress each event as it's captured. To use it, you must provide the `pack` function during recording and the `unpack` function during replay.

**Recording with Compression**

```javascript Recording with Compression icon=logos:javascript
import { pack } from '@rrweb/packer';

rrweb.record({
  emit(event) {
    // The 'event' is now a compressed string.
    // Send it to your backend for storage.
  },
  packFn: pack,
});
```

**Replaying Compressed Events**

```javascript Replaying Compressed Events icon=logos:javascript
import { unpack } from '@rrweb/packer';

// 'events' is an array of compressed strings from your backend.
const replayer = new rrweb.Replayer(events, {
  unpackFn: unpack,
});
```

### Full Session Compression

While event-level compression is convenient, you can often achieve a higher compression ratio by compressing the entire session's data at once. This approach is best implemented on your backend after receiving all events for a session. Standard compression algorithms like deflate or zlib are highly effective for this purpose, as they can leverage the repetitive nature of the entire event stream.

## Data Deduplication

To accurately replay user interactions like hover effects, rrweb inlines CSS styles directly into the recorded events. Across many sessions, this can lead to a large amount of duplicated style data. 

A more advanced optimization strategy is to implement deduplication on your backend. This involves iterating through the events, extracting the CSS styles, and storing only a single, canonical copy. The same principle can be applied to full DOM snapshots, which may be very similar across different user sessions.

By implementing these strategies, you can effectively manage the performance and storage footprint of your rrweb recordings, ensuring the system remains scalable and efficient.