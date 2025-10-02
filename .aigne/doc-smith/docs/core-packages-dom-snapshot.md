# DOM Snapshot

The `rrweb-snapshot` package is the engine responsible for converting a live Document Object Model (DOM) into a stateful, serializable data structure and, conversely, rebuilding a DOM from that structure. This process is fundamental to how rrweb captures the initial state of a page for replay. It provides the foundational snapshot that all subsequent incremental changes are applied to.

This functionality is used by the [Recording Engine](./core-packages-recording-engine.md) to create the first event in a session and by the [Player Component](./core-packages-player-component.md) to construct the initial DOM state before applying mutations.

## The Serialization Process

To transmit and store a representation of a web page, its DOM structure must be converted into a serializable format like JSON. The `rrweb-snapshot` library implements a non-standard serialization process tailored for accurate replay, which includes several key transformations:

- **Unique Identification**: Each node in the DOM is assigned a unique, incrementing `id`. This is crucial for tracking nodes and applying incremental changes correctly during replay.
- **State Capture**: Dynamic states that are not reflected in the HTML source, such as the current value of an `<input>` field or the scroll position of an element, are captured and stored as special attributes.
- **Path Absolutization**: Relative paths in attributes like `href`, `src`, and within CSS stylesheets are converted to absolute URLs. This ensures that resources load correctly when the session is replayed in a different context (e.g., inside an `<iframe>` on a different domain).
- **Stylesheet Inlining**: To guarantee that all styles are applied correctly during replay, external stylesheets (`<link rel="stylesheet">`) are fetched, parsed, and their rules are inlined into the snapshot as text content.
- **Script Sanitization**: All `<script>` tags are transformed into `<noscript>` tags. This prevents any JavaScript from the original page from executing during replay, as rrweb replays the *effects* of scripts, not the scripts themselves.

### Example of a Serialized DOM

A simple HTML structure like this:

```html
<html>
  <body>
    <header>Hello</header>
  </body>
</html>
```

Is serialized into a JSON object that represents the entire node tree, with each node having a type, tagName, attributes, child nodes, and a unique `id`:

```json
{
  "type": 0,
  "childNodes": [
    {
      "type": 2,
      "tagName": "html",
      "attributes": {},
      "childNodes": [
        {
          "type": 2,
          "tagName": "head",
          "attributes": {},
          "childNodes": [],
          "id": 3
        },
        {
          "type": 2,
          "tagName": "body",
          "attributes": {},
          "childNodes": [
            {
              "type": 2,
              "tagName": "header",
              "attributes": {},
              "childNodes": [
                {
                  "type": 3,
                  "textContent": "Hello",
                  "id": 6
                }
              ],
              "id": 5
            }
          ],
          "id": 4
        }
      ],
      "id": 2
    }
  ],
  "id": 1
}
```

## The Reconstruction Process

Reconstruction is the reverse process of serialization. It takes the serialized node tree and builds a live DOM from it. This is typically done inside an `<iframe>` to provide an isolated environment for the replay.

Internally, rrweb uses its own virtual DOM implementation, **rrdom**, to efficiently manage the reconstructed DOM and apply subsequent mutations. `rrdom` is designed to mirror the behavior of a real DOM, providing a performance-optimized layer for applying changes, which is especially useful when seeking to different points in a session timeline.

The reconstruction process involves:

1.  Creating DOM nodes (`Element`, `Text`, `Comment`, etc.) based on the `type` and `tagName` specified in the serialized data.
2.  Recursively building the entire DOM tree by appending child nodes.
3.  Setting all attributes on the elements, including special `rr_` attributes that restore state like scroll positions (`rr_scrollTop`) and media playback status (`rr_mediaState`).
4.  Injecting inlined CSS rules into `<style>` tags.

## Manual Snapshot and Reconstruction API

The `rrweb-snapshot` package exports functions that allow you to manually perform serialization and reconstruction. This can be useful for debugging or for custom applications that require DOM snapshots.

### Core Functions

| Function | Description |
| :--- | :--- |
| `snapshot(n, options?)` | Traverses a DOM node (usually `document`) and returns its serializable representation. |
| `rebuild(n, options)` | Reconstructs a DOM tree from a serialized node object. Requires a target `doc` (e.g., an iframe's `contentDocument`). |
| `serializeNodeWithId(n, options)` | A lower-level function that serializes a single node into the snapshot format and assigns it an ID. |
| `buildNodeWithSN(sn, options)` | A lower-level function that builds a single DOM node from a serialized node (`sn`). |

### Usage Example

Here is a basic example of how to take a snapshot of the current page and rebuild it in a new `<iframe>`.

```javascript Snapshot and Rebuild Flow icon=logos:javascript
import { snapshot, rebuild } from 'rrweb-snapshot';

// 1. Take a snapshot of the current document.
// The snapshot function returns a serializable representation of the DOM.
const serializedDocument = snapshot(document);

if (serializedDocument) {
  // The 'serializedDocument' object can be sent over the network or stored.
  // For example: const snapshotJSON = JSON.stringify(serializedDocument);

  // 2. To rebuild, create a target document, typically in an iframe.
  const iframe = document.createElement('iframe');
  iframe.style.width = '100%';
  iframe.style.height = '500px';
  document.body.appendChild(iframe);
  const targetDoc = iframe.contentDocument;

  // 3. Rebuild the DOM from the serialized object inside the iframe.
  if (targetDoc) {
    rebuild(serializedDocument, { doc: targetDoc });
  }
}
```

## Summary

The `rrweb-snapshot` package is a cornerstone of rrweb, providing the essential mechanisms for DOM serialization and reconstruction. It transforms a live, complex DOM into a portable, replayable format through a series of intelligent transformations. Understanding this process is key to comprehending how rrweb achieves high-fidelity session recording and replay.

For more information on how this snapshot is used, see the [Player Component](./core-packages-player-component.md) documentation.