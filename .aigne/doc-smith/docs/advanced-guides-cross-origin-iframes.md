# Cross-Origin Iframes

Recording user interactions within a cross-origin iframe presents a challenge due to browser security restrictions, specifically the Same-Origin Policy. This policy prevents a script on one page from accessing the content of an iframe from another origin. `rrweb` provides a mechanism to bypass this for recording purposes, but it requires careful setup and a thorough understanding of the security implications.

This guide covers the necessary configuration to record sessions across different domains and highlights the critical security considerations you must address before implementation.

## How It Works

`rrweb`'s solution involves running a `rrweb` recorder instance on both the parent page and within the cross-origin iframe. The process unfolds as follows:

1.  **Iframe Recording**: The `rrweb` instance inside the iframe captures all user interactions and DOM changes as it normally would.
2.  **Event Communication**: Instead of emitting these events for local replay, the iframe's recorder serializes them and sends them to the parent window using the `window.postMessage()` API.
3.  **Parent Reception**: The parent window's `rrweb` instance listens for these messages.
4.  **ID Transformation**: To avoid conflicts between the DOM node IDs of the parent page and the iframe, the parent recorder transforms all IDs within the received events. This ensures every element in the combined recording has a unique identifier.
5.  **Event Consolidation**: The transformed events from the iframe are then integrated into the main session recording, creating a single, seamless replay.

```d2 Data Flow for Cross-Origin Recording
direction: down

parent-window: {
  label: "Parent Window"
  shape: rectangle

  rrweb-recorder: {
    label: "rrweb.record()"
    style.fill: "#d1e7dd"
  }
  
  event-handler: {
    label: "Message Event Listener"
  }
}

child-iframe: {
  label: "Cross-Origin Iframe"
  shape: rectangle
  style: {
    stroke: "#888"
    stroke-width: 2
    stroke-dash: 4
  }
  
  rrweb-child: {
    label: "rrweb.record()"
    style.fill: "#d1e7dd"
  }
}

parent-window.rrweb-recorder -> parent-window.event-handler: "Listens for messages"
child-iframe.rrweb-child -> parent-window.event-handler: "1. Sends events via postMessage()"
parent-window.event-handler -> parent-window.rrweb-recorder: "2. Forwards events"
parent-window.rrweb-recorder -> parent-window.rrweb-recorder: "3. Transforms IDs &\n Emits to main recording"

```

## Enabling Cross-Origin Recording

To enable this feature, you must configure `rrweb` on both the parent (embedding) page and the child (embedded) page.

### Parent Page Configuration

In the top-level window that contains the iframe, initialize `rrweb` with the `recordCrossOriginIframes` option set to `true`. This instance will listen for events from child iframes and merge them into a single recording.

```javascript Parent Page Setup icon=logos:javascript
rrweb.record({
  emit(event) {
    // All events, including those from iframes, will be emitted here.
  },
  recordCrossOriginIframes: true,
});
```

### Child Iframe Configuration

In the page that will be loaded inside the iframe, you must also initialize `rrweb` with `recordCrossOriginIframes: true`. This tells the recorder to send its events to the parent window instead of emitting them locally.

```javascript Iframe Page Setup icon=logos:javascript
rrweb.record({
  emit(event) {
    // This is required, but the child page will not emit any events.
    // Events are sent to the parent window via postMessage.
  },
  recordCrossOriginIframes: true,
});
```

## Important Security Considerations

Enabling cross-origin iframe recording effectively disables a critical browser security feature for your website. You should only use this feature if you have complete control over both domains and fully trust them. Be aware of the following risks:

-   **Data Exposure**: If you enable this feature on your website, any other website on the internet can embed your site in an iframe and record everything that happens within it, potentially capturing sensitive user data.
-   **Malicious Parent Window**: If a malicious website embeds your page, it can listen for all the events your page sends and transmit them to its own servers.
-   **Unencrypted Communication**: The `postMessage` API does not encrypt data. Malicious scripts or browser extensions running on the page could intercept the communication between the child iframe and the parent window, gaining access to the raw session data.

**Recommendation:** Only enable this feature in a tightly controlled environment. A common valid use case is an application suite where different parts of the application are hosted on separate subdomains but are part of the same trusted ecosystem.

## Injecting `rrweb` into Iframes

Here are several methods for ensuring the `rrweb` recording script is present in your cross-origin iframes.

### 1. Direct Script Inclusion

If you own and control the source code for both the parent and child websites, the most straightforward method is to include the `rrweb` script and the necessary configuration on both pages directly.

### 2. Browser Extension

For scenarios where you don't control the iframe's source, a browser extension can be used to inject content scripts into any page, including iframes. This allows you to programmatically add the `rrweb` recorder.

For more details, refer to the [Chrome Extension documentation on Content Scripts](https://developer.chrome.com/docs/extensions/mv3/content_scripts/#functionality).

### 3. Puppeteer Script

For automated testing or server-side recording environments, you can use a tool like Puppeteer to inject the recorder into all frames of a page.

```javascript Puppeteer Injection Script icon=logos:javascript
import puppeteer from 'puppeteer';

// Assuming 'code' contains the bundled rrweb code as a string.

async function injectRecording(frame, code) {
  await frame.evaluate((rrwebCode) => {
    if (window.__IS_RECORDING__) return;
    window.__IS_RECORDING__ = true;

    const s = document.createElement('script');
    s.type = 'text/javascript';
    s.innerHTML = rrwebCode;
    document.head.append(s);

    window.rrweb.record({
      emit: (event) => {
        // Expose a function to send events back to the Puppeteer context.
        window._captureEvent(event);
      },
      recordCrossOriginIframes: true,
    });
  }, code);
}

async function main() {
  const browser = await puppeteer.launch();
  const page = (await browser.pages())[0];

  const events = []; // Contains all events from all frames

  // Expose a function on the page that can be called from the browser context.
  await page.exposeFunction('_captureEvent', (event) => {
    events.push(event);
  });

  // Listen for new frames and inject the script.
  page.on('framenavigated', async (frame) => {
    await injectRecording(frame, rrwebCode); // Pass your rrweb code here
  });

  await page.goto('https://example.com');

  // At this point, `events` array will be populated with rrweb events.
  console.log(events);
}
```

### 4. Electron Application

In an Electron application, you can use preload scripts to inject `rrweb` into a `BrowserWindow` and its iframes. Preload scripts have access to Node.js APIs and the document context.

```typescript Electron Preload Configuration icon=logos:electron
import { BrowserWindow } from 'electron';
import path from 'path';

const win = new BrowserWindow({
  width: 800,
  height: 600,
  webPreferences: {
    // Specify the script to run before other scripts on the page.
    preload: path.join(__dirname, 'rrweb-recording-script.js'),
    // This enables the preload script to run inside iframes.
    nodeIntegrationInSubFrames: true,
    // It's a good security practice to disable nodeIntegration in the renderer.
    nodeIntegration: false,
  },
});
```

For more details, consult the official Electron documentation on [Preload Scripts](https://www.electronjs.org/docs/latest/tutorial/tutorial-preload).