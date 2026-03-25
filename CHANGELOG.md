# Changelog

## [Current]

### Added
- **Stop button** — square stop icon resets playback, rewinds to start, parks arm, silences crackle immediately
- **Windows MediaSession integration** — song title, artist, and album art appear in OS media overlay (volume popup, lock screen, etc.); play/pause/previous controls work from OS
- **Album art support** — reads embedded cover art from MP3 (ID3v2), FLAC, M4A, OGG, and OPUS files via jsmediatags; displayed in the spinning label circle, counter-rotated to stay upright
- **Extended format support** — FLAC, M4A, AAC, OPUS, WMA in addition to MP3/WAV/OGG
- **Spin-up sequence** — dropping arm on record triggers 2 seconds of vinyl crackle before music fades in
- **Song-end sequence** — music fades out while crackle fades in simultaneously, 2 seconds of crackle, then silence; arm returns to parked
- **Arm drag-to-start** — drag tonearm onto the record edge to begin playback; arm snaps to outer groove and spin-up starts
- **Parked arm position** — arm rests to the right of the record by default, not on the groove
- **Forward 10s button** — skips ahead 10 seconds; triggers proper song-end sequence if it would go past the end
- **Keyboard shortcuts** — Space (play/pause), ←/→ (seek 5s), ↑/↓ (volume)
- **Replay logic** — pressing play after song ends rewinds to start automatically
- **Track info display** — title and artist parsed from filename (`Title - Artist.ext`) shown in bottom bar

### Changed
- Split monolithic `index.html` into separate files: `css/main.css`, `css/theme-wood.css`, `js/audio.js`, `js/render.js`, `js/player.js`
- Theming separated into swappable theme CSS files
- Tonearm inner boundary set to 30% of record radius — stylus cannot reach the label circle
- Drag interaction simplified: drag only moves arm when not playing; drop on groove snaps to outer start position
- Playhead groove highlight ring removed (was visually confusing)
- Crackle limited to spin-up and song-end sequences only (was continuous throughout playback)
- Record only spins during spin-up and active playback; eases in during spin-up (quadratic)

### Fixed
- Record continuing to spin after keyboard skip to end of song
- Play button not reverting to play icon after song ends via keyboard skip
- Double-firing of song-end handler on replay
- Tonearm needle visually entering the label circle at end of track
- Arm snapping back to parked after drag (was not holding position)
- Crackle gain conflicts between scheduled automations and direct value assignments
