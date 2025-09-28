# Overview

rrweb, short for 'record and replay the web', is an open-source JavaScript library that enables you to record and replay user interactions on your web applications. It provides a high-fidelity, pixel-perfect reproduction of a user's session, capturing everything from mouse movements and clicks to DOM mutations and CSS changes.

This capability is invaluable for a variety of use cases, including:

- **Bug Reproduction**: Eliminate guesswork by seeing the exact sequence of actions a user took to encounter a bug.
- **User Experience Analysis**: Gain insights into how users interact with your application, identify points of friction, and validate design choices.
- **Customer Support**: Resolve support tickets more efficiently by viewing a user's session to understand their issue.

## Core Functionalities

rrweb's functionality is centered around two primary operations: recording and replaying.

### Recording

The recording process captures a complete picture of the web page's state and subsequent changes. It begins by taking a full snapshot of the Document Object Model (DOM). After this initial snapshot, it subscribes to all changes using the `MutationObserver` API, recording every mutation, user interaction (like mouse clicks and input changes), and other events as a timestamped, serializable data entry. This stream of events, known as "events," represents the entire user session.

### Replaying

The replaying process reconstructs the recorded session in a sandboxed environment, typically an `<iframe>`. The replayer first rebuilds the initial DOM from the snapshot. Then, it iterates through the stream of mutation events, applying each one at the correct time to accurately recreate the user's interactions and the application's responses.

```d2
direction: down

User: {
  shape: c4-person
}

Your-Application: {
  label: "Your Web Application"
  shape: rectangle

  rrweb-recorder: {
    label: "rrweb Recorder"
    shape: rectangle
  }
}

Backend: {
  label: "Your Backend"
  shape: rectangle

  Event-Storage: {
    label: "Event Storage"
    shape: cylinder
  }
}

Developer-View: {
  label: "Developer View"
  shape: rectangle

  rrweb-replayer: {
    label: "rrweb Replayer"
    shape: rectangle
  }

  Sandbox: {
    label: "Sandboxed iframe"
    shape: rectangle
    style.stroke-dash: 4
  }
}

User -> Your-Application: "Interacts with UI"
Your-Application.rrweb-recorder -> Backend.Event-Storage: "1. Record & send events"
Developer-View.rrweb-replayer -> Backend.Event-Storage: "2. Fetch events"
Developer-View.rrweb-replayer -> Developer-View.Sandbox: "3. Replay session"

```

## Project Architecture

rrweb is a monorepo composed of several specialized packages. Understanding their roles is key to effectively using the library.

<x-cards data-columns="3">
  <x-card data-title="rrweb" data-icon="lucide:file-json-2">
    The core package that orchestrates the recording and replaying processes. It integrates the snapshot and rebuilding functionalities to capture and reproduce sessions.
  </x-card>
  <x-card data-title="rrweb-snapshot" data-icon="lucide:camera">
    This package is responsible for the foundational tasks of converting a live DOM into a serializable data structure (snapshotting) and rebuilding the DOM from that data.
  </x-card>
  <x-card data-title="rrweb-player" data-icon="lucide:play-circle">
    A feature-rich, pre-built player component with a graphical user interface (GUI) for replaying sessions. It includes controls for play/pause, seeking, and adjusting playback speed.
  </x-card>
</x-cards>

## Next Steps

Now that you have a high-level understanding of what rrweb is and how it works, the next logical step is to see it in action. Proceed to the [Getting Started](./getting-started.md) guide to learn how to install rrweb and record your first session.