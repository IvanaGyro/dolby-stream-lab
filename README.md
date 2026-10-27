# Dolby Stream Lab

A single-page browser test for **Dolby Vision (all profiles)**, **HDR10 / HDR10+**, and **Dolby Atmos (E-AC-3 JOC)**. Works in Chromium browsers (Edge, Chrome), Firefox, and Safari, including iOS/iPadOS via ManagedMediaSource.

## What it does

1. **Capability checks**: reports what the browser claims via `MediaSource.isTypeSupported()` and `navigator.mediaCapabilities.decodingInfo()`.
   - Display: HDR / wide-gamut media queries
   - Base layers (plain HEVC / H.264 / AV1 in HDR10, HLG or SDR), the fallback that is *not* Dolby Vision
   - Dolby Vision profiles 5, 7, 8.1, 8.2, 8.4, 9, 10, 10.1, 10.2, 10.4, each checked for clear, PlayReady SL3000, Widevine and FairPlay
   - Dolby audio: AC-3, E-AC-3 (5.1 / 16 ch), Atmos (`spatialRendering`), AC-4
2. **Stream test**: pick a source, then one video rendition and one audio rendition from that same source, and play them with hls.js. Browsers without MSE fall back to native HLS. For Dolby Vision streams you can declare either the Dolby Vision codec or the plain base codec.
3. **Download**: the full rendition, always:
   - **Video + audio (one file)**: both tracks merged into a single MP4 in the browser, no re-encoding
   - **Video only** / **Audio only**

   Single-file on-demand renditions are fetched in one request; segmented ones are concatenated.
4. **Log**: copy or clear the log. The "Copy media-internals address" button copies the right diagnostics page for the browser (`edge://`, `chrome://`, `opera://media-internals`, or Firefox `about:support#media`). Browsers do not let web pages open these addresses directly.

## Sources

| Source | Content |
|---|---|
| [Apple HLS example `adv_dv_atmos`](https://developer.apple.com/streaming/examples/) | DV P5, HDR10+, HEVC SDR, H.264; AAC, AC-3, E-AC-3 JOC |
| [Dolby OTT reference, Dolby Vision](https://optiview.dolby.com/docs/theoplayer/examples/streaming/dolby-vision/) | P5 (`dvh1`/`dvhe`), P8.1 / P8.4 (`hvc1`/`hev1`), P10 (`dav1`), P10.1 / P10.4 (`av01`), at 25 / 30 / 60 fps |
| [Dolby OTT reference, Atmos](https://optiview.dolby.com/docs/theoplayer/examples/streaming/dolby-atmos/) | H.264 + E-AC-3 16/JOC |

All media is streamed directly from Apple's and Dolby's servers (both send `Access-Control-Allow-Origin: *`). Nothing is hosted here.

## Known findings

- Chromium/Edge report Dolby Vision as supported in `isTypeSupported()`, but `decodingInfo()` only reports support with a DRM key system. Clear Dolby Vision fails to play.
- Apple's HDR10+ renditions fail in Chromium MSE. Their `hvc1` sample entry ends with a 4-byte QuickTime terminator (`00000000`), which is not valid in ISO BMFF.
- Apple's timeline starts at 10 s, so downloaded Apple files also start at 10 s.
- In Edge on Windows, true Atmos object rendering needs the Media Foundation renderer: `--enable-features=MediaFoundationClearPlayback,ForceMFMediaEngineRenderer`.

## Hosting

This is a single `index.html` with no build step. Enable GitHub Pages for the repository (Settings → Pages → Deploy from branch → `main` / root).
