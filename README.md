<p align="center">
  <img src="logo.svg" alt="ClipMaker logo" width="96">
</p>

<h1 align="center">ClipMaker</h1>

Turn a song (or the audio of a video) into a shareable vertical-friendly video clip, entirely in your browser.

ClipMaker is a **single-file web app** (`clipmaker.html`). Pick the part of a track you like (or load a video and use its audio), and it renders an animated card with cover art, title, artist, track details and a live waveform visualizer, then exports it as a video ready to post. Nothing is uploaded: decoding, rendering and encoding all happen locally.

<table>
<tr>
<td><img width="1366" height="641" alt="ClipMaker_1" src="https://github.com/user-attachments/assets/8a7737a9-0532-4bc2-b350-8b13944a5fb8" /></td>
<td><img width="1366" height="641" alt="ClipMaker_2" src="https://github.com/user-attachments/assets/1f197498-74df-4d04-aaf1-a5eb779de048" /></td>
</tr>
</table>

## Features

- **Drag & drop audio or video**: MP3, WAV, M4A, AAC, OGG or FLAC, or a video file (MP4, MOV, WebM, MKV and similar). For a video, only the audio track is extracted and used as the source. Drop a file on the page at any time to swap in a new one, or use **Replace audio**.
- **Automatic metadata**: title, artist and embedded cover art are read from ID3 (MP3), FLAC and MP4/M4A tags. For a video without embedded cover art, a frame from the video is used as the cover. Everything stays editable. Swap in your own cover image, remove the cover, or reload the one embedded in the audio file.
- **Clip selection on a waveform timeline**
  - Drag the amber handles to trim.
  - Drag the highlighted area to slide the whole clip.
  - Click or tap anywhere on the waveform to listen from that spot.
  - Type exact **Start / End** times (millisecond precision).
  - New tracks start with the first 30 s selected. Minimum selection length: 0.3 s.
- **Live preview** rendered on a canvas, with a **seek bar** to scrub inside the selected clip. Tap the card or press <kbd>Space</kbd> to play or pause.
- **Play clip and Loop**: audition the selection with **Play clip**, and switch on **Loop** to repeat it while you fine-tune the handles.
- **Themes**: Amber Glow, Midnight Blue, Mono Noir, Sunset Punch, Neon Pulse. Amber Glow takes its accent color from the cover art.
- **Frame shapes**: 4:5, 1:1, 9:16, 2:3, 3:2, 16:9.
- **Quality**: 720p or 1080p (the long edge of the frame is 1280 or 1920 px).
- **Frame rate**: 30 or 24 fps.
- **Fast, frame-accurate export** to MP4 (H.264 + AAC) where the browser supports WebCodecs, with an automatic real-time fallback elsewhere (see [Browser support](#browser-support)).
- **Audio-only export**: **Export audio** saves just the selected part as an audio file, without rendering a video. It produces an M4A (AAC, 192 kbps, 48 kHz) where the browser can encode AAC, and a lossless 16-bit WAV at the source sample rate otherwise. This also works for tracks loaded from a video, so you can use it to rip the audio of a video clip.
- **Progress and cancel**: a sticky action bar shows the selected length and export progress, and you can cancel an export at any time.
- **Touch friendly**: 44 px hit targets, pointer-capture dragging and a layout that adapts to phones.

https://github.com/user-attachments/assets/fb7f929a-8e0b-4e46-940c-5be24c704916

## Usage

1. Open the app and drop in a track or a video file.
2. Check the title, artist and cover art.
3. Choose the clip on the timeline, using the handles or the time fields. Use **Play clip** and **Loop** to check it.
4. Pick a theme, frame shape, quality and frame rate.
5. Press **Export video**. When it finishes, preview the result and press **Download video**. The file is named `<title> - clip.mp4` (or `.webm` in the fallback mode).
6. Optional: press **Export audio** instead (or as well) to download only the selected audio, named `<title> - audio.m4a` (or `.wav`). The theme, frame shape, quality and frame rate settings don't affect it.

### Keyboard shortcuts

| Key | Action |
| --- | --- |
| <kbd>Space</kbd> | Play / pause the preview. Works anywhere except while typing in a text field or when a button is focused. It also works when a trim handle or the seek bar is focused. Holding the key does not repeat, and it is ignored while exporting or when combined with <kbd>Ctrl</kbd>, <kbd>Cmd</kbd>, <kbd>Alt</kbd> or <kbd>Shift</kbd>. |
| <kbd>←</kbd> <kbd>→</kbd> (or <kbd>↓</kbd> <kbd>↑</kbd>) on a trim handle | Move that handle by 1 s. Click or tap a handle, or <kbd>Tab</kbd> to it, to focus it first. |
| <kbd>Shift</kbd> + arrow | 0.1 s steps |
| <kbd>Alt</kbd> + arrow | 1 ms steps |
| <kbd>PageUp</kbd> / <kbd>PageDown</kbd> on a trim handle or the seek bar | Move or scrub by 10 s |
| <kbd>←</kbd> <kbd>→</kbd> (or <kbd>↓</kbd> <kbd>↑</kbd>), <kbd>Home</kbd>, <kbd>End</kbd> on the seek bar | Scrub within the clip: 1 s steps, <kbd>Shift</kbd> 0.1 s, <kbd>Alt</kbd> 1 ms |

## Running it

No build step and no dependencies to install. Open `clipmaker.html` in a modern browser. The small MP4 muxer it needs ([mp4-muxer](https://github.com/Vanilagy/mp4-muxer) by Vanilagy, MIT) is bundled inside the file.

For the most reliable results, serve it over `http://localhost` instead of opening it from `file://`. Chrome and Edge can misbehave on `file://`, for example with console errors or silent export failures.

```bash
# any of these will do
python -m http.server 8000     # then open http://localhost:8000/clipmaker.html
npx serve
# or use the VS Code "Live Server" extension
```

The Google Fonts stylesheet (Space Grotesk, Inter, JetBrains Mono) is loaded from the web. Without a connection the app still works and falls back to system fonts.

## Browser support

ClipMaker has two export paths and picks the best one automatically.

**1. Offline export (preferred): WebCodecs + MP4 muxer.** Used when the browser provides `VideoEncoder`, `AudioEncoder` and `OfflineAudioContext`, which is the case in current **Chrome** and **Edge**. The selected audio is rendered to 48 kHz stereo, then every video frame is drawn at an exact timestamp and encoded as H.264 (High, Main or Baseline profile, whichever the device supports) with AAC audio, falling back to Opus if AAC is unavailable. The result is always an `.mp4`. Because frames are rendered one by one rather than recorded live, a slow device simply takes longer instead of dropping frames, and the visualizer is derived directly from the audio, so it is identical on every run. The screen is kept awake during the export where the browser allows it.

**2. Real-time fallback: `MediaRecorder` + Web Audio.** Used when WebCodecs is unavailable or the encoder setup fails. The app picks the best supported format, in this order:

1. MP4 (H.264 + AAC)
2. WebM (VP9, VP8 or H.264 + Opus). On phones VP8 is preferred over VP9, which is too heavy for software encoding on mobile.

If neither path can produce a video, the app shows an error instead of exporting.

## How it works

- **Decoding & tags**: `decodeAudioData` for audio (it also pulls the audio track out of video containers), with small built-in parsers for ID3, FLAC and MP4 metadata.
- **Waveform & visualizer**: peaks are precomputed for the timeline. The preview uses a live `AnalyserNode` and a particle effect. The offline export feeds the same visualizer code from the rendered PCM at each frame's time.
- **Rendering**: one shared `renderFrame()` canvas routine draws both the live preview and the exported video, so what you see is what you get. Themes are plain data objects, and the static parts of the card are cached between frames.
- **Export**: see [Browser support](#browser-support). Frame sizes are rounded to even pixel dimensions, as video encoders require.

## Project structure

```
clipmaker.html   # the whole app: HTML, CSS and JavaScript (including the bundled mp4-muxer)
logo.svg         # app icon (same artwork as the in-app header logo)
README.md
```

## Notes & limitations

- In the offline path, export time depends on your device, not on the clip length. In the real-time fallback it takes roughly as long as the clip itself (a 30 s clip takes about 30 s), and you should keep the tab open and in the foreground while it runs.
- Audio-only export is always offline and applies no fades, normalization or effects. The selected range is exported as is.
- Exporting is silent in both paths: audio is recorded into the video but not played through your speakers.
- The output container (MP4 or WebM) depends on what your browser can encode. Offline export always produces MP4.
- Video import only uses the audio: the picture is never part of the exported clip (apart from the optional cover frame). The whole file is read into memory to extract the audio, so very large videos may fail to load; if that happens, trim or compress the video first. A video with no audio track, or with an audio codec your browser cannot decode, cannot be used. MP4, MOV and WebM work best; MKV and others depend on the browser.
- Very long or very high-bitrate files use more memory, since the audio is fully decoded in the browser, and the finished video is held in memory until you download it.
