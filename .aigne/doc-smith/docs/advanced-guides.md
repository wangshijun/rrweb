# Advanced Guides

Once you have mastered the basics of recording and replaying, you may encounter more complex scenarios that require specialized configurations. This section provides detailed guides for advanced use cases and optimization strategies. Here, we will discuss performance tuning, efficient storage management, the intricacies of recording across cross-origin iframes, and various techniques for capturing canvas content.

These guides are designed for developers looking to tailor rrweb to specific, demanding environments and to get the most out of its capabilities.

```d2
direction: down

advanced-guides: {
  label: "rrweb\nAdvanced Guides"
  shape: rectangle
}

topics: {
  style.stroke-width: 0
  grid-columns: 3

  performance-storage: {
    label: "Performance and Storage"
    shape: rectangle
  }

  cross-origin-iframes: {
    label: "Cross-Origin Iframes"
    shape: rectangle
  }

  canvas-recording: {
    label: "Canvas Recording"
    shape: rectangle
  }
}

advanced-guides -> topics.performance-storage
advanced-guides -> topics.cross-origin-iframes
advanced-guides -> topics.canvas-recording
```

<x-cards data-columns="3">
  <x-card data-title="Performance and Storage" data-icon="lucide:gauge" data-href="/advanced-guides/performance-and-storage">
    Learn strategies to optimize recording performance and minimize data storage requirements. This guide covers event sampling, compression, and deduplication techniques.
  </x-card>
  <x-card data-title="Cross-Origin Iframes" data-icon="lucide:webhook" data-href="/advanced-guides/cross-origin-iframes">
    Understand the necessary setup and security considerations for recording user sessions that span across different domains within iframes.
  </x-card>
  <x-card data-title="Canvas Recording" data-icon="lucide:image" data-href="/advanced-guides/canvas-recording">
    Explore the available methods for recording HTML canvas content, including periodic image snapshots and real-time streaming options.
  </x-card>
</x-cards>

Each guide provides practical code examples and step-by-step instructions to help you implement these advanced features correctly and securely.
