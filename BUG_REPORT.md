# Bug Report

## 1) Invalid HTML document skeleton
The file starts directly with `<meta>`, `<title>`, and `<style>` without opening `<html>` and `<head>` tags, while closing `</body>` and `</html>` tags are present at the end. This creates malformed markup and can trigger inconsistent parsing behavior across browsers/tools.

## 2) Slicer playback length is ignored
`playBufferSource(deck, startTime, duration, loop)` receives a `duration` argument, but for non-looping playback it calls `src.start(0, startTime)` without the third `duration` parameter. As a result, Slicer pads play from the slice start all the way to track end instead of playing only one slice.

## 3) Slicer threshold updater can throw before audio init
`updateSlicerThreshold()` writes to `decks[0].sliceThreshold` and `decks[1].sliceThreshold` unconditionally. Before `initAudio()` runs, both entries are `null`, so calling this function early throws `Cannot set properties of null`.

## 4) Repeated global mouseup listeners per pad
`createPads()` attaches `window.addEventListener('mouseup', onUp)` once per pad (16 total). This causes unnecessary global listeners and repeated handler execution on every mouseup.

## 5) Object URLs are never revoked when loading tracks
`loadTrack()` creates object URLs via `URL.createObjectURL(file)` and assigns them to `<audio>`, but does not call `URL.revokeObjectURL()` for previously loaded files. Repeated track loads can leak memory.
