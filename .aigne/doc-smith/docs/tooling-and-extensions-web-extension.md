# Web Extension

The rrweb web extension offers a convenient, code-free method for recording and replaying web page interactions directly within your browser. It captures session data with a simple click, making it an ideal tool for debugging, user behavior analysis, and quality assurance without any code integration.

This guide covers how to build, install, and use the extension for both Google Chrome and Mozilla Firefox. After recording a session, you can export it and use other tools, such as the [Video Conversion](./tooling-and-extensions-video-conversion.md) utility, to create video files.

## Building and Installing from Source

For developers who need to build the extension from its source code, the following steps will guide you through the process.

### 1. Install Dependencies

First, navigate to the `packages/web-extension` directory and install the required dependencies using Yarn.

```bash Installation icon=lucide:package
# Install project dependencies
yarn install
```

### 2. Build the Extension

Next, run the build command for your target browser. These commands will generate the necessary extension files in a `dist` folder.

For Google Chrome:

```bash Build for Chrome icon=logos:chrome
yarn build:chrome
```

For Mozilla Firefox:

```bash Build for Firefox icon=logos:firefox
yarn build:firefox
```

### 3. Load the Extension in Your Browser

Once the build is complete, you can load the unpacked extension into your browser.

**For Chrome:**
1.  Navigate to `chrome://extensions`.
2.  Enable "Developer mode" using the toggle in the top-right corner.
3.  Click "Load unpacked".
4.  Select the `dist` directory generated in the previous step.

**For Firefox:**
1.  Navigate to `about:debugging`.
2.  Click "This Firefox" in the sidebar.
3.  Click "Load Temporary Add-on".
4.  Select any file inside the `dist` directory.

## How to Use the Extension

After installation, the rrweb icon will appear in your browser's toolbar. Clicking it opens the control popup.

### Recording Controls

The popup provides a simple interface to manage the recording process.

<x-cards data-columns="2">
  <x-card data-title="Start/Stop Recording" data-icon="lucide:circle-dot">
    The main button starts the recording. When active, its icon changes to a square, and clicking it again stops the recording and saves the session.
  </x-card>
  <x-card data-title="Pause/Resume Recording" data-icon="lucide:pause-circle">
    This allows you to temporarily pause the recording. When paused, the icon changes to a play symbol, which you can click to resume.
  </x-card>
  <x-card data-title="View Sessions" data-icon="lucide:list">
    Opens a new tab displaying a list of all your saved recordings. You can view details and replay them directly from this page.
  </x-card>
  <x-card data-title="Settings" data-icon="lucide:settings">
    Opens the extension's options page, where you can configure recording settings.
  </x-card>
</x-cards>

While a recording is active, a timer will display the elapsed time. The extension can seamlessly handle recording across multiple tabs; if you switch to a new tab during a session, the recording will automatically pause on the old tab and resume on the new one.

## How It Works

The extension is composed of several key components that work together to capture and store session data.

```d2 How It Works Diagram
direction: down

User: {
  shape: c4-person
}

Browser-Toolbar: {
  label: "Browser Toolbar"
  shape: rectangle

  Extension-Popup: {
    label: "Extension Popup UI"
    shape: rectangle
  }
}

Background-Script: {
  label: "Background Script\n(Manages State)"
  shape: rectangle
}

Active-Tab: {
  label: "Active Web Page"
  shape: rectangle

  Content-Script: {
    label: "Content Script"
  }

  Injected-Script: {
    label: "Injected Script (rrweb.record)"
  }
}

Browser-Storage: {
  label: "Browser Storage\n(IndexedDB)"
  shape: cylinder
}

User -> Browser-Toolbar.Extension-Popup: "1. Clicks icon & 'Start'"
Browser-Toolbar.Extension-Popup -> Background-Script: "2. Sends 'start' command"
Background-Script -> Active-Tab.Content-Script: "3. Requests recording start"
Active-Tab.Content-Script -> Active-Tab.Injected-Script: "4. Injects & starts rrweb"
Active-Tab.Injected-Script -> Background-Script: "5. Streams events"
Background-Script -> Browser-Storage: "6. Saves session on stop"

```

1.  **Popup UI**: The user interacts with the popup to start, stop, or pause recording.
2.  **Background Script**: Acts as the central controller. It manages the recorder's status (`IDLE`, `RECORDING`, `PAUSED`), listens for commands from the UI, and persists session data.
3.  **Content Script**: Injected into the active web page. It establishes communication between the web page and the background script.
4.  **Injected Script**: This script contains the core `rrweb.record()` logic. It is injected into the page by the content script to capture DOM events and mutations.
5.  **Browser Storage**: All recorded events and session metadata are saved using the browser's `storage` and `unlimitedStorage` permissions, typically in IndexedDB.

## Summary

The rrweb web extension is a powerful tool for capturing user sessions without writing a single line of code. It simplifies the process of recording for analysis and debugging.

Once you have captured a session, the next logical step is often to share it or archive it as a video. To learn how, proceed to our guide on [Video Conversion](./tooling-and-extensions-video-conversion.md).