# VolumetricFlowmaps-Fab

**Volumetric Flowmaps** is an Unreal Engine data plugin, available on Fab, designed to help you create and work with 3D volumetric flowmaps. It provides a collection of Blueprints and materials to easily set up, customize, and animate your own volumetric effects.

2D flowmaps are a well-established technique in game development, commonly used to create inexpensive, looping animations for water, smoke, and other effects as an alternative to costly simulations.

3D simulations are considerably more expensive. While Unreal Engine now supports playing back large, pre-baked OpenVDB sequences, these can still consume significant amounts of memory and require substantial resources to stream, process, and render.

**This is where the idea of 3D Volumetric Flowmaps comes in.**

By combining a **static volumetric texture with a lightweight 3D flowmap**, we can animate volumetric effects without the need for large VDB animation sequences, dramatically reducing memory requirements while retaining much of the visual richness of the original simulation.

This plugin gives you all the tools to setup 3D Volumetric flowmaps in Unreal Engine. It contains:
* 7 demonstration levels
* 3 master blueprints + 3 helper blueprints
* 5 master materials, with 24 material instances
* 11 volumes + 4 sparse volumes textures

<img width="2551" height="1344" alt="2026-10-20 13_40_58-SVT_5_5 - Unreal Editor" src="https://github.com/user-attachments/assets/8a755630-8508-4799-9b12-5609babb963a" />


## Table of Contents
- [Install](#Install)
- [Basic principles](#Basic-principles)
- [Heterogeneous Volumes](#Heterogeneous-Volumes)
- [Dense Volumes](#Dense-Volumes)
- [Materials](#Materials)
- [Turbulence + Wind](#Turbulence-+-Wind)
- [Distance Field](#Distance-Field)
- [Tubulence Volumes](#Tubulence-Volumes)
- [Debug Visualizer](#Debug-Visualizer)
- [Acronyms](#Acronyms)
- [Thanks](#Thanks)


## Install

First make sure that the plugin is enabled:
<img width="1137" height="248" alt="image" src="https://github.com/user-attachments/assets/3e7456d1-0446-4610-9321-304136e383de" />

Also check that your are displaying Plugins Content in content browser's settings:
<img width="477" height="772" alt="2026-11-06 11_44_10-Clipboard" src="https://github.com/user-attachments/assets/683bbcf3-5a6e-4e63-b1c4-1e2403d8d1d1" />


## Basic principles

To apply 3D volumetric flowmaps, you need:
* A (dense or sparse) volume
* A (volume) flowmap
* A flowmap material


### Heterogeneous Volumes

In modern Unreal Engine, the best way to display volume data is with Heterogeneous Volumes. 

<img width="107" height="176" alt="image" src="https://github.com/user-attachments/assets/6d90ecff-4061-43af-b603-bd4fe2e9f427" />

`Blueprints/BP_HV_Flowmap` is the main Blueprint for creating and customizing Volumetric Flowmaps on Heterogeneous Volumes (HV). Simply drag and drop it into your level, then use the Details panel to access a wide range of basic and advanced parameters.

| **Parameter** | **Description** |
|---|---|
| *Material* | Select the material or material instance to use for your Volumetric Flowmap. See [Materials](#Materials) for more details. |
| *Sparse Volume Texture* | Select the Sparse Volume Texture to use. |
| *Density* | Density multiplier applied to the Sparse Volume Texture. |
| *Albedo Color* | Diffuse color of the Sparse Volume Texture. |
| *Albedo Scale* | Diffuse color multiplier applied to the Sparse Volume Texture. |
| *Edge Fade* | Controls the fading of the volume near its boundaries. Default: `0.0` (no fading). |

  
### Dense Volumes

Use `Blueprints/BP_DV_Flowmap` to access the same Blueprint functionality and behavior, but with a Dense Volume (DV) texture as the main volume.

<img width="103" height="174" alt="image" src="https://github.com/user-attachments/assets/9a793b82-6bd8-40bb-b7f4-e32407bb9c02" />



### Materials

The plugin comes with 5 master materials and 24 ready-to-use material instances. 

To provide maximum flexibility and control, the master materials expose several **static switches** that let you customize the flowmap behavior and choose between different quality and performance trade-offs:

| **Static Switch** | **Description** |
|---|---|
| *Two Phases (2P) / Three Phases (3P)* | Controls the number of volume samples blended during animation. Three phases generally produce smoother-looking results but come with an additional 50% sampling cost. |
| *UVW / World Space (WS) Coordinates* | Determines how the Turbulence Volume is sampled. UVW coordinates respect the volume's original aspect ratio, while World Space coordinates ignore it. |
| *Use Distance Field (DF)* | Enables sampling of the Global Distance Field to approximate flow behavior around nearby objects. Requires Distance Fields to be enabled in the project settings. |
| *Displacement in Local Space / World Space* | Determines whether turbulence and wind vectors are interpreted in Local Space (default) or World Space. |


The predefined material instances follow the above annotations to indicate which options are enabled. For example, `MI_HV_Flowmap_2P_UVW_DF_WS` uses the Heterogeneous Volume flowmap version, with two phases, UVW turbulence coordinates, samples and reacts to the Distance Field, and considers all displacement in world space (invariant to owning actor's transform).

<img width="1151" height="180" alt="image" src="https://github.com/user-attachments/assets/16f23f30-6fc6-48ec-abc1-d75e49f788f5" />

Each demonstration level clearly illustrates these options and highlights their differences to help you understand their behavior and choose the settings that best suit your needs.

#### Advanced Customization

If you prefer to use your own custom Actor or Blueprint, you can select one of the provided master materials and create your own Material Instance to enable and adjust the available options according to your needs.

<img width="416" height="174" alt="image" src="https://github.com/user-attachments/assets/0708cc76-f444-4f3c-a0a0-21b609caa307" />

You can also go a step further and create your own master material. The plugin includes several key Material Functions, all fully documented and commented. Use the provided master materials as a reference to understand the underlying algorithm and implement your own variations.

---

## Turbulence + Wind

Volumetric flowmaps generally rely on two different sources of displacement:

- **Turbulence:** In this plugin, represented by a volumetric vector field.
- **Wind:** A simple directional displacement vector.

Neither of these sources is limited to this implementation. Turbulence doesn't necessarily have to be volumetric, and wind or other displacement sources could be much more complex. The possibilities are virtually endless.

This plugin focuses on common use cases involving volumetric variations while providing enough flexibility for users to adapt the system to their own requirements.

<img width="1039" height="852" alt="VolumetricFlowmapsBis" src="https://github.com/user-attachments/assets/078746cf-e478-45e8-a712-f6a6b8785e77" />

Below is a description of all available displacement parameters, exposed through the Blueprint and Material Instance:

| **Parameter** | **Description** |
|---|---|
| *Total Displacement* | Global displacement multiplier. Default: `1.0`. |
| *Time Speed* | Controls the animation speed by scaling game time. Default: `0.1`. |
| *Wind Strength* | Wind displacement multiplier. Default: `0.0`. |
| *Turbulence Flowmap* | Volume Texture containing the displacement vectors. Note that the plugin also provides a separate master material that samples a Sparse Volume Texture (SVT) instead. This parameter is unused when using that version. |
| *Turbulence Scale* | Multiplier applied to turbulence vectors. The actual operation is `(Turbulence + Bias) * Scale`. Depending on the DCC application used to create the volume, values may range from `0.001` to `100.0`. Default: `0.1`. |
| *Turbulence Bias* | Bias added to the turbulence vectors before scaling. The actual operation is `(Turbulence + Bias) * Scale`. |
| *Turbulence Tiling* | Controls the scale of the turbulence sampling coordinates. If the turbulence volume was specifically created to match your density volume, a value of `1.0` will generally provide a 1:1 match. Default: `1.0`. |
| *Advanced - Turbulence Rotation Transform* | Applies a per-vector rotation transform to the resulting turbulence vectors. |
| *Advanced - Turbulence Scale Transform* | Applies a per-vector scale transform to the resulting turbulence vectors. |
| *Advanced - Distance Field (DF) Strength* | Controls the strength of the displacement generated by the Distance Field. |
| *Advanced - Distance Field (DF) Radius* | Defines the radius of influence of the Distance Field. |

---

## Distance Fields

When enabled in the project settings, Unreal Engine generates [Mesh Distance Fields](https://dev.epicgames.com/documentation/unreal-engine/mesh-distance-fields-in-unreal-engine) for supported meshes.

These Distance Fields can be sampled directly in materials to retrieve the distance and gradient at any given position. This information can be used to approximate flow deflection around nearby geometry, making the volumetric flow appear to move around objects instead of simply passing through them.

Distance Field deflection is available through the Material Instances with `DF` in their names.

See `Demo/LVL_05_DistanceFieldFlow` for a demonstration of this feature.

| Without Distance Field Deflection | With Distance Field Deflection |
|:---:|:---:|
| <img width="1300" height="880" alt="Without Distance Field Deflection" src="https://github.com/user-attachments/assets/1874beb0-3de9-4976-a834-ab9d593d6f5b" /> | <img width="1300" height="880" alt="With Distance Field Deflection" src="https://github.com/user-attachments/assets/04f48aa8-1e66-40e7-a2b4-18387d577150" /> |

---

## Turbulence Volumes

Turbulence Volumes are 3D vector fields that can be created using external DCC applications such as Blender, Maya, Houdini, or EmberGen.

<img width="128" height="128" alt="Icon128" src="https://github.com/user-attachments/assets/896573e3-ddf7-44f0-9437-111a1fd5fc5a" />

Most of the custom turbulence volumes included with the plugin were created using [JangaFX's free VectorayGen](https://jangafx.com/software/vectoraygen).

Creating custom volume textures directly in Unreal Engine can be surprisingly challenging. To make this process easier, I've also created a [free Unreal Engine plugin](https://www.fab.com/listings/d5e168d5-3f90-4c42-ba25-32d5d71159f5) that helps convert Sparse Volume Textures (SVTs) into regular Volume Textures, as well as convert vector fields into Volume Textures.

Hopefully, this will be useful for anyone looking to create their own turbulence volumes!

---

## Debug Visualizer

The plugin also includes dedicated Blueprints (`BP_HV_FlowVisualizer`) and materials (`M_HV_FlowVisualizer`) to help you inspect, understand, and debug your volumetric flowmap setup.

The `LVL_99_DebugTurbulenceVisualization` demo level provides a closer look at what happens inside the materials. It allows you to visualize the underlying turbulence and wind displacement fields and observe how they respond to changes in the various parameters.

This is particularly useful when creating custom turbulence volumes or fine-tuning your flowmap settings.

<img width="800" height="433" alt="DebugVisualizer" src="https://github.com/user-attachments/assets/fa384303-b9ac-479c-b73d-e23a0188e486" />


## Acronyms

* DV: Dense Volume
* HV: Heterogeneous Volume
* WS: World Space
* LS: Local Space
* UVW: volumetric coordinates
* WC: world coordinates


## Thanks!

Thank you for your interest in this plugin!

At the time of publication, and to the best of my knowledge, this is the first and only plugin to bring volumetric flowmaps to a game engine.

If you've purchased the plugin and enjoy using it, please consider leaving a positive review. It really helps a lot!

If you encounter any issues, bugs, or missing features, please open an [Issue](https://github.com/thilamb/VolumetricFlowmaps-Fab/issues). I'll do my very best to address it as soon as possible.

This is my fifth publicly available Unreal Engine plugin! Have you also tried [SVT / VDB Helper](https://www.fab.com/listings/2cc1bd5a-40df-49a2-bd52-42fa06b114dc), which helps with more advanced VDB setups in Unreal, or [RuntimeVDB](https://www.fab.com/listings/12fd5458-5d7f-4123-8614-138a689c030b), which lets you load OpenVDB files at runtime?
