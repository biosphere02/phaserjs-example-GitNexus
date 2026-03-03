# src — audio

# Phaser 3 Audio Module Documentation

The `src/audio` module provides a unified interface for sound playback across different browser environments. It abstracts the complexities of the Web Audio API and HTML5 Audio elements, while also supporting specialized formats like Commodore 64 SID files.

## Overview

The audio system is managed by the Global Sound Manager (`this.sound` within a Scene). It supports three primary operation modes configured via the Game Config:

1.  **Web Audio:** The default high-performance mode. Supports advanced features like spatial audio and precise timing.
2.  **HTML5 Audio:** A fallback mode using standard `<audio>` tags, useful when Web Audio is unavailable or explicitly disabled via `disableWebAudio: true`.
3.  **No Audio:** A silent mode enabled via `noAudio: true`. It maintains API compatibility (functions won't throw errors) but produces no sound, which is useful for server-side environments or testing.

---

## Core Components and Flow

```mermaid
graph TD
    GameConfig[Game Configuration] --> SoundManager[Sound Manager]
    SoundManager -->|Manages| SoundInstance[Sound Instance]
    SoundInstance -->|Uses| Markers[Markers / Audio Sprites]
    SoundManager -->|Events| Scene[Phaser Scene]
    
    subgraph Drivers
        WebAudio[Web Audio API]
        HTML5[HTML5 Audio Tag]
        NoAudio[Silent/Stub]
    }
    SoundManager -.-> Drivers
```

### 1. The Sound Manager
Accessed via `this.sound`, the manager handles global state:
-   **Master Controls:** `mute`, `volume`, `rate`, and `detune`.
-   **Bulk Operations:** `pauseAll()`, `resumeAll()`, and `stopAll()`.
-   **Unlocking:** Modern browsers require a user gesture (like a click) to start audio. The manager handles this via the `unlocked` event.

### 2. Sound Instances
Created using `this.sound.add(key, config)`. Each instance represents a specific sound object that can be played, paused, or manipulated independently of others using the same source asset.

### 3. Audio Sprites and Markers
To optimize loading, multiple sound effects can be combined into a single file.
-   **Audio Sprites:** Loaded via `this.load.audioSprite(key, jsonConfig, audioFiles)`. It uses a JSON spritemap to define named segments.
-   **Markers:** Manually defined segments within a standard sound instance using `soundInstance.addMarker(markerConfig)`.

---

## Key Implementation Patterns

### Gapless Looping (HTML5 Audio)
HTML5 Audio suffers from latency. To achieve gapless loops, the module provides configuration offsets:
```javascript
// Compensation for browser lag during tag playback
this.sound.audioPlayDelay = 0.1;
this.sound.loopEndOffset = 0.05;
```

### Handling Browser Audio Locks
Most examples implement a "Tap to Start" pattern:
```javascript
if (this.sound.locked) {
    this.sound.once('unlocked', () => {
        music.play();
    });
} else {
    music.play();
}
```

### Playback Configuration
The `play` method accepts a `SoundConfig` object to override defaults:
```javascript
music.play({
    loop: true,
    volume: 0.5,
    seek: 2.5,     // Start 2.5 seconds in
    delay: 0,      // Delay before starting
    rate: 1.5      // Playback speed
});
```

---

## Specialized Audio: SID Player
The module supports C64 SID music integration through a plugin architecture (`SIDPlayerPlugin`).
-   **Loading:** Requires SID files to be loaded as binary data.
-   **Sub-tunes:** Many SID files contain multiple tracks. These are navigated using `SIDplayer.loadLocal(sidData, trackIndex)`.
-   **Metadata:** Provides access to original file info like `getauthor()`, `gettitle()`, and `getmodel()`.

---

## Important Events
All sound instances and the manager emit events to synchronize game logic:

| Event | Source | Description |
| :--- | :--- | :--- |
| `play` | Instance | Fired when playback starts. |
| `pause` | Instance | Fired when playback is suspended. |
| `resume` | Instance | Fired when playback resumes from pause. |
| `looped` | Instance | Fired when a sound reaches the end of its loop. |
| `unlocked` | Manager | Fired when the browser audio context is resumed via user gesture. |
| `mute`, `volume` | Both | Fired when the respective property changes. |

## Feature Comparison

| Feature | Web Audio | HTML5 Audio | No Audio |
| :--- | :--- | :--- | :--- |
| **Precision** | High (Sample-accurate) | Low (Browser dependent) | N/A |
| **Multiple Instances** | Excellent | Limited by browser tags | Infinite (Stubs) |
| **Spatial Audio** | Supported | Not supported | Not supported |
| **Dynamic Rate/Detune** | Smooth | Varies by browser | Logic only |
| **Best Use Case** | Games/Apps | Mobile Fallback/Legacy | Server/Testing |