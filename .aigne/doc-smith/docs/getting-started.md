# Getting Started

This guide offers a quick-start for developers to install rrweb, record their first session, and replay it. The goal is to achieve a working example in under 30 minutes. The process involves three main steps: Installation, Recording, and Replaying.

## The Basic Workflow

The fundamental workflow of rrweb is straightforward:
1.  **Record**: Capture user interactions and DOM changes on a webpage using `rrweb.record()`. This generates a series of JSON events.
2.  **Store**: Save these events. You can send them to a server, store them in a database, or keep them in local storage. The key is to maintain their order.
3.  **Replay**: Load the stored events into `rrweb.Replayer` to reconstruct and play back the user's session in another browser session.

This diagram illustrates the flow from recording to replay:

```d2
direction: down

web-app: {
  label: "Web Application\n(Live User Session)"
  shape: rectangle
}

rrweb-record: {
  label: "rrweb.record()"
  shape: rectangle
}

storage: {
  label: "Event Storage\n(Server, DB, etc.)"
  shape: cylinder
}

rrweb-replayer: {
  label: "rrweb.Replayer"
  shape: rectangle
}

replay-target: {
  label: "Replay Target\n(e.g., iframe)"
  shape: rectangle
}

web-app -> rrweb-record: "1. Record Session"
rrweb-record -> storage: "2. Store JSON Events"
storage -> rrweb-replayer: "3. Load Events"
rrweb-replayer -> replay-target: "4. Replay Session"
```

## How to Proceed

We've broken down the process into three simple, sequential guides. Follow them in order to go from zero to a fully functional implementation.

<x-cards data-columns="3">
  <x-card data-title="1. Installation" data-icon="lucide:download" data-href="/getting-started/installation">
    First, add the rrweb library to your project. We'll cover installation using package managers like npm/yarn and via a CDN for quick prototyping.
  </x-card>
  <x-card data-title="2. Recording a Session" data-icon="lucide:record-circle" data-href="/getting-started/recording-a-session">
    Next, learn how to start recording a user's session with a simple function call. This section provides a minimal, copy-paste example to get you started.
  </x-card>
  <x-card data-title="3. Replaying a Session" data-icon="lucide:play-circle" data-href="/getting-started/replaying-a-session">
    Finally, use the recorded data with the rrweb replayer to play back the session. We'll show you how to set up the player and load the events.
  </x-card>
</x-cards>

## A Quick Look at the API

To give you a preview, here is a look at the core recording and replaying functions.

### Recording

You can start recording with a single API call. The `emit` callback is where you'll receive the event data to store.

```javascript Recording a Session icon=logos:javascript
let events = [];

const stopFn = rrweb.record({
  emit(event) {
    // Push the event to the events array
    events.push(event);
  },
});

// Later, to stop recording:
// stopFn();
```

### Replaying

To replay, you just need a target element to mount the replayer and the array of events you recorded.

```javascript Replaying a Session icon=logos:javascript
const replayer = new rrweb.Replayer(events, {
  root: document.body, // The element to replay in
});

replayer.play();
```

Ready to begin? Let's start with the [Installation guide](./getting-started-installation.md).
