<p align="center">
  <img src="logo.svg" alt="ClipMaker logo" width="96">
</p>

<h1 align="center">ClipMaker</h1>

Turn a song into a shareable vertical-friendly video clip, entirely in your browser.

ClipMaker is a **single-file web app** (`clipmaker.html`). Pick the part of a track you like, and it renders an animated card with cover art, title, artist, track details and a live waveform visualizer, then exports it as a video ready to post. Nothing is uploaded: decoding, rendering and encoding all happen locally.

<img width="1366" height="641" alt="ClipMaker_1" src="https://github.com/user-attachments/assets/8a7737a9-0532-4bc2-b350-8b13944a5fb8" />

<img width="1366" height="641" alt="ClipMaker_2" src="https://github.com/user-attachments/assets/1f197498-74df-4d04-aaf1-a5eb779de048" />

## Features

- **Drag & drop audio**: MP3, WAV, M4A, OGG or FLAC.
- **Automatic metadata**: title, artist and embedded cover art are read from ID3 (MP3), FLAC and MP4/M4A tags. Everything stays editable. Swap in your own cover image, remove the cover, or reload the one embedded in the audio file.
- **Clip selection on a waveform timeline**
  - Drag the amber handles to trim.
  - Drag the highlighted area to slide the whole clip.
  - Click or tap anywhere on the waveform to listen from that spot.
  - Type exact **Start / End** times (millisecond precision).
  - Minimum selection length: 0.3 s.
- **Live preview** rendered on a canvas, with a **seek bar** to scrub inside the selected clip. Tap the card or press <kbd>Space</kbd> to play or pause.
- **Themes**: Amber Glow, Midnight Blue, Mono Noir, Sunset Punch, Neon Pulse. The accent color can follow the cover art.
- **Frame shapes**: 4:5, 1:1, 9:16, 2:3, 3:2, 16:9.
- **Quality**: 720p or 1080p.
- **Frame rate**: 30 or 24 fps.
- **Optional sound while exporting**, so you can monitor the audio as it is recorded.
- **Touch friendly**: 44 px hit targets, pointer-capture dragging and a layout that adapts to phones.

https://github.com/user-attachments/assets/fb7f929a-8e0b-4e46-940c-5be24c704916

## Usage

1. Open the app and drop in a track.
2. Check the title, artist and cover art.
3. Choose the clip on the timeline, using the handles or the time fields.
4. Pick a theme, frame shape, quality and frame rate.
5. Press export. When it finishes, preview and download the video.

### Keyboard shortcuts

| Key | Action |
| --- | --- |
| <kbd>Space</kbd> | Play / pause the preview. Works anywhere except while typing in a text field or when a button is focused. It also works when a trim handle or the seek bar is focused. Holding the key does not repeat, and it is ignored while exporting. |
| <kbd>←</kbd> <kbd>→</kbd> (or <kbd>↓</kbd> <kbd>↑</kbd>) on a trim handle | Move that handle by 1 s. Click or tap a handle, or <kbd>Tab</kbd> to it, to focus it first. |
| <kbd>Shift</kbd> + arrow | 0.1 s steps |
| <kbd>Alt</kbd> + arrow | 1 ms steps |
| <kbd>PageUp</kbd> / <kbd>PageDown</kbd> on a trim handle or the seek bar | Move or scrub by 10 s |
| <kbd>←</kbd> <kbd>→</kbd> (or <kbd>↓</kbd> <kbd>↑</kbd>), <kbd>Home</kbd>, <kbd>End</kbd> on the seek bar | Scrub within the clip: 1 s steps, <kbd>Shift</kbd> 0.1 s, <kbd>Alt</kbd> 1 ms |

## Running it

No build step and no dependencies to install. Open `clipmaker.html` in a modern browser.

For the most reliable results, serve it over `http://localhost` instead of opening it from `file://`. Chrome and Edge can misbehave on `file://`, for example with console errors or silent export failures.

```bash
# any of these will do
python -m http.server 8000     # then open http://localhost:8000/clipmaker.html
npx serve
# or use the VS Code "Live Server" extension
```

The Google Fonts stylesheet (Space Grotesk, Inter, JetBrains Mono) is loaded from the web. Without a connection the app still works and falls back to system fonts.

## Browser support

Export uses the browser's `MediaRecorder` API plus the Web Audio API, and the app picks the best supported format automatically, in this order:

1. MP4 (H.264 + AAC)
2. WebM (VP9, VP8 or H.264 + Opus)

The latest **Chrome** or **Edge** is recommended. If no supported codec is available the app shows an error instead of exporting.

## How it works

- **Decoding & tags**: `decodeAudioData` for audio, with small built-in parsers for ID3, FLAC and MP4 metadata.
- **Waveform & visualizer**: peaks are precomputed for the timeline. The preview and export use a live `AnalyserNode` and a particle effect.
- **Rendering**: one shared `renderFrame()` canvas routine draws both the live preview and the exported video, so what you see is what you get. Themes are plain data objects.
- **Export**: the canvas stream and the audio are recorded together with `MediaRecorder` in real time, so exporting takes about as long as the clip itself. Keep the tab open while it runs.

## Project structure

```
clipmaker.html   # the whole app: HTML, CSS and JavaScript
logo.svg         # app icon (same artwork as the in-app header logo)
README.md
```

## Notes & limitations

- Export is real-time, so a 30 s clip takes roughly 30 s to render.
- The output container (MP4 or WebM) depends on what your browser can record.
- Very long or very high-bitrate files use more memory, since the audio is fully decoded in the browser.
