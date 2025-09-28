# DOM Snapshot

The `rrweb-snapshot` package is a foundational component of rrweb. Its primary responsibility is to convert a live Document Object Model (DOM) into a stateful, serializable data structure. It also provides the functionality to rebuild the DOM from this structure, which is the core mechanism enabling session replay.

This process is more complex than simply cloning the DOM. A DOM object itself is not serializable, meaning it cannot be converted to a text format like JSON for storage or transmission. The snapshot process intelligently captures the necessary information to create a portable and accurate representation of the web page's view.

## The Serialization Process

To ensure a faithful reconstruction of the user's view, `rrweb-snapshot` performs several critical transformations during the serialization process:

- **Stateful Capture**: It records dynamic states that are not reflected in the static HTML. For example, the current value of an `<input>` element or the scroll position of an element is captured and stored as an attribute in the snapshot.
- **Path Resolution**: All relative paths in attributes like `src`, `href`, and within CSS stylesheets (`url()`) are converted to absolute paths. This prevents broken links and missing resources when the session is replayed in a different environment.
- **Stylesheet Inlining**: To guarantee visual fidelity, external stylesheets linked via `<link>` tags are parsed, and their CSS rules are embedded directly into the snapshot. This ensures that styles are applied correctly, even if the original stylesheet is no longer accessible.
- **Script Neutralization**: All `<script>` tags are converted into `<noscript>` tags. This is a crucial security and stability measure that prevents any JavaScript from the original page from executing during replay.
- **Unique Node Identification**: Each node in the DOM tree is assigned a unique, sequential `id`. This identifier is essential for the incremental snapshot system, allowing rrweb to precisely target which node was added, removed, or modified.

Here is an example of a simple DOM tree and its corresponding serialized JSON structure:

```html HTML Structure
<html>
  <body>
    <header></header>
  </body>
</html>
```

```json Serialized Output icon=logos:javascript
{
  "type": 3, // Corresponds to NodeType.Element
  "tagName": "html",
  "attributes": {},
  "childNodes": [
    {
      "type": 3,
      "tagName": "head",
      "attributes": {},
      "childNodes": [],
      "id": 3
    },
    {
      "type": 3,
      "tagName": "body",
      "attributes": {},
      "childNodes": [
        {
          "type": 2, // Corresponds to NodeType.Text
          "textContent": "\n    ",
          "id": 5
        },
        {
          "type": 3,
          "tagName": "header",
          "attributes": {},
          "childNodes": [],
          "id": 6
        }
      ],
      "id": 4
    }
  ],
  "id": 2
}
```

## Core API

The `rrweb-snapshot` package exports several functions, but the two primary ones you will interact with are `snapshot` and `rebuild`.

### `snapshot()`

This function traverses a given DOM tree (starting from the `document` node) and generates a complete, serializable snapshot.

```javascript Take a Snapshot icon=logos:javascript
import { snapshot } from 'rrweb-snapshot';
import { Mirror } from 'rrweb-snapshot';

// A mirror is needed to map nodes to unique IDs.
const mirror = new Mirror();

// Take a snapshot of the current document.
const serializedDocument = snapshot(document, {
  mirror,
  // other options...
});

console.log(serializedDocument);
```

The `snapshot` function returns the serialized node tree, which can then be stored or sent to a server. It takes the node to be snapshotted and an optional configuration object as arguments.

### `rebuild()`

This function takes a serialized node tree and reconstructs the corresponding DOM within a target document, which is typically an `<iframe>` for sandboxing.

```javascript Rebuild a Snapshot icon=logos:javascript
import { rebuild } from 'rrweb-snapshot';
import { Mirror } from 'rrweb-snapshot';

// Assume 'serializedDocument' is the output from the snapshot() function.
// You also need a mirror for the rebuild process.
const mirror = new Mirror();

// Create an iframe to host the replay.
const iframe = document.createElement('iframe');
iframe.style.width = '800px';
iframe.style.height = '600px';
document.body.appendChild(iframe);

// Rebuild the DOM inside the iframe.
const [rebuiltNode, unsubscribe] = rebuild(serializedDocument, {
  doc: iframe.contentDocument,
  mirror,
});

// Clean up the listeners when done.
// unsubscribe();
```

The `rebuild` function returns a tuple containing the rebuilt node (or `null`) and an `unsubscribe` function to clean up any internal listeners that were set up.

## Low-Level APIs

For more granular control, the package also exposes the functions that `snapshot` and `rebuild` use internally:

- **`serializeNodeWithId(node, options)`**: Serializes a single DOM node and its children into the snapshot format, assigning unique IDs.
- **`buildNodeWithSN(serializedNode, options)`**: Constructs a DOM node from a serialized node object, mapping it in the provided mirror.

These are generally used for advanced use cases or when building custom tooling on top of rrweb's serialization logic.

---

In summary, the DOM snapshot mechanism is the bedrock of rrweb's ability to record and replay web sessions. By converting the DOM into a portable format and providing the tools to reconstruct it, `rrweb-snapshot` enables the entire session replay workflow.

To see how these snapshots are utilized in a full recording, please refer to the [Recording Engine](./core-packages-recording-engine.md) documentation.