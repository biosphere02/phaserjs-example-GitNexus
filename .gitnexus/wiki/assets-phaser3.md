# assets — phaser3

# Phaser 3 Asset: Boing Texture Atlas

The `assets/phaser3/boing.json` file is a **Texture Atlas** definition designed for use with the Phaser 3 game engine. It specifies how a single image file (`boing.png`) is partitioned into multiple sub-images (frames), allowing for efficient rendering and animation.

This specific atlas contains frames for a bouncing animation ("boing"), a window UI element, and a shadow.

## Technical Specifications

- **Format:** Phaser 3 Multi-Atlas (compatible with TexturePacker JSON format).
- **Source Image:** `boing.png` (465x607, RGBA8888).
- **Tooling:** Generated via [TexturePacker](https://www.codeandweb.com/texturepacker).

## Frame Definitions

The atlas contains 16 distinct frames. These can be categorized into animation sequences and static sprites.

### 1. Animation Sequence (`boing`)
There are 14 frames dedicated to a "boing" animation.
- **Frames:** `boing1` through `boing14`
- **Dimensions:** Consistent size of 113x98 pixels.
- **Usage:** Typically played in sequence to create a squash-and-stretch or bouncing effect.

### 2. Static Sprites
- `boing-window`: A larger 344x266 element, likely used as a background container or UI panel.
- `shadow`: A 74x100 sprite used for depth or ground positioning.

## Integration Guide

### Loading the Atlas
To use these assets in a Phaser 3 Scene, load the JSON file and its corresponding image in the `preload` method:

```javascript
function preload() {
    // The key 'boing' will be used to reference this atlas later
    this.load.atlas('boing', 'assets/phaser3/boing.png', 'assets/phaser3/boing.json');
}
```

### Creating an Animation
You can define an animation using the numbered frames identified in the JSON:

```javascript
function create() {
    this.anims.create({
        key: 'bounce_effect',
        frames: this.anims.generateFrameNames('boing', {
            prefix: 'boing',
            start: 1,
            end: 14
        }),
        frameRate: 24,
        repeat: -1
    });

    const sprite = this.add.sprite(400, 300, 'boing', 'boing1');
    sprite.play('bounce_effect');
}
```

### Using Static Frames
Individual frames can be assigned to Sprites or Images by referencing their `filename` from the JSON:

```javascript
// Add the window UI element
this.add.image(400, 300, 'boing', 'boing-window');

// Add the shadow
this.add.image(400, 400, 'boing', 'shadow');
```

## Data Structure Reference
The JSON follows the standard Phaser 3 texture layout:

| Property | Description |
| :--- | :--- |
| `filename` | The unique identifier for the frame. |
| `frame` | The `x, y, w, h` coordinates within `boing.png`. |
| `spriteSourceSize` | The offset and dimensions if the frame was trimmed of whitespace. |
| `sourceSize` | The original un-trimmed dimensions of the sprite. |