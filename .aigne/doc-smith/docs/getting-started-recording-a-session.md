# Recording a Session

This guide provides a simple, copy-paste ready example to start recording user interactions on a web page. The primary function for this is `rrweb.record()`, which captures DOM changes, user input, and other browser events into a structured format.

## Basic Recording

To begin recording, you only need to call the `rrweb.record()` function. The most critical option is the `emit` callback, which receives each captured event. It is your responsibility to store these events for later replay.

The `rrweb.record()` function returns another function that you can call to stop the recording process.

Here is a minimal example of how to start recording and collect events in an array:

```javascript Basic Recording Setup icon=logos:javascript
// This array will store the events for the session.
let events = [];

// Start recording, and save the function that stops the recording.
const stopFn = rrweb.record({
  emit(event) {
    // Push the event to the events array.
    events.push(event);
  },
});

// To stop the recording, you can call the returned function.
// For example, after 30 seconds:
// setTimeout(() => {
//   stopFn(); 
//   console.log('Recording stopped. Events captured:', events);
// }, 30000);
```

In a real-world application, you would typically send these events to a server for persistent storage rather than just keeping them in a local array.

## A Complete Example

Let's create a more practical example with "Start" and "Stop" buttons to control the recording session. When the recording is stopped, the captured event data will be logged to the browser's console.

You can copy and paste this complete HTML file into a local file and open it in your browser to see it in action. Make sure you have included the `rrweb` script, for example, from a CDN.

```html Full Recording Example icon=mdi:code-braces
<!DOCTYPE html>
<html>
<head>
  <title>rrweb Recording Example</title>
  <!-- Include the rrweb library from a CDN -->
  <script src="https://cdn.jsdelivr.net/npm/rrweb@latest/dist/rrweb.min.js"></script>
</head>
<body>

  <h1>rrweb Recording Demo</h1>
  <p>Interact with the page (click, type, resize) to generate events.</p>
  
  <textarea placeholder="Type something here..."></textarea>
  <button id="record-btn">Start Recording</button>
  <button id="stop-btn" disabled>Stop Recording</button>

  <script>
    let stopFn = null;
    let events = [];

    const recordBtn = document.getElementById('record-btn');
    const stopBtn = document.getElementById('stop-btn');

    recordBtn.addEventListener('click', () => {
      events = []; // Reset events array
      
      // Start the recording with the 'emit' callback.
      stopFn = rrweb.record({
        emit(event) {
          events.push(event);
        },
      });

      // Update button states
      recordBtn.setAttribute('disabled', 'true');
      stopBtn.removeAttribute('disabled');
      console.log('Recording started...');
    });

    stopBtn.addEventListener('click', () => {
      if (stopFn) {
        stopFn(); // Stop the recording
      }

      // Update button states
      recordBtn.removeAttribute('disabled');
      stopBtn.setAttribute('disabled', 'true');
      console.log('Recording stopped. Total events:', events.length);
      
      // Log the captured events to the console
      console.log('Captured events:', events);
    });
  </script>

</body>
</html>
```

### How It Works

1.  **Initialization**: We declare a `stopFn` variable to hold the stop function and an `events` array to store the captured data.
2.  **Start Recording**: Clicking the "Start Recording" button calls `rrweb.record()`. The `emit` function pushes every event into our `events` array. The returned stop function is stored in `stopFn`.
3.  **Stop Recording**: Clicking the "Stop Recording" button executes the `stopFn`, which cleans up all listeners and ends the session. The collected `events` are then logged to the console.

Now that you have successfully recorded a session, the next logical step is to play it back. You can learn how to do this in the next section.

---

Next, let's learn how to replay the session you just recorded.

[Replaying a Session](./getting-started-replaying-a-session.md)
