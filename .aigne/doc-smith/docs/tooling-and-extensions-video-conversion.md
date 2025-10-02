# Video Conversion

While rrweb's native JSON-based format is efficient for storage and provides pixel-perfect replay, there are scenarios where a standard video file is more convenient for sharing or archiving. The `rrvideo` command-line tool is designed specifically for this purpose, allowing you to convert rrweb session recordings into WebM video files.

![Demo of rrvideo converting a session to a video](../../../packages/rrvideo/demo/demo.gif)

This guide will walk you through the installation and usage of `rrvideo`.

## Installation

Before using `rrvideo`, you need to install it globally via npm. Ensure you have Node.js installed on your system first.

1.  **Install Node.js**: If you don't have it, download and install it from the [official Node.js website](https://nodejs.org/en/download/).
2.  **Install rrvideo**: Open your terminal and run the following command to install the CLI tool globally.

```shell Install rrvideo icon=logos:npm
npm i -g rrvideo
```

This command also installs a compatible version of Playwright and a headless browser, which are used under the hood to perform the conversion.

## Basic Usage

The simplest way to use `rrvideo` is to provide it with the path to your rrweb events file. The events file should be a JSON array of rrweb events.

### Convert a Session

Run the following command, replacing `events.json` with the path to your file:

```shell Basic Conversion
rrvideo --input events.json
```

By default, this will generate a video file named `rrvideo-output.webm` in the directory where you run the command.

### Specify an Output Path

You can specify a different name or location for the output video file using the `--output` flag.

```shell Specify Output Path
rrvideo --input events.json --output ./converted/session.webm
```

## Advanced Configuration

For more control over the replay behavior and video output, you can provide a configuration file. This allows you to pass any valid `rrweb-player` options to customize the appearance and playback during the conversion process.

### Using a Configuration File

Create a JSON configuration file (e.g., `rrvideo.config.json`) and pass its path to the CLI using the `--config` flag.

```shell Conversion with Config File
rrvideo --input events.json --config rrvideo.config.json
```

Here is an example configuration file that adjusts the player's dimensions, playback speed, and mouse cursor appearance.

```json rrvideo.config.example.json icon=logos:javascript
{
  "width": 1400,
  "height": 900,
  "speed": 4,
  "skipInactive": true,
  "mouseTail": {
    "strokeStyle": "green",
    "lineWidth": 2
  }
}
```

This configuration will produce a video with a resolution of 1400x900, replayed at 4x speed, skipping any inactive periods, and rendering a green tail on the mouse cursor.

## Programmatic Usage

In addition to the CLI, `rrvideo` can be used programmatically within your Node.js applications. This is useful for integrating video conversion into an automated workflow.

First, install `rrvideo` as a local dependency in your project:

```shell Install Local Dependency icon=logos:npm
npm install rrvideo
```

Then, you can import and use the `transformToVideo` function.

```javascript transform.js icon=logos:javascript
import { transformToVideo } from 'rrvideo';
import path from 'path';

async function convert() {
  try {
    const outputPath = await transformToVideo({
      input: './path/to/events.json',
      output: './path/to/output.webm',
      onProgressUpdate: (percent) => {
        console.log(`Conversion progress: ${Math.round(percent * 100)}%`);
      },
      rrwebPlayer: {
        speed: 2,
        width: 1920,
        height: 1080,
      },
    });
    console.log(`Successfully transformed into "${outputPath}".`);
  } catch (error) {
    console.error('Failed to transform this session:', error);
    process.exit(1);
  }
}

convert();
```

### `transformToVideo` Parameters

The `transformToVideo` function accepts a configuration object with the following properties:

<x-field-group>
  <x-field data-name="input" data-type="string" data-required="true" data-desc="The absolute or relative path to the input rrweb events JSON file."></x-field>
  <x-field data-name="output" data-type="string" data-required="false" data-default="rrvideo-output.webm">
    <x-field-desc markdown>The output path for the generated video file. Defaults to `rrvideo-output.webm` in the current working directory.</x-field-desc>
  </x-field>
  <x-field data-name="headless" data-type="boolean" data-required="false" data-default="true">
    <x-field-desc markdown>Whether to run the headless browser in headless mode. Set to `false` for debugging.</x-field-desc>
  </x-field>
  <x-field data-name="resolutionRatio" data-type="number" data-required="false" data-default="0.8">
    <x-field-desc markdown>A number between 0 and 1. The higher the value, the better the quality of the video. This is a trade-off between quality and file size.</x-field-desc>
  </x-field>
  <x-field data-name="onProgressUpdate" data-type="(percent: number) => void" data-required="false">
    <x-field-desc markdown>A callback function that will be called with the conversion progress, where `percent` is a number from 0 to 1.</x-field-desc>
  </x-field>
  <x-field data-name="rrwebPlayer" data-type="object" data-required="false">
    <x-field-desc markdown>An object containing configuration options to be passed to the underlying `rrweb-player` instance. See the [Player Component](./core-packages-player-component.md) documentation for all available options.</x-field-desc>
  </x-field>
</x-field-group>

## Summary

The `rrvideo` tool provides a straightforward way to convert rrweb sessions into standard video files, either through an easy-to-use command-line interface or a programmatic API for more complex integrations. By leveraging `rrweb-player` configurations, you can precisely control the output to meet your needs.