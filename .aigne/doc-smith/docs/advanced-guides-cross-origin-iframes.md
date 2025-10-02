# Cross-Origin Iframes

By default, browser security policies prevent a parent window from accessing the content of an `iframe` loaded from a different domain. This is a crucial security feature known as the Same-Origin Policy. While this policy is in place for protection, `rrweb` provides a mechanism to record user sessions that span across these cross-origin boundaries, provided you have control over both the parent and child pages.

This guide covers the necessary setup, security considerations, and different methods for enabling recording in cross-origin iframes.

## How It Works

The cross-origin recording mechanism in `rrweb` relies on the `window.postMessage` API for communication between the iframe and its parent window.

1.  **Child Iframe**: An `rrweb` recorder instance within the cross-origin iframe captures events as it normally would. Instead of emitting them for local storage or transmission, it sends them to the parent window via `postMessage`.
2.  **Parent Page**: The main `rrweb` recorder on the parent page listens for `message` events. When it receives an event from a recognized child iframe, it processes the event.
3.  **Event Transformation**: To prevent conflicts, the parent recorder transforms the incoming event data, primarily by re-mapping node IDs from the iframe to ensure they are unique within the context of the entire session. The transformed event is then integrated into the main recording stream.

## Configuration

To enable cross-origin iframe recording, you must configure `rrweb` on both the parent page and the page loaded inside the iframe.

### Parent Page Setup

In the parent window, initialize the recorder with the `recordCrossOriginIframes` option set to `true`. This enables the recorder to listen for and process events sent from child iframes.

```javascript Parent Page Configuration icon=logos:javascript
rrweb.record({
  emit(event) {
    // All events, including those from cross-origin iframes, will be emitted here.
  },
  recordCrossOriginIframes: true,
});
```

### Iframe Page Setup

Similarly, the page that will be loaded into the iframe must also have an `rrweb` recorder initialized with `recordCrossOriginIframes: true`. This tells the recorder to send its events to the parent window instead of emitting them locally.

```javascript Iframe Page Configuration icon=logos:javascript
rrweb.record({
  emit(event) {
    // This function is required by rrweb, but no events will be emitted here
    // when the page is in a cross-origin iframe.
  },
  recordCrossOriginIframes: true,
});
```

If `rrweb` is not running in the top-level window, any events captured within the iframe will be lost.

## Security Considerations

Enabling this feature bypasses standard browser security controls and introduces risks that must be carefully considered.

-   **Malicious Host Page**: If your website (the child iframe) is embedded into a malicious third-party site, that site's `rrweb` instance can record all user interactions within your application's iframe.
-   **Event Sniffing**: The communication via `postMessage` is not encrypted. Malicious scripts running on the parent page can listen for these messages and intercept the raw event data, potentially exposing sensitive information.

Due to these risks, you should only enable this feature if you have strict control over the websites allowed to embed your application.

## Methods for Injecting rrweb into Iframes

Here are several common strategies for ensuring the `rrweb` recording script is present in your cross-origin iframes.

### 1. Direct Script Inclusion

If you own the source code for both the parent page and the embedded page, the simplest method is to include the `rrweb` script tag in the HTML of both pages.

### 2. Browser Extensions

A browser extension can use content scripts to inject `rrweb` into any page, including iframes, regardless of their origin. This is a powerful method for building developer tools or support applications.

For more details, refer to the [Chrome Extension documentation on content scripts](https://developer.chrome.com/docs/extensions/mv3/content_scripts/#functionality).

### 3. Puppeteer for Automated Environments

When running tests or generating recordings in an automated environment like Puppeteer, you can inject the recording script into all frames as they are navigated.

```javascript Puppeteer Injection Script icon=logos:javascript
import puppeteer from 'puppeteer';

// Assume 'rrwebCode' contains the bundled rrweb library as a string

async function injectRecording(frame) {
  await frame.evaluate((rrwebCode) => {
    if (window.__IS_RECORDING__) return;
    window.__IS_RECORDING__ = true;

    const s = document.createElement('script');
    s.type = 'text/javascript';
    s.innerHTML = rrwebCode;
    document.head.append(s);

    window.rrweb.record({
      emit: (event) => {
        // Expose an event capture function to the Puppeteer context
        window._captureEvent(event);
      },
      recordCrossOriginIframes: true,
    });
  }, rrwebCode);
}

const browser = await puppeteer.launch();
const page = (await browser.pages())[0];

const events = []; // All events from all frames will be collected here

// Expose a function from Node.js to the browser context
await page.exposeFunction('_captureEvent', (event) => {
  events.push(event);
});

// Inject rrweb into each frame as it loads
page.on('framenavigated', async (frame) => {
  await injectRecording(frame);
});

await page.goto('https://example.com');

// The 'events' array now contains the recording data.
```

### 4. Electron Applications

In an Electron application, you can use a `preload` script to reliably inject `rrweb` into all web contents, including iframes. To ensure the preload script runs in sub-frames, you need to configure the `webPreferences` of your `BrowserWindow`.

```typescript Electron Preload Configuration icon=logos:electron
const win = new BrowserWindow({
  width: 800,
  height: 600,
  webPreferences: {
    // Path to your recording script
    preload: path.join(__dirname, 'rrweb-recording-script.js'),
    // This allows the preload script to run in iframes
    nodeIntegrationInSubFrames: true,
    // It's a good practice to disable nodeIntegration for security
    nodeIntegration: false,
  },
});
```

For more information, see the Electron documentation on [Preload Scripts](https://www.electronjs.org/docs/latest/tutorial/tutorial-preload).
