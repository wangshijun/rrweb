# Video Conversion

While rrweb's native JSON-based format is optimized for performance and pixel-perfect replay, you may need to convert session recordings into a standard video format for sharing, archiving, or integration with other tools. The `rrvideo` command-line tool is designed specifically for this purpose, transforming rrweb event streams into WebM video files.

![rrvideo Demonstration](https://raw.githubusercontent.com/rrweb-io/rrweb/master/packages/rrvideo/demo/demo.gif)

This guide provides detailed instructions on how to install and use the `rrvideo` CLI.

## Installation

Before you can use `rrvideo`, you must have Node.js installed on your system. Once Node.js is available, you can install the tool globally using npm.

```shell
npm i -g rrvideo
```

This command installs `rrvideo` and makes it available in your system's path.

## Basic Usage

The most straightforward way to use `rrvideo` is to provide an input file containing your rrweb events. The tool will process the events and generate a video file with a default name.

### Convert a Session Recording

To convert a JSON file of rrweb events, use the `--input` flag.

```shell
rrvideo --input ./path/to/your/events.json
```

After the process completes, a file named `rrvideo-output.webm` will be created in the directory where you ran the command.

### Specify an Output Path

You can control the destination and filename of the generated video by using the `--output` flag.

```shell
rrvideo --input events.json --output ./videos/session-replay.webm
```

This command will save the video as `session-replay.webm` inside the `videos` directory.

## Advanced Configuration

For more fine-grained control over the replay and video generation process, you can provide a configuration file using the `--config` flag. This allows you to pass any valid options that the `rrweb-player` component accepts, giving you control over aspects like playback speed, dimensions, and more.

```shell
rrvideo --input events.json --config rrvideo.config.json
```

Here is an example of a `rrvideo.config.json` file:

```json rrvideo.config.json icon=logos:javascript
{
  "width": 1920,
  "height": 1080,
  "speed": 2,
  "mouseTail": false
}
```

In this example, the video will be rendered with a resolution of 1920x1080, played back at twice the original speed, and the mouse tail effect will be disabled. For a full list of available options, please refer to the documentation for the [Player Component](./core-packages-player-component.md).

## Command-Line Options

Here is a summary of the available command-line arguments for `rrvideo`.

| Argument  | Description                                                                 |
| :-------- | :-------------------------------------------------------------------------- |
| `--input` | **Required.** Specifies the path to the input JSON file containing rrweb events. |
| `--output`| Optional. Sets the path and filename for the output video file. Defaults to `rrvideo-output.webm`. |
| `--config`| Optional. Provides the path to a JSON configuration file for advanced `rrweb-player` options. |