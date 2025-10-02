# Overview

`rrweb`, short for 'record and replay the web', is an open-source library that enables you to record and replay user interactions on a web application. It provides a high-fidelity, pixel-perfect reproduction of a user's session, which is invaluable for debugging, user behavior analysis, and providing remote assistance.

Unlike screen recording tools that produce video, rrweb captures user interactions as a series of structured, timestamped events. This data format is lightweight, easily searchable, and allows for precise analysis and inspection of the application's state at any point in time.

## How It Works

The core functionality of rrweb is divided into two distinct phases: recording and replaying.

1.  **Recording**: When a recording session begins, rrweb first takes a complete snapshot of the Document Object Model (DOM). This initial snapshot serves as the baseline. After that, it uses the `MutationObserver` API to listen for all subsequent changes to the DOM, such as text input, mouse movements, clicks, and style changes. These changes, or mutations, are captured as incremental events.

2.  **Replaying**: The replayer takes the initial DOM snapshot and the stream of mutation events as input. It first reconstructs the initial state of the page in a sandboxed environment (typically an `iframe`). Then, it applies each mutation event in the exact order and with the same timing as it originally occurred, accurately recreating the user's session.

This approach ensures a precise and efficient way to capture and replay web sessions, providing developers with a powerful tool for understanding and troubleshooting their applications.

## Architecture

rrweb is designed with a modular architecture, consisting of several key packages that work together to provide its recording and replaying capabilities. Understanding these components is helpful for customizing and extending rrweb's functionality.

```d2
direction: right

subgraph "User's Browser Session" {
  style.fill: "#f0f4f8"
  Browser: "Live DOM & Interactions"
}

subgraph "Recording Process" {
  style.fill: "#e6f7ff"
  Record [shape: hexagon, label: "rrweb.record()"]: {
    Snapshot [shape: document, label: "rrweb-snapshot"]: Captures the initial DOM state.
    Observer [shape: oval, label: "MutationObserver"]: Listens for all subsequent changes.
  }
  Browser -> Record: "User interacts with page"
  Record.Snapshot -> Events: "Initial Snapshot Event"
  Record.Observer -> Events: "Stream of Mutation Events"
}

subgraph "Replaying Process" {
  style.fill: "#e6fffb"
  Replayer [shape: hexagon, label: "rrweb.Replayer() or rrweb-player"]: {
    Rebuild [shape: document, label: "rrweb-snapshot"]: Reconstructs the DOM from the snapshot.
    Apply [shape: oval, label: "Event Application"]: Applies mutations sequentially.
  }
  Events -> Replayer: "Feeds into replayer"
  Replayer.Rebuild -> "Replayed Session"
  Replayer.Apply -> "Replayed Session"
}

Events [shape: cylinder, label: "Serialized Events (JSON)"]


classDef default {
  font-size: 14
  font-family: "Menlo", "Monaco", monospace
}

```

<x-cards data-columns="2">
  <x-card data-title="rrweb" data-icon="lucide:file-json">
    The core package that orchestrates the recording and replaying processes. It integrates `rrweb-snapshot` to capture the DOM and listens for changes to generate a stream of events.
  </x-card>
  <x-card data-title="rrweb-snapshot" data-icon="lucide:camera">
    A utility for converting a DOM tree into a serializable data structure. It also includes the logic for rebuilding the DOM from this serialized format, which is essential for both the initial snapshot and the final replay.
  </x-card>
  <x-card data-title="rrweb-player" data-icon="lucide:play-circle">
    A pre-built, feature-rich player component that provides a user interface for replaying rrweb sessions. It includes controls for play/pause, seeking, and adjusting playback speed.
  </x-card>
  <x-card data-title="@rrweb/types" data-icon="lucide:file-type">
    A dedicated package containing all the TypeScript type definitions shared across the rrweb ecosystem, ensuring type safety and consistency between the different packages.
  </x-card>
</x-cards>

## Next Steps

Now that you have a high-level understanding of what rrweb is and how it works, the next logical step is to see it in action. Proceed to the [Getting Started](./getting-started.md) guide for a quick tutorial on how to install rrweb and record your first session.