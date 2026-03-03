# assets — particles

# Assets — Particles Module

The `assets/particles` module contains the texture atlases and emitter configurations required for the game's visual effects system. These assets are specifically formatted for the **Phaser 3** game engine, utilizing JSON hash/array formats for texture packing and a standardized JSON structure for particle emitter properties.

## Module Components

The module consists of three primary types of files:
1.  **Texture Atlases**: JSON metadata describing how individual sprites are packed into a single `.png` sheet.
2.  **Emitter Configurations**: Pre-defined property sets for particle behavior.
3.  **Source Project Files**: `.tps` files for [TexturePacker](https://www.codeandweb.com/texturepacker).

### Texture Atlases

These atlases define the visual frames available for particle effects. Each JSON refers to a corresponding PNG of the same name.

#### 1. Bubbles (`/assets/particles/bubbles.json`)
Despite the name, this atlas serves as a general-purpose effects collection.
*   **Key Frames**: 
    *   **Bubbles**: `bluebubble`, `greenbubble`, `redbubble`, `silverbubble` (64x64).
    *   **Electricity**: `elec1` through `elec4` (various sizes, up to 204x185).
    *   **Misc**: `coin` (64x63).
*   **Format**: Phaser-compatible JSON.

#### 2. Explosion (`/assets/particles/explosion.json`)
Contains 10 high-resolution (128x128) frames for impact and fire effects.
*   **Key Frames**: `cloud`, `muzzleflash1-7`, `smoke-puff`, `smoke0`, `stone`, `white-smoke`, `red`.

#### 3. Flares (`/assets/particles/flares.json`)
Simple 128x128 radial glows used for trails, points of interest, or additive blending effects.
*   **Key Frames**: `blue`, `green`, `red`, `white`, `yellow`.

---

### Emitter Configuration (`/assets/particles/emitter.json`)

This file contains a serialised object for a `Phaser.GameObjects.Particles.ParticleEmitter`. It allows developers to modify particle behavior (physics, lifespan, alpha) without changing source code.

**Key Default Properties:**
*   **Gravity**: `gravityY: 200`
*   **Lifespan**: `1000ms` to `2000ms`
*   **Speed**: `400`
*   **Angle**: `140` to `180` (directional burst)
*   **Blend Mode**: `1` (Additive)

---

## Integration Guide

To use these assets within a Phaser Scene, follow the standard loading and instantiation pattern.

### Loading Assets
```javascript
// Within a Phaser.Scene's preload() method
this.load.atlas('particles', '/assets/particles/bubbles.png', '/assets/particles/bubbles.json');
this.load.json('emitterConfig', '/assets/particles/emitter.json');
```

### Implementing an Emitter
```javascript
// Within a Phaser.Scene's create() method
const config = this.cache.json.get('emitterConfig');
const particles = this.add.particles('particles');

// Create emitter using the config and a specific frame from the atlas
const emitter = particles.createEmitter({
    ...config,
    frame: 'bluebubble'
});
```

## Asset Flow

The following diagram illustrates how the `.tps` file and source images compile into the runtime assets used by the game engine.

```mermaid
graph TD
    A[Source Images .png] -->|TexturePacker| B[bubbles.tps]
    B --> C[/assets/particles/bubbles.json]
    B --> D[/assets/particles/bubbles.png]
    E[/assets/particles/emitter.json] -->|Configuration| F[Phaser Particle Emitter]
    C -->|Metadata| F
    D -->|Textures| F
```

## Legal and Copyright
The particles module includes assets from the **Animated Sprite Pack** by *The Game Creators*. 
*   **Restriction**: These assets are included for development and testing within this specific project context. 
*   **Requirement**: Use in commercial projects requires the purchase of a full license from [The Game Creators](http://www.thegamecreators.com/?m=view_product&id=2154).
*   See `/assets/particles/copyright.txt` for full details.