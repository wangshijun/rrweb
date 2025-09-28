# Getting Started

This guide provides a step-by-step process to help you integrate rrweb into your project. The objective is to achieve a functional recording and replay implementation in under 30 minutes. We will cover the essential steps: installation, recording a user session, and replaying it.

![rrweb demo](../../../packages/rrvideo/demo/demo.gif)

Follow these three primary stages to get started:

<x-cards data-columns="3">
  <x-card data-title="Installation" data-icon="lucide:download" data-href="/getting-started/installation">
    Learn how to add rrweb to your project using package managers like npm and yarn, or by including it directly from a CDN.
  </x-card>
  <x-card data-title="Recording a Session" data-icon="lucide:record-circle" data-href="/getting-started/recording-a-session">
    Follow a simple example to start recording user interactions on your web page with the `rrweb.record()` function.
  </x-card>
  <x-card data-title="Replaying a Session" data-icon="lucide:play-circle" data-href="/getting-started/replaying-a-session">
    Use the recorded event data with the rrweb player to play back the captured user session in the browser.
  </x-card>
</x-cards>

## A Complete Example

For a practical and immediate result, the following HTML file contains a complete, self-contained example. It demonstrates how to record a session and then replay it within the same page. You can save this code as an `.html` file and open it in your browser to see it work.

```html A complete record and replay example icon=logos:html-5
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>rrweb Getting Started</title>
    <!-- Import rrweb library from CDN -->
    <script src="https://cdn.jsdelivr.net/npm/rrweb@latest/dist/rrweb.umd.cjs"></script>
    <!-- Import rrweb-player stylesheet for the replayer UI -->
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/style.css"
    />
    <!-- Import rrweb-player library from CDN -->
    <script src="https://cdn.jsdelivr.net/npm/rrweb-player@latest/dist/index.js"></script>
    <style>
      /* Basic styling for the demonstration */
      body {
        font-family: sans-serif;
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 2rem;
      }
      #replay-container {
        margin-top: 1.5rem;
        border: 1px solid #ccc;
        background: #f0f0f0;
      }
      button, input, textarea {
        margin: 0.5rem;
        padding: 0.5rem;
        font-size: 1rem;
      }
    </style>
  </head>
  <body>
    <h1>rrweb Getting Started Example</h1>
    <p>Interact with the elements below. Your actions will be recorded.</p>

    <!-- Interactive elements to record -->
    <textarea placeholder="Type something here..."></textarea>
    <input type="text" placeholder="Another input field" />
    <button id="action-btn">Click Me!</button>

    <!-- Controls for recording and replaying -->
    <div id="controls">
      <button id="record-btn">Start Recording</button>
      <button id="replay-btn" disabled>Replay Session</button>
    </div>

    <!-- Container for the replayer -->
    <div id="replay-container"></div>

    <script>
      let events = [];
      let stopFn = null;

      const recordBtn = document.getElementById('record-btn');
      const replayBtn = document.getElementById('replay-btn');
      const actionBtn = document.getElementById('action-btn');

      // Simple counter for the action button
      let clicks = 0;
      actionBtn.addEventListener('click', () => {
        clicks++;
        actionBtn.textContent = `Clicked ${clicks} times`;
      });

      recordBtn.addEventListener('click', () => {
        if (stopFn) {
          // If already recording, stop it
          stopFn();
          stopFn = null;
          recordBtn.textContent = 'Start Recording';
          replayBtn.disabled = false; // Enable replay button
        } else {
          // Start a new recording
          events = []; // Clear previous events
          stopFn = rrweb.record({
            emit(event) {
              // Push each recorded event to the 'events' array
              events.push(event);
            },
          });
          recordBtn.textContent = 'Stop Recording';
          replayBtn.disabled = true; // Disable replay until recording stops
        }
      });

      replayBtn.addEventListener('click', () => {
        if (events.length < 2) {
            alert('Please record a session first.');
            return;
        }

        // Clear the replay container before starting a new replay
        const replayContainer = document.getElementById('replay-container');
        replayContainer.innerHTML = '';

        // Initialize the replayer with the recorded events
        const replayer = new rrwebPlayer({
          target: replayContainer,
          props: {
            events,
            width: 800,
            height: 600,
          },
        });

        // Start the replay
        replayer.play();
      });
    </script>
  </body>
</html>
```

## Next Steps

Now that you have a basic implementation, you can explore the core components of rrweb in more detail to customize its functionality. Proceed to the [Core Packages](./core-packages.md) section to learn about the recording engine, the player component, and the DOM snapshot mechanism.