# Core Packages

The rrweb ecosystem is designed with a modular architecture, allowing developers to use only the parts they need. At its heart are a few core packages that handle the logic for recording, snapshotting the DOM, and replaying sessions. Understanding these components is key to leveraging rrweb's full potential.

This section provides a detailed exploration of these main packages, explaining their specific roles and how they interact to provide a complete session recording and replay solution.

## Architecture Overview

The following diagram illustrates the relationship between the core packages and the flow of data from recording to replay.

```d2
direction: down

Recording: {
  label: "Recording Process"
  shape: rectangle

  rrweb: {
    label: "rrweb\n(Orchestrator)"
    shape: rectangle
  }

  rrweb-record: {
    label: "@rrweb/record\n(Core Recording Logic)"
    shape: rectangle
  }

  rrweb-snapshot: {
    label: "rrweb-snapshot\n(DOM Snapshotting)"
    shape: rectangle
  }
}

Replay: {
  label: "Replay Process"
  shape: rectangle

  rrweb-player: {
    label: "rrweb-player\n(UI Component)"
    shape: rectangle
  }

  rrweb-replay: {
    label: "@rrweb/replay\n(Core Replay Logic)"
    shape: rectangle
  }

  rrdom: {
    label: "rrdom\n(Virtual DOM)"
    shape: rectangle
  }
}

Recording.rrweb -> Recording.rrweb-record: "Includes"
Recording.rrweb-record -> Recording.rrweb-snapshot: "Uses to serialize DOM"

Recording -> Replay: "Serialized Events"

Replay.rrweb-player -> Replay.rrweb-replay: "Uses"
Replay.rrweb-replay -> Replay.rrdom: "Uses to rebuild DOM"
```

## Key Packages

The rrweb functionality is distributed across several key packages. Here is a summary of the most important ones:

| Package | Description |
|---|---|
| `rrweb` | The main, all-in-one package that provides both recording and replaying capabilities. |
| `rrweb-player` | A feature-rich UI component for replaying rrweb sessions with a timeline, play/pause controls, and event inspection. |
| `rrweb-snapshot` | A library for serializing the DOM and external assets into a structured, replayable format. |
| `rrdom` | A virtual DOM implementation designed specifically for rrweb, used to accurately rebuild the DOM state during replay. |
| `@rrweb/record` | A scoped package containing only the recording logic, ideal for applications that only need to capture sessions. |
| `@rrweb/replay` | A scoped package containing only the core replaying logic, used by `rrweb-player` under the hood. |

## In-Depth Guides

For a deeper dive into the configuration and API of each core component, please refer to the following guides:

<x-cards data-columns="3">
  <x-card data-title="Recording Engine" data-href="/core-packages/recording-engine" data-icon="lucide:record-circle">
    Delve into the configuration options of the core recording engine, such as data masking, event sampling, and custom hooks.
  </x-card>
  <x-card data-title="Player Component" data-href="/core-packages/player-component" data-icon="lucide:play-circle">
    Learn how to use and configure the rrweb-player UI, including its properties, API for programmatic control, and player events.
  </x-card>
  <x-card data-title="DOM Snapshot" data-href="/core-packages/dom-snapshot" data-icon="lucide:camera">
    Understand how rrweb serializes the DOM into a replayable format and rebuilds it using its virtual DOM implementation (rrdom).
  </x-card>
</x-cards>