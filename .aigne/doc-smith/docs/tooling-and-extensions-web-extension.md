# Web Extension

The rrweb web extension offers a convenient, code-free method for recording and replaying user sessions directly within your browser. It is available for both Google Chrome and Mozilla Firefox. The extension adds an icon to your browser's toolbar, allowing you to start, pause, and stop recordings with a single click. All recorded data is stored locally in your browser for privacy and immediate access.

## Installation from Source

To use the extension, you must first build it from the source code. This process requires Node.js and Yarn.

### Step 1: Install Dependencies

First, clone the `rrweb` repository to your local machine. Then, navigate to the `packages/web-extension` directory and install the necessary dependencies by running the following command:

```bash
yarn install
```

### Step 2: Build the Extension

Next, build the extension for your target browser. The build process will generate an unpacked extension in the `dist/` directory.

**For Google Chrome**

```bash Build for Chrome
yarn build:chrome
```

**For Mozilla Firefox**

```bash Build for Firefox
yarn build:firefox
```

### Step 3: Load the Extension in Your Browser

After the build is complete, load the unpacked extension into your browser.

**Google Chrome**

1.  Navigate to `chrome://extensions` in your browser.
2.  Enable the **Developer mode** toggle in the top-right corner.
3.  Click the **Load unpacked** button.
4.  Select the `dist/chrome` directory generated in the previous step.

**Mozilla Firefox**

1.  Navigate to `about:debugging` in your browser.
2.  Click on the **This Firefox** tab on the left.
3.  Click the **Load Temporary Add-on...** button.
4.  Select any file inside the `dist/firefox` directory.

Once loaded, the rrweb icon will appear in your browser's toolbar.

## Usage Guide

The extension's interface is straightforward, allowing you to manage recordings from a simple popup.

### Starting and Stopping a Recording

1.  Click the rrweb icon in your browser's toolbar to open the popup.
2.  To begin recording the current tab, click the large circular red button. The recording will start immediately.
3.  While recording, the button will transform into a square, and a timer will display the elapsed time.
4.  To stop the recording, click the square red button. The session is automatically saved to your browser's local storage.

### Pausing and Resuming

You can pause the recording at any time without terminating the session.

*   **To Pause**: While a recording is active, click the pause icon next to the stop button. The timer will halt.
*   **To Resume**: Click the play icon (resume) to continue recording the session.

Notably, the extension automatically handles tab switching. If you navigate to a new tab while recording, the session is paused on the old tab and seamlessly resumed on the new one.

### Accessing Recorded Sessions

All recorded sessions are stored locally and can be accessed for replay.

*   After stopping a recording, the popup will display a link to the newly created session file.
*   To view a complete list of all your saved sessions, click the list icon in the top-right corner of the popup. This action opens the session management page in a new tab, where you can view, manage, and replay your recordings.

## Technical Details

The extension operates with a focus on privacy and functionality, requiring specific permissions to function correctly.

### Permissions

The extension requires the following browser permissions:

| Permission | Purpose |
| :--- | :--- |
| `activeTab` | Allows the extension to access the content of the currently active tab for recording. |
| `storage` | Used to save user settings and session metadata. |
| `unlimitedStorage` | Grants permission to store large session recordings in IndexedDB without restrictive size limitations. |

### Data Storage

All session data is stored locally within your browser's IndexedDB. No data is transmitted to external servers, ensuring that your recorded sessions remain private.

### Cross-Origin Iframe Support

The extension is designed to capture a complete user experience, including interactions within cross-origin iframes. It injects a content script into all frames on a page to ensure comprehensive recording coverage.

## Summary

The rrweb web extension is a powerful tool for capturing session recordings without any code integration, simplifying bug reproduction and user behavior analysis.

After recording a session, you may want to convert it into a video format for easier sharing. Proceed to the [Video Conversion](./tooling-and-extensions-video-conversion.md) guide to learn how.