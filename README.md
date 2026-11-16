<p align="center">
  <img src="logo.svg" alt="ClipMaker logo" width="96">
</p>

<h1 align="center">ClipMaker</h1>

Turn a song (or the audio of a video) into a shareable vertical-friendly video clip, entirely in your browser.

ClipMaker is a **single-file web app** (`clipmaker.html`). Pick the part of a track you like (or load a video and use its audio), and it renders either an animated card with cover art, title, artist, track details and a live waveform, a full-screen **word-by-word synced lyrics** view, or a playful **Bubble Game** layout where each word or line floats by as an emoji bubble, then exports it as a video ready to post. Nothing is uploaded: decoding, rendering and encoding all happen locally.

<table>
<tr>
<td><img width="1366" height="641" alt="ClipMaker_1" src="https://github.com/user-attachments/assets/8a7737a9-0532-4bc2-b350-8b13944a5fb8" /></td>
<td><img width="1366" height="641" alt="ClipMaker_2" src="https://github.com/user-attachments/assets/1f197498-74df-4d04-aaf1-a5eb779de048" /></td>
</tr>
</table>

## Features

- **Drag & drop audio or video**: MP3, WAV, M4A, AAC, OGG or FLAC, or a video file (MP4, MOV, WebM, MKV and similar). For a video, only the audio track is extracted and used as the source. Drop a file on the page at any time to swap in a new one, or use **Replace audio**.
- **Automatic metadata**: title, artist, embedded cover art and embedded lyrics are read from ID3 (MP3), FLAC and MP4/M4A tags. For a video without embedded cover art, a frame from the video is used as the cover. Everything stays editable. Swap in your own cover image, remove the cover, or reload the one embedded in the audio file.
- **Clip selection on a waveform timeline**
  - Drag the amber handles to trim.
  - Drag the highlighted area to slide the whole clip.
  - Click or tap anywhere on the waveform to listen from that spot.
  - Type exact **Start / End** times (millisecond precision).
  - Press **Select all** (next to the "Selected … of …" label) to use the whole track as the clip.
  - New tracks start with the first 30 s selected. Minimum selection length: 0.3 s.
- **Live preview** rendered on a canvas, with a **seek bar** to scrub inside the selected clip. Tap the card or press <kbd>Space</kbd> to play or pause. Use the corner button for a **fullscreen preview** (press <kbd>Esc</kbd> or the same button to leave). The preview is rendered at a higher resolution in fullscreen so it stays sharp. Browsers without the Fullscreen API for elements, such as iPhone Safari, get a full-window overlay instead.
- **Play clip and Loop**: audition the selection with **Play clip**, and switch on **Loop** to repeat it while you fine-tune the handles.
- **Three video layouts**
  - **Classic Card**: cover art, title, artist, track details and a live waveform visualizer.
  - **Full Lyrics**: synced lyrics fill the frame, with the title, artist and cover in a compact header. See [Full Lyrics layout](#full-lyrics-layout).
  - **Bubble Game**: lyrics float across the screen as cute emoji (or your own PNG/SVG) bubbles with word- or line-level text bubbles, like a rhythm game. See [Bubble Game layout](#bubble-game-layout).
- **Video as cover** (off by default): when you load a video, a **Use video as cover** toggle appears under *Style and format*. When on, the actual video plays inside the cover slot, in the preview and in the exported clip, instead of a still frame. The still cover is still used for the background glow. In the offline export every frame is seeked exactly, so this makes the export slower than a still cover. Removing the cover turns the option off.
- **Themes**: Amber Glow, Midnight Blue, Mono Noir, Sunset Punch, Neon Pulse. Amber Glow takes its accent color from the cover art.
- **Typography**: Space Grotesk, Inter, JetBrains Mono, Serif Editorial, or your own font file (Custom Font).
- **Frame shapes**: 4:5, 1:1, 9:16, 2:3, 3:2, 16:9.
- **Quality**: 720p or 1080p (the long edge of the frame is 1280 or 1920 px).
- **Frame rate**: 60, 30 or 24 fps (Bubble Game switches to 60 fps automatically).
- **Fast, frame-accurate export** to MP4 (H.264 + AAC) where the browser supports WebCodecs, with an automatic real-time fallback elsewhere (see [Browser support](#browser-support)). The progress label shows the rendering speed and an estimated time left.
- **Audio-only export**: **Export audio** saves just the selected part as an audio file, without rendering a video. It produces an M4A (AAC, 192 kbps, 48 kHz) where the browser can encode AAC, and a lossless 16-bit WAV at the source sample rate otherwise. This also works for tracks loaded from a video, so you can use it to rip the audio of a video clip.
- **Progress and cancel**: a sticky action bar shows the selected length and export progress, and you can cancel an export at any time.
- **Touch friendly**: 44 px hit targets, pointer-capture dragging and a layout that adapts to phones.

https://github.com/user-attachments/assets/fb7f929a-8e0b-4e46-940c-5be24c704916

## Full Lyrics layout

Choose **Full Lyrics** under *Video layout*. Loading lyrics in step 3 switches to it automatically when you are on Classic Card (it never overrides Bubble Game).

- **Lyrics formats**: TTML (`.ttml`, `.xml`) with line or word-level timing, Enhanced LRC (`[mm:ss.xx] <mm:ss.xx>word …`) and standard line-level LRC (`.lrc`, `.elrc`, `.txt`). Load a file, drop one on the lyrics box, or use **Edit / Paste**. Lyrics embedded in the audio tags are picked up automatically. Line-level lyrics get their word timings interpolated across the line.
- **Word-by-word highlighting**: the singing word sweeps to the accent color with a soft glow and a small lift. Background vocals (`x-bg` in TTML) show smaller under their main line and fade in and out with the singer. Turn **Word highlight** off to simply light up the whole current line, which suits lyrics with line-level timestamps.
- **Accurate current line**: a line stays lit only for its own time range and dims at its end time (the end of its last word in TTML), instead of waiting for the next line to start.
- **Smooth auto scroll**: lyrics glide from line to line with eased motion that is computed from the playback time alone, so the preview, seeking and the exported video always match. The active line eases up in size, and instrumental gaps and the intro show three pulsing dots that fade in and out. The top and bottom edges of the lyrics area fade out softly under the header and above the footer, so lines are never cut off sharply.
- **Timing offset**: shift all lyrics earlier or later in 0.05 s steps (±5 s) to fix sync.
- **Song part labels**: show or hide small labels such as VERSE or CHORUS above the first line of each section (*Song part labels*, on by default). They are read from `[lrc:songPart:Verse]` or `[songPart:Verse]` tags in LRC (on their own line before a section, or on the same line as a timestamped lyric) and from the `itunes:song-part` attribute on TTML `<div>` elements. Names like `PreChorus` are shown as "Pre Chorus". If the lyrics have no part tags, nothing is shown.
- **Style options**: text size (Small, Normal, Large), alignment (left, center, right) and **Lyrics edge blur**, which softly blurs the lines toward the top and bottom of the lyrics area, **Word highlight** (On/Off) and **Song part labels** (Show/Hide).
- **Progress waveform**: a slim waveform at the bottom shows playback progress. In landscape and wide frames it uses more, thinner bars.

## Bubble Game layout

Choose **Bubble Game** under *Video layout*. It needs lyrics (TTML, Enhanced LRC or LRC, see [Full Lyrics layout](#full-lyrics-layout)) and uses the same timing offset.

- **How it plays**: every word (or line) appears as a round, glassy bubble with an icon, and a speech bubble with the text below it. All bubbles travel at the same constant speed and pass a dashed **hit line** exactly when their word starts. At that moment the bubble hops, sparkles, and its speech bubble lights up in the accent color, so upcoming words are always visible coming in. Heights (or horizontal positions) are random-looking but fixed per word, and everything is computed from the playback time alone, so the preview, seeking and the exported video always match.
- **Bubble direction**: Right → Left (default), Left → Right, Top → Bottom or Bottom → Top. Vertical directions stay above the bottom HUD and fade softly at its edge.
- **Bubble text**: **Words** (word-level bubbles; line-level lyrics get interpolated word timings) or **Lines** (whole lines, wrapped up to three rows).
- **Bubble icons**
  - **Emoji**: type your own list in the text field, separated by spaces (ZWJ and skin-tone emoji work too). Icons are picked pseudo-randomly per word.
  - **Custom PNG / SVG**: press **Upload PNG / SVG icons** and pick one or more files (PNG, SVG, WebP or JPEG). They are used in rotation instead of emoji. SVGs without a pixel size are handled automatically. Press the ✕ on the icon count to remove them.
- **HUD**: cover thumbnail, title and artist, a ⭐ score counting the words passed so far, and a level-style progress bar with a star at its end.
- **Shared options**: theme, text size, typography (including a custom font), background blur, frame shape and quality all apply. Lyrics edge blur, Word highlight and Song part labels are Full Lyrics options and are hidden here.
- **Smooth motion**: constant-speed motion looks choppy at 24/30 fps, so choosing Bubble Game switches the frame rate to 60 fps (you can change it back). Its effects use crisp halos and shadows instead of soft blurs, and the offline export uses a higher bitrate for this layout (8 Mbps at 720p and 14 Mbps at 1080p, 1.5× at 60 fps) so fast-moving bubbles don't smear.

## Usage

1. Open the app and drop in a track or a video file.
2. Check the title, artist and cover art.
3. Choose the clip on the timeline, using the handles, the time fields or **Select all**. Use **Play clip** and **Loop** to check it.
4. Optional: load lyrics (TTML or LRC) for the Full Lyrics layout.
5. Pick a layout (for Bubble Game also the direction, text unit and icons), theme, frame shape, quality and frame rate.
6. Press **Export video**. When it finishes, preview the result and press **Download video**. The file is named `<title> - clip.mp4` (or `.webm` in the fallback mode).
7. Optional: press **Export audio** instead (or as well) to download only the selected audio, named `<title> - audio.m4a` (or `.wav`). The layout, theme, frame shape, quality and frame rate settings don't affect it.

### Keyboard shortcuts

| Key | Action |
| --- | --- |
| <kbd>Space</kbd> | Play / pause the preview. Works anywhere except while typing in a text field or when a button is focused. It also works when a trim handle or the seek bar is focused. Holding the key does not repeat, and it is ignored while exporting or when combined with <kbd>Ctrl</kbd>, <kbd>Cmd</kbd>, <kbd>Alt</kbd> or <kbd>Shift</kbd>. |
| <kbd>Esc</kbd> | Leave the fullscreen preview. |
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

**1. Offline export (preferred): WebCodecs + MP4 muxer.** Used when the browser provides `VideoEncoder`, `AudioEncoder` and `OfflineAudioContext`, which is the case in current **Chrome** and **Edge**. The selected audio is rendered to 48 kHz stereo, then every video frame is drawn at an exact timestamp and encoded as H.264 (High, Main or Baseline profile, with a level that fits the frame size, whichever the device supports) with AAC audio, falling back to Opus if AAC is unavailable. Hardware encoding is preferred when the device offers it. The result is always an `.mp4`. Because frames are rendered one by one rather than recorded live, a slow device simply takes longer instead of dropping frames, and the visualizer is derived directly from the audio, so it is identical on every run. The render loop waits for the encoder to catch up before drawing the next frame, so raw frames never pile up in memory. The screen is kept awake during the export where the browser allows it.

**2. Real-time fallback: `MediaRecorder` + Web Audio.** Used when WebCodecs is unavailable or the encoder setup fails. The app picks the best supported format, in this order:

1. MP4 (H.264 + AAC)
2. WebM (VP9, VP8 or H.264 + Opus). On phones VP8 is preferred over VP9, which is too heavy for software encoding on mobile.

If neither path can produce a video, the app shows an error instead of exporting.

## How it works

- **Decoding & tags**: `decodeAudioData` for audio (it also pulls the audio track out of video containers), with small built-in parsers for ID3, FLAC and MP4 metadata.
- **Waveform & visualizer**: peaks are precomputed for the timeline. The preview uses a live `AnalyserNode`. The offline export feeds the same visualizer code from the rendered PCM at each frame's time.
- **Lyrics**: TTML and (enhanced) LRC are parsed into lines of timed words. The Full Lyrics renderer works out the scroll position, line sizes and word highlighting purely from the playback time, and caches word measurements between frames. The Bubble Game renderer likewise derives every bubble's position, pulse and highlight from the playback time and caches the word list.
- **Rendering**: one shared `renderFrame()` canvas routine (dispatching to the Classic, Full Lyrics or Bubble Game renderer) draws both the live preview and the exported video, so what you see is what you get. Themes are plain data objects, and the static parts of the card are cached between frames.
- **Export**: see [Browser support](#browser-support). Frame sizes are rounded to even pixel dimensions, as video encoders require.

## Project structure

```
clipmaker.html   # the whole app: HTML, CSS and JavaScript (including the bundled mp4-muxer)
logo.svg         # app icon (same artwork as the in-app header logo)
README.md
```

## Notes & limitations

- In the offline path, export time depends on your device, not on the clip length. The Full Lyrics layout at 1080p is the heaviest to render, and 60 fps doubles the number of frames to render and encode compared with 30 fps. In the real-time fallback it takes roughly as long as the clip itself (a 30 s clip takes about 30 s), and you should keep the tab open and in the foreground while it runs.
- Bubble Game in the real-time fallback (`MediaRecorder`) can drop frames on slower devices, which shows up as choppy motion. Prefer a browser with WebCodecs (Chrome or Edge) for this layout. In fast passages, word-level bubbles can partly overlap.
- Audio-only export is always offline and applies no fades, normalization or effects. The selected range is exported as is.
- Exporting is silent in both paths: audio is recorded into the video but not played through your speakers.
- The output container (MP4 or WebM) depends on what your browser can encode. Offline export always produces MP4.
- Video import only uses the audio unless you switch on **Use video as cover**: otherwise the picture is never part of the exported clip (apart from the cover frame). The whole file is read into memory to extract the audio, so very large videos may fail to load; if that happens, trim or compress the video first. A video with no audio track, or with an audio codec your browser cannot decode, cannot be used. MP4, MOV and WebM work best; MKV and others depend on the browser.
- Very long or very high-bitrate files use more memory, since the audio is fully decoded in the browser, and the finished video is held in memory until you download it. The MP4 is assembled in memory and then copied into a download file, so very long 1080p clips can briefly need about twice the file size.
- Song part labels need tags in the lyrics file. A repeated LRC part tag on every line shows a label only where the section changes, so two back-to-back LRC sections with the same name show one label.
