# Use Cases

rrweb is a powerful tool for recording and replaying web interactions, providing a pixel-perfect representation of user sessions. This capability unlocks a wide range of practical applications that can help developers, product managers, and support teams solve real-world problems. This section explores some of the most common and impactful use cases for rrweb.

<x-cards data-columns="2">
  <x-card data-title="Bug Reproduction" data-icon="lucide:bug">
    Accurately capture the sequence of events leading to a bug, eliminating guesswork and dramatically speeding up the debugging process.
  </x-card>
  <x-card data-title="User Behavior Analysis" data-icon="lucide:mouse-pointer-2">
    Gain qualitative insights into how users interact with your application by watching their complete sessions, identifying pain points and opportunities for UX improvements.
  </x-card>
  <x-card data-title="Collaborative Browsing" data-icon="lucide:users">
    Enable real-time session sharing for customer support and co-browsing scenarios, allowing support agents to see exactly what the user sees.
  </x-card>
  <x-card data-title="Video Conversion" data-icon="lucide:video">
    Convert session recordings into standard video formats for easy sharing, creating product demos, or archiving.
  </x-card>
</x-cards>

## Bug Reproduction

One of the most challenging aspects of software development is reproducing bugs that occur only for specific users or under particular conditions. Often, user bug reports lack the necessary context for a developer to understand and fix the issue.

**Solution:** By integrating rrweb into your application, you can automatically record sessions where errors occur. When a user reports a bug, you have a complete, replayable recording of their interactions, console logs, and network requests leading up to the error. This provides an exact, unbiased view of the problem, making it significantly easier to diagnose and resolve.

You can even empower your QA team or end-users with the [rrweb Web Extension](./tooling-and-extensions-web-extension.md) to manually record sessions when they encounter unexpected behavior.

## User Behavior Analysis

Quantitative analytics tools can tell you *what* users are doing (e.g., which pages they visit), but they often can't tell you *why*. Understanding the user journey and identifying points of friction is key to improving user experience and conversion rates.

**Solution:** rrweb allows you to capture and replay entire user sessions, providing a qualitative understanding of user behavior. Product managers and UX designers can watch these recordings to see where users hesitate, where they encounter confusion, or how they interact with new features. This is the core technology behind many session replay and product analytics platforms.

```javascript Recording a session for analysis icon=logos:javascript
rrweb.record({
  emit(event) {
    // Send the event to your analytics backend for storage.
    fetch('/my-analytics-endpoint', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(event),
    });
  },
});
```

## Collaborative Browsing (Co-browsing)

For customer support and sales demonstrations, it's often necessary to guide a user through a website in real-time. Co-browsing solutions allow a support agent or sales representative to view and sometimes interact with the user's web session.

**Solution:** rrweb can be used to stream user events to a support agent's dashboard in real-time instead of storing them. The agent's browser can then use the rrweb replayer to render a live mirror of the user's page. This enables the agent to provide precise, contextual guidance without requiring the user to install any software.

## Creating Demonstrations and Tutorials

Creating video content for product demonstrations, tutorials, or marketing materials can be a time-intensive process. rrweb offers a streamlined way to capture web interactions and convert them into standard video formats.

**Solution:** Use rrweb to record a flawless walkthrough of a feature on your website. Once you have the session data, you can use the [`rrvideo`](./tooling-and-extensions-video-conversion.md) command-line tool to convert the JSON-based recording into a WebM video file. This video can then be easily shared, embedded on a website, or used in presentations.

![Demo of rrvideo converting a session to a video file](../../../packages/rrvideo/demo/demo.gif)

This method is often faster and produces a more precise result than traditional screen recording software.

## Advanced Scenarios

rrweb's capabilities extend to more complex web architectures.

-   **Cross-Origin Iframes:** Record user interactions within embedded content from different domains, which is common in applications that integrate third-party services. For a detailed guide, see the [Cross-Origin Iframes](./advanced-guides-cross-origin-iframes.md) documentation.

By leveraging these use cases, you can enhance your development workflows, gain deeper insights into user behavior, and improve your customer support experience.