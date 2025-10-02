# Tooling and Extensions

The `rrweb` ecosystem extends beyond the core recording and replaying libraries. To streamline common workflows, we provide auxiliary tools that simplify session capture and processing. These tools are designed to make `rrweb` more accessible, whether you're a developer, a QA tester, or a product manager.

This section covers two primary tools:

1.  **Web Extension**: A browser extension for Chrome and Firefox that allows you to record sessions on any website without writing a single line of code.
2.  **Video Conversion (rrvideo)**: A command-line interface (CLI) tool that transforms `rrweb`'s JSON-based session data into standard video files, such as WebM.

```d2
direction: down

Website: {
  label: "Any Website"
  shape: rectangle
}

Session-Data: {
  label: "Session Data (JSON)"
  shape: cylinder
}

arrweb-Tooling: {
  label: "rrweb Tooling & Extensions"
  shape: rectangle

  Web-Extension: {
    label: "Web Extension"
    shape: rectangle
  }

  Video-Conversion: {
    label: "Video Conversion (rrvideo)"
    shape: rectangle
  }
}

Video-File: {
  label: "Video File (WebM)"
  shape: rectangle
}

Website -> rrweb-Tooling.Web-Extension: "Record session"
rrweb-Tooling.Web-Extension -> Session-Data: "Generates"
Session-Data -> rrweb-Tooling.Video-Conversion: "Input"
rrweb-Tooling.Video-Conversion -> Video-File: "Converts to"
```

Explore the guides below to learn how to integrate these tools into your workflow.

<x-cards data-columns="2">
  <x-card data-title="Web Extension" data-icon="lucide:mouse-pointer-click" data-href="/tooling-and-extensions/web-extension">
    Learn how to install and use the browser extension for quick, code-free session recording.
  </x-card>
  <x-card data-title="Video Conversion" data-icon="lucide:film" data-href="/tooling-and-extensions/video-conversion">
    Discover how to convert session recordings into video files using the `rrvideo` command-line tool.
  </x-card>
</x-cards>
