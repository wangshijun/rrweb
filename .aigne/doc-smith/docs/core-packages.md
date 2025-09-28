# Core Packages

The `rrweb` ecosystem is designed with a modular architecture, consisting of several specialized packages. This structure allows you to use only the parts you need, providing flexibility for different use cases. This section provides an overview of the primary packages that form the foundation of `rrweb`'s recording and replaying capabilities.

## Architecture Overview

The following diagram illustrates how the core packages interact during the recording and replaying processes. The recording is handled by `rrweb` and `rrweb-snapshot`, which produce a stream of events. The replay is managed by `rrweb-player`, which uses `rrdom` to reconstruct the session in a sandboxed environment.

```d2
direction: down

Recording-Process: {
  label: "Recording Process"
  shape: rectangle
  
  rrweb: {
    label: "rrweb\n(Recording Engine)"
  }
  
  rrweb-snapshot: {
    label: "rrweb-snapshot\n(DOM Serializer)"
  }
}

Replay-Process: {
  label: "Replay Process"
  shape: rectangle
  
  rrweb-player: {
    label: "rrweb-player\n(UI Component)"
  }
  
  rrdom: {
    label: "rrdom\n(Virtual DOM)"
  }
}

Event-Stream: {
  label: "Event Stream (JSON)"
  shape: cylinder
}

Recording-Process.rrweb -> Recording-Process.rrweb-snapshot: "1. Takes initial snapshot"
Recording-Process.rrweb -> Event-Stream: "2. Emits event stream"
Event-Stream -> Replay-Process.rrweb-player: "3. Consumes event stream"
Replay-Process.rrweb-player -> Replay-Process.rrdom: "4. Reconstructs DOM"
```

## Main Packages

Below is a detailed breakdown of each core package, its primary function, and a link to more in-depth documentation.

<x-cards data-columns="2">
  <x-card data-title="rrweb" data-icon="lucide:record-circle" data-href="/core-packages/recording-engine" data-cta="Learn More">
    The core recording engine. It captures all necessary data to reconstruct a web session, including the initial DOM state, incremental mutations, user interactions (mouse movements, clicks, scrolls), and more. It is highly configurable to handle complex scenarios.
  </x-card>
  <x-card data-title="rrweb-player" data-icon="lucide:play-circle" data-href="/core-packages/player-component" data-cta="Learn More">
    A feature-rich player component with a user interface for replaying recorded sessions. It provides typical video controls like play/pause, a timeline for scrubbing, speed adjustments, and displays metadata about the session.
  </x-card>
  <x-card data-title="rrweb-snapshot" data-icon="lucide:camera" data-href="/core-packages/dom-snapshot" data-cta="Learn More">
    A specialized utility for serializing a webpage's DOM and its state into a structured, JSON-serializable format. It also includes the logic to rebuild the DOM from this snapshot, which is the first step in any replay.
  </x-card>
  <x-card data-title="rrdom" data-icon="lucide:boxes">
    A lightweight, custom implementation of the DOM designed specifically for rrweb's replay process. Instead of manipulating the live DOM, the replayer uses rrdom to reconstruct the recorded page state in a sandboxed environment, ensuring fidelity and preventing side effects.
  </x-card>
</x-cards>

Understanding these core packages is key to effectively using and customizing `rrweb`. For practical implementation details, please proceed to the detailed guides for each component.