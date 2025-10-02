
# Recording a Session

To capture user interactions on a web page, you will use the `rrweb.record()` function. This function observes the Document Object Model (DOM) and listens for user actions like mouse clicks, scrolling, and keyboard input. It converts all these activities into a series of structured data objects called "events".

This guide provides a straightforward, practical example to begin recording.

## Basic Recording

The primary function for recording is `rrweb.record()`. It takes a configuration object as its argument. The only required option is `emit`, a callback function that receives the recorded events one by one.

Here is a minimal example of how to start and stop a recording:

```javascript Basic Recording Example icon=logos:javascript
// This events array will store the recorded events.
const events = [];

// The 'rrweb.record' function returns a function that can be used to stop the recording.
const stopFn = rrweb.record({
  emit(event) {
    // Push the event into the events array
    events.push(event);
  },
});

// For demonstration, we'll stop the recording after 5 seconds.
// In a real application, you would call stopFn based on user action or other logic.
setTimeout(() => {
  stopFn();
  console.log('Recording stopped. Total events captured:', events.length);
  // You can now save the 'events' array to a file, send it to a server, etc.
  console.log(JSON.stringify(events));
}, 5000);
```

### How It Works

1.  **Initialization**: We declare an empty array named `events` to store the data rrweb produces.
2.  **Starting the Recorder**: We call `rrweb.record()` with an object containing the `emit` function.
    *   **`emit(event)`**: This is the core of the recording process. `rrweb` calls this function every time a new interaction or change is captured. Our implementation simply adds each `event` object to our `events` array.
3.  **Stopping the Recorder**: The `rrweb.record()` function returns another function, which we've named `stopFn`. When you are ready to end the recording session, you call `stopFn()`. This detaches all the observers and stops the recording process.

## The Recorded Events

After stopping the recording, the `events` array will contain a JSON-serializable representation of the user's session. Each event object in the array has a `type`, a `timestamp`, and a `data` payload.

Here is a simplified example of what the first few events in the array might look like:

```json Captured Events (Example) icon=mdi:code-json
[
  {
    "type": 2,
    "data": {
      "href": "http://localhost:8080/",
      "width": 1440,
      "height": 796
    },
    "timestamp": 1617936187031
  },
  {
    "type": 0,
    "data": {},
    "timestamp": 1617936187032
  },
  {
    "type": 1,
    "data": {},
    "timestamp": 1617936187033
  },
  {
    "type": 3,
    "data": {
      "node": {
        "id": 1,
        "type": 0,
        "childNodes": [
          // ... full DOM snapshot ...
        ]
      },
      "initialOffset": {
        "left": 0,
        "top": 0
      }
    },
    "timestamp": 1617936187042
  }
]
```

These events capture everything from the initial state of the page (a full DOM snapshot) to subsequent incremental changes and user interactions.

## Summary

You have now successfully recorded a user session. The process involves initializing the recorder with an `emit` function to collect events and calling the returned function to stop the recording.

With the array of events captured, you are ready for the next step.

- **Next**: Learn how to play back this session in the [Replaying a Session](./getting-started-replaying-a-session.md) guide.
