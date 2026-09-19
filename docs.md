# React Native Torrent Streamer - Documentation

## Overview

React Native Torrent Streamer is a recently updated library that enables torrent streaming functionality in React Native applications. This library allows you to stream video content directly from torrent magnets or .torrent files.

**Platform Support:** Android only

## Installation

### Step 1: Install the package

```bash
npm install react-native-torrent-streamer
# or
yarn add react-native-torrent-streamer
```

### Step 2: Android Configuration

Add the JitPack repository to your `android/build.gradle` file:

```gradle
allprojects {
  repositories {
    maven { url("https://jitpack.io") }
  }
}
```

### Step 3: Link dependencies (for React Native < 0.60)

If you're using React Native 0.60 or higher, autolinking will handle this automatically. For older versions:

```bash
react-native link react-native-torrent-streamer
```

## API Reference

### Methods

#### `start(magnetUrl)`

Starts streaming from a torrent magnet URL or .torrent file URL.

**Parameters:**
- `magnetUrl` (string): The magnet URI or torrent file URL to stream

**Example:**
```javascript
TorrentStreamer.start('magnet:?xt=urn:btih:88594aaacbde40ef3e2510c47374ec0aa396c08e&dn=bbb_sunflower_1080p_30fps_normal.mp4')
```

#### `stop()`

Stops the current torrent stream and cleans up resources.

**Example:**
```javascript
TorrentStreamer.stop()
```

#### `open(url, mimeType)`

Opens the streamed content with the specified MIME type. Typically called after the 'ready' event.

**Parameters:**
- `url` (string): The URL of the streamed content
- `mimeType` (string): The MIME type of the content (e.g., 'video/mp4')

**Example:**
```javascript
TorrentStreamer.open(data.url, 'video/mp4')
```

#### `addEventListener(eventType, callback)`

Registers an event listener for torrent stream events.

**Parameters:**
- `eventType` (string): The type of event to listen for
  - `'error'`: Triggered when an error occurs
  - `'status'`: Triggered when download status updates
  - `'ready'`: Triggered when the stream is ready to play
  - `'stop'`: Triggered when the stream stops
- `callback` (function): The function to call when the event occurs

**Example:**
```javascript
TorrentStreamer.addEventListener('error', this.onError)
TorrentStreamer.addEventListener('status', this.onStatus)
TorrentStreamer.addEventListener('ready', this.onReady)
TorrentStreamer.addEventListener('stop', this.onStop)
```

#### `removeEventListener(eventType, callback)`

Removes a previously registered event listener.

**Parameters:**
- `eventType` (string): The type of event
- `callback` (function): The callback function to remove

**Example:**
```javascript
TorrentStreamer.removeEventListener('error', this.onError)
```

### Events

#### `error`

Triggered when an error occurs during streaming.

**Callback Parameter:**
- `error` (object): Error information

**Example:**
```javascript
onError = (error) => {
  console.log('Error:', error)
}
```

#### `status`

Triggered periodically with download progress updates.

**Callback Parameter:**
- `progress` (number): Download progress (0-1)
- `buffer` (number): Buffer level
- `downloadSpeed` (number): Current download speed in bytes/second
- `seeds` (number): Number of connected seeds

**Example:**
```javascript
onStatus = ({ progress, buffer, downloadSpeed, seeds }) => {
  this.setState({
    progress,
    buffer,
    downloadSpeed,
    seeds
  })
}
```

#### `ready`

Triggered when the stream is ready to be played.

**Callback Parameter:**
- `data` (object): Contains the `url` property with the local streaming URL

**Example:**
```javascript
onReady = (data) => {
  TorrentStreamer.open(data.url, 'video/mp4')
}
```

#### `stop`

Triggered when the stream has been stopped.

**Callback Parameter:**
- `data` (object): Stop event data

**Example:**
```javascript
onStop = (data) => {
  console.log('Stream stopped')
}
```

## Complete Usage Example

```javascript
import React, { Component } from 'react';
import { View, Text, TouchableHighlight, StyleSheet } from 'react-native';
import TorrentStreamer from 'react-native-torrent-streamer';

export default class App extends Component {
  state = {
    progress: 0,
    buffer: 0,
    downloadSpeed: 0,
    seeds: 0
  }

  componentDidMount() {
    TorrentStreamer.addEventListener('error', this.onError)
    TorrentStreamer.addEventListener('status', this.onStatus)
    TorrentStreamer.addEventListener('ready', this.onReady)
    TorrentStreamer.addEventListener('stop', this.onStop)
  }

  componentWillUnmount() {
    TorrentStreamer.removeEventListener('error', this.onError)
    TorrentStreamer.removeEventListener('status', this.onStatus)
    TorrentStreamer.removeEventListener('ready', this.onReady)
    TorrentStreamer.removeEventListener('stop', this.onStop)
  }

  onError = (error) => {
    console.log('Error:', error)
  }

  onStatus = ({ progress, buffer, downloadSpeed, seeds }) => {
    this.setState({
      progress,
      buffer,
      downloadSpeed,
      seeds
    })
  }

  onReady = (data) => {
    // Open the video player with the streamed URL
    TorrentStreamer.open(data.url, 'video/mp4')
  }

  onStop = (data) => {
    console.log('Stream stopped')
  }

  handleStart = () => {
    TorrentStreamer.start('magnet:?xt=urn:btih:88594aaacbde40ef3e2510c47374ec0aa396c08e&dn=bbb_sunflower_1080p_30fps_normal.mp4&tr=udp%3A%2F%2Ftracker.openbittorrent.com%3A80%2Fannounce')
  }

  handleStop = () => {
    this.setState({
      progress: 0,
      buffer: 0,
      downloadSpeed: 0,
      seeds: 0
    }, () => {
      TorrentStreamer.stop()
    })
  }

  render() {
    const { progress, buffer, downloadSpeed, seeds } = this.state

    return (
      <View style={styles.container}>
        <TouchableHighlight style={styles.button} onPress={this.handleStart}>
          <Text>Start Torrent!</Text>
        </TouchableHighlight>

        <TouchableHighlight style={styles.button} onPress={this.handleStop}>
          <Text>Stop Torrent!</Text>
        </TouchableHighlight>

        {buffer ? <Text>Buffer: {buffer}</Text> : null}
        {downloadSpeed ? <Text>Download Speed: {(downloadSpeed / 1024).toFixed(2)} KB/s</Text> : null}
        {progress ? <Text>Progress: {(progress * 100).toFixed(2)}%</Text> : null}
        {seeds ? <Text>Seeds: {seeds}</Text> : null}
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  button: {
    padding: 15,
    margin: 10,
    backgroundColor: '#007AFF',
    borderRadius: 5,
  }
})
```

## Troubleshooting

### Common Issues

1. **Build errors on Android**
   - Ensure you've added the JitPack repository to your `android/build.gradle`
   - Make sure you're using compatible versions of React Native (>=0.70.0)

2. **Stream not starting**
   - Verify the magnet URL is valid
   - Check if you have internet connectivity
   - Ensure the torrent has active seeds

3. **No video playback**
   - Make sure you're calling `TorrentStreamer.open()` after receiving the 'ready' event
   - Verify the MIME type matches your video format

## Requirements

- React Native >= 0.70.0
- React >= 18.2.0
- Android SDK >= 21
- Android Gradle Plugin >= 8.1.0

## License

MIT
