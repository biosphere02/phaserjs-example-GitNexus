# assets — textures

# Assets — Textures

The `/assets/textures/` directory serves as the central repository for image-based surface data used in the application's rendering pipeline. These assets are primarily Physically Based Rendering (PBR) texture sets used to define the visual properties of 3D models and environments.

## Overview

Content in this module consists of static assets rather than executable code. The textures are sourced from [3dtextures.me](https://3dtextures.me), which provides high-quality, seamless maps compatible with standard PBR workflows (Metalness/Roughness or Specular/Glossiness).

## Directory Structure

While this module contains no logic, it follows a strict organizational pattern to ensure compatibility with asset loaders. Each material is typically stored in its own sub-directory within `/assets/textures/`:

```text
/assets/textures/
├── [material_name]/
│   ├── [material_name]_basecolor.png  (or _albedo)
│   ├── [material_name]_normal.png
│   ├── [material_name]_roughness.png
│   ├── [material_name]_metallic.png
│   ├── [material_name]_ambientocclusion.png
│   └── [material_name]_height.png
└── readme.txt
```

### Map Types and Usage

When contributing or adding new textures to this directory, ensure they align with the following standard naming conventions used by the engine's material parsers:

| Suffix | Map Type | Description |
| :--- | :--- | :--- |
| `_basecolor` | Albedo | The raw color of the surface without lighting information. |
| `_normal` | Normal Map | Used to perturb surface normals for fine detail. Typically RGB (Tangent Space). |
| `_roughness` | Roughness | Greyscale map defining how microscopic irregularities scatter light. |
| `_metallic` | Metalness | Greyscale map defining which areas of the texture are metallic. |
| `_ao` | Ambient Occlusion | Greyscale map used to simulate soft shadows in crevices. |
| `_height` | Displacement | Used for parallax mapping or vertex displacement. |

## Integration

As indicated by the execution flow analysis, this module has no internal code logic. It is a **passive resource module**. 

1.  **Loading**: Resources in `/assets/textures/` are accessed by the engine's filesystem or asset manager using absolute paths.
2.  **Referencing**: Shaders and Material files (e.g., `.mtl`, `.json`, or `.material`) point to these files to bind them to GPU texture units during the draw call.

## Workflow for Adding Assets

1.  **Source**: Download the desired texture pack from [3dtextures.me](https://3dtextures.me).
2.  **Format**: Ensure textures are in a web-compatible or engine-optimized format (standardly `.png` or `.jpg`).
3.  **Placement**: Create a descriptive folder name in `/assets/textures/` (e.g., `/assets/textures/cobblestone_01/`).
4.  **Naming**: Rename files to match the suffixes listed in the table above to ensure the automated material builder can resolve them.

## Attribution

All assets currently residing in this directory are provided by **3dtextures.me**. Developers should refer to the `/assets/textures/readme.txt` file for licensing and source information before redistributing these assets outside of this project.