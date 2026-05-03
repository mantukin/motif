# Motif

<p align="center">
  <img src="shared/public/favicon.png" width="128" height="128" alt="Motif Logo">
</p>

A professional tool for music composition and MIDI sequence planning using AI-driven tools. Designed as a lightweight bridge between creative intent and final assembly in your DAW (like Reaper, Ableton, or Logic).

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Version](https://img.shields.io/badge/version-0.4.2-green.svg)
![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS-lightgrey)

## 🎹 Key Features

### Dual Editor System
- **Piano Roll**: A classic, intuitive interface for melodic and harmonic composition with multi-note editing.
- **Drum Step Sequencer**: Specialized grid for rhythmic patterns, optimized for quick drum programming.
- **Voice Melody Editor**: A lyric-aware piano roll for vocal lines, with per-note word and syllable tokens plus bulk lyric assignment.

### Voice Melody + Karaoke
- **Voice Tracks**: Dedicated `Voice` track mode with warm clip styling and editable lyric tokens on each note.
- **Karaoke Strip**: A docked lower-panel line that highlights the currently active lyric tokens across the full vocal timeline.

### Generative AI Tools
- **Humanize**: Add "soul" to your sequences by introducing micro-timing offsets and velocity variations.
- **Euclidean Rhythms**: Generate mathematically perfect rhythmic patterns for complex, world-music-inspired grooves.
- **Melody Generator**: Instantly create melodic ideas within a selected key and scale.
- **Drum Generator**: Procedural generation of drum parts in styles like Rock, House, Hip Hop, Techno, and Trap.

### Audio-to-MIDI Transcription
Powered by **Spotify's Basic Pitch**, the app allows you to transcribe audio files or microphone recordings directly into MIDI notes with high accuracy.

### Advanced MIDI & DAW Integration
- **Full Export**: Export entire projects or individual clips for use in any DAW.
- **Smart Import**: Robust MIDI import with support for drum note remapping (e.g., **EZDrummer 3** maps).
- **UJAM Support**: Specialized MIDI programming logic for UJAM Virtual Instruments (Iron, Rowdy, etc.), utilizing "Player Mode" patterns.

### Local Synth Engine
- Built-in synthesis based on **Tone.js**.
- Over 24+ high-quality presets including Leads, Pads, FX, and Basses.
- Soundfont support for realistic instrument playback.

## 🤖 AI-Powered Workflow

The application is optimized for a collaborative workflow with Large Language Models (ChatGPT, Claude, etc.):

1. **Export Project**: Copy your project's state as a JSON string from the Project Menu.
2. **Context Sharing**: Provide the AI with the project JSON and the `shared/public/AI_INSTRUCTIONS_EN.md` file.
3. **AI Generation**: Ask the AI to modify the structure, compose a melody, or write chord progressions.
   Voice tracks now use `trackType: "voice"` and optional `note.lyric` tokens in JSON.
4. **Instant Import**: Paste the AI-generated JSON back into the app to see and hear the changes immediately.

## 🛠 Technology Stack

- **Frontend**: React 19, TypeScript, Tailwind CSS 4.
- **Audio Engine**: Tone.js (Synthesis & Scheduling), Soundfont-player.
- **Machine Learning**: @spotify/basic-pitch (Audio transcription).
- **Desktop Wrapper**: Tauri v2 (Rust-based backend for filesystem access and native performance).
- **Bundler**: Vite.

## 🚀 Getting Started

### Prerequisites
- Node.js (Latest LTS)
- Rust (Required for building the Tauri desktop app)

### Installation
```bash
npm install
```

### Development
```bash
# Start the web version
npm run dev:web

# Start the desktop Tauri version
npm run dev:desktop
```

### Build
```bash
# Build the web version
npm run build:web

# Build the desktop frontend
npm run build:desktop

# Build the desktop installers
npm run tauri:build --workspace @ai-midi/desktop
```

## ⌨️ Shortcuts

- `Space`: Play / Stop.
- `Ctrl + Z / Ctrl + Shift + Z`: Undo / Redo.
- `Alt + Mouse Wheel`: Vertical zoom in Piano Roll.
- `Shift + Mouse Wheel`: Horizontal scroll.
- `Ctrl + Mouse Wheel`: Horizontal zoom.
- `Ctrl + Drag (on note)`: Duplicate notes (Reaper-style).
- `Alt + Drag (on note)`: Quick velocity adjustment.

## 📄 License

MIT
