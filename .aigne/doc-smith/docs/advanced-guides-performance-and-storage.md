# Performance and Storage

In applications with high user activity or complex user interfaces, rrweb can generate a significant amount of data. Managing this data is crucial for both performance and storage costs. This guide provides several practical strategies to optimize the size of your session recordings.

We will cover the following optimization techniques:

- **Event Sampling**: Reducing the frequency or disabling certain types of events.
- **DOM Element Blocking**: Excluding specific parts of the UI from being recorded.
- **Data Compression**: Shrinking the size of recorded event data.
- **Deduplication**: Removing redundant data, such as inline CSS, across sessions.

## Event Sampling

One of the most effective ways to reduce data volume is to sample events. You can configure rrweb to ignore certain high-frequency events or limit how often they are captured. This is configured via the `sampling` option in `rrweb.record()`.

Common strategies include disabling mouse movement tracking or throttling scroll and media events.

```javascript Event Throttling and Disabling icon=logos:javascript
rrweb.record({
  emit(event) {
    // ... send event to your backend
  },
  sampling: {
    // Disable mouse movement recording entirely.
    mousemove: false,
    // Disable all mouse interaction events.
    mouseInteraction: false,
    // Emit a scroll event at most once every 150ms.
    scroll: 150,
    // Emit a media interaction event at most once every 800ms.
    media: 800,
    // For rapid text input, only record the final state.
    input: 'last',
  },
});
```

For more granular control, you can specify exactly which mouse interactions to record.

```javascript Selective Mouse Interaction Recording icon=logos:javascript
rrweb.record({
  emit(event) {
    // ...
  },
  sampling: {
    mouseInteraction: {
      MouseUp: false,
      MouseDown: false,
      Click: true, // Only record click events
      ContextMenu: false,
      DblClick: true, // and double-click events
      Focus: false,
      Blur: false,
      TouchStart: false,
      TouchEnd: false,
    },
  },
});
```

## Blocking DOM Elements

Certain UI elements can generate a large number of mutations, leading to excessive event data. Common examples include elements with JavaScript-controlled animations, complex SVG graphics, or long, dynamic lists. You can instruct rrweb to ignore these elements completely by adding a specific block class.

This prevents any events originating from the element or its descendants from being recorded, effectively reducing the recording area.

Common candidates for blocking include:
- Long, virtualized lists
- Complex SVG diagrams or animations
- Elements with continuous JS-driven animations
- Canvas animations

## Data Compression

After reducing the number of events, you can further shrink the storage size by compressing the event data itself. There are two primary approaches to this.

### Per-Event Compression with `packFn`

rrweb provides a utility package, `@rrweb/packer`, which can compress each event individually before it is emitted. This is accomplished by passing the `pack` function to the `packFn` recording option.

```javascript Recording with packFn icon=logos:javascript
import { pack } from '@rrweb/packer';

rrweb.record({
  emit(event) {
    // The 'event' is now a compressed string.
  },
  packFn: pack,
});
```

To replay the session, you must use the corresponding `unpack` function.

```javascript Replaying with unpackFn icon=logos:javascript
import { unpack } from '@rrweb/packer';

// 'events' is an array of compressed strings from the backend.
const replayer = new rrweb.Replayer(events, {
  unpackFn: unpack,
});
```

### Full-Session Compression (Recommended)

While `packFn` is convenient for client-side compression, a more efficient method is to compress the entire session on your backend. Batching all events from a session together allows compression algorithms like deflate to achieve a significantly higher compression ratio by leveraging redundancies across the entire event array.

This approach is the recommended best practice for production systems as it yields the best storage optimization.

## Deduplication

Another advanced optimization strategy is deduplication, which is particularly effective for applications with consistent UI elements across many sessions. rrweb inlines CSS styles to ensure pixel-perfect replay, but this can lead to the same style blocks being stored thousands of times.

By post-processing recorded sessions on your backend, you can extract these duplicated CSS rules, store a single copy, and replace the inline styles with a reference. This technique can also be applied to full DOM snapshots, offering substantial storage savings over time.