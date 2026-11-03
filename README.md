# VolumetricFlowmaps-Fab

**Volumetric Flowmaps** is an Unreal Engine data plugin to setup 3D Volumetric Flowmaps, available on Fab. It is a toolkit of Blueprints and materials to help create and customize 3D volumetric flowmaps.

2D flowmaps are a very common technique and a popular trick in video games often used to create cheap, looping animations for water, smoke, and other effects.

Volumetric rendering is still relatively expensive for games, and even sparse volumes can consume a significant amount of memory. This is where the idea of 3D Volumetric Flowmaps comes in. We can replace expensive, high-definition volumetric animations with a lower-quality but much cheaper flowmap-based animation.

This plugin gives you all the tools to setup 3D Volumetric flowmaps in Unreal Engine. It contains:
* 7 demonstration levels
* 3 master blueprints + 3 helper blueprints
* 4 master materials, with 23 material instances
* 11 volumes + 3 sparse volumes textures


## Table of Contents
- [Install](#Install)
- [How to use](#How-to-use)
- [SVT to Volume Texture](#Sparse-Volume-Texture-to-Volume-Texture)
- [Math Expression on Textures](#Modify-Texture-values)
- [Change Texture format](#Change-Texture-format)


## Install

First make sure that the plugin is enabled:
<img width="1137" height="248" alt="image" src="https://github.com/user-attachments/assets/3e7456d1-0446-4610-9321-304136e383de" />


## How to use

To apply 3D volumetric flowmaps, you first need a volume. It could be a Volume texture (dense) or a Sparse Volume Texture. In modern Unreal Engine, the best way to display volume data is with Heterogeneous Volumes.

To apply 3D volumetric flowmaps, you need:
* A (dense or sparse) volume
* A (volume) flowmap
* A flowmap material


### Heterogeneous Volumes

In modern Unreal Engine, the best way to display volume data is with Heterogeneous Volumes. 

Drag and drop `Blueprints/BP_HV_Flowmap` in your level for a basic setup. 

<img width="107" height="176" alt="image" src="https://github.com/user-attachments/assets/6d90ecff-4061-43af-b603-bd4fe2e9f427" />

Here are the principal parameters:

* _Material_: Select which material options you want to enable for your volumetric flowmap. Explained in more details in [Materials](#Materials).
* _Sparse Volume Texture_: Select your Sparse Volume Texture
* _Density_: Density multiplier of Sparse Volume Texture
* _Albedo Color_: Diffuse Color of the Sparse Volume Texture
* _Albedo Scale_: Diffuse Color Scale of the Sparse Volume Texture
* _Edge Fade_: Option to fade out volume from the volume edges. By default 0.0, no fading.

  
### Dense Volumes

Use `Blueprints/BP_DV_Flowmap` for the same blueprint and behavior but with a Dense Volume (DV) texture as main volume.

<img width="103" height="174" alt="image" src="https://github.com/user-attachments/assets/9a793b82-6bd8-40bb-b7f4-e32407bb9c02" />



### Materials

If you prefer to use your own custom actor or blueprint, you can choose your master material and create your own material instance to enable and tweak different options according to your needs.

<img width="416" height="174" alt="image" src="https://github.com/user-attachments/assets/0708cc76-f444-4f3c-a0a0-21b609caa307" />

Some users will even want to create their own master material: the plugin comes with several key material functions, all explained and commented. Use the provided master materials as reference to help you understand the underlying algorithm and recreate your own.

The provided master materials contain several **static switches**:
* _Two phases (2P) or three phases (3P)_: number of volume samples to blend during flow. Three is better looking but 50% more expensive.
* _UVW or world space (WS) coordinates_: wether to sample Turbulence Volume in volume space (UVW, respect volume ratio) or world space (WS, ignores volume ratio).
* _Use distance field (DF) or not_: whether to sample level distance field to approximate flow around objects, if distance field is enabled in project settings.
* _Displacement in Local Space (default) or World Space_: whether to use turbulence vector and wind vector in local space or world space.

The predefined material instances follow the above annotations to indicate which options are enabled. For example, `MI_HV_Flowmap_2P_UVW_DF_WS` uses the Heterogeneous Volume flowmap version, with two phases, UVW turbulence coordinates, samples and reacts to the Distance Field, and considers all displacement in world space (invariant to owning actor's transform).

<img width="1151" height="180" alt="image" src="https://github.com/user-attachments/assets/16f23f30-6fc6-48ec-abc1-d75e49f788f5" />


All demonstration levels explain and clearly show the differences between all these options for better understanding.


## Turbulence + Wind

In flowmaps, there's usually two different displacement sources:
* **Turbulence**, in our case volumetric
* **Wind**, in our case a "simple" displacement vector

Turbulence doesn't have to be volumetric, and wind or other sources of displacement could be more complex; options are endless, your imagination is your limit. 

This plugin focuses on the most difficult and common cases. It is also meant to be flexible to allow users to adapt it to their specific needs.

Here's an explanation of all Blueprint (or material instance) displacement parameters:
* _Total Displacement_: Global displacement multiplication factor (default 1.0)
* _Time Speed_: Game time multiplicator (default 0.1)
* _Wind Strength_: Wind multiplier (default 0.0)
* _Turbulence Flowmap_: Volume texture that contains displacement vectors.
* _Turbulence Scale_: Turbulence vector scaler. Actual operation is: (Turbulence + Bias) * Scale. Depending on which DCC you use, values might range from 0.001 to 100.0. Default is 0.1.
* _Turbulence Bias_: Bias applied on Turbulence Vectors. Actual operation is: (Turbulence + Bias) * Scale.
* _Turbulence Tiling_: Turbulence coordinates scale. If using a Turbulence volume tailored for your density Volume, it should probably be 1 to get a 1:1 match. Default is 1.

* _Advanced - Turbulence Rotation Transform_: rotation applied on the result Turbulence vectors. Per vector.
* _Advanced - Turbulence Scale Transform_: scale applied on the result Turbulence vectors. Per vector.
* _Advanced - Distance Field (DF) Strength_: distance field deflection scale.
* _Advanced - Distance Field (DF) Radius_: radius of influence of distance field.
 
 
## Distance Field

If enabled in project settings, Unreal Engine generates [mesh distance fields](https://dev.epicgames.com/documentation/unreal-engine/mesh-distance-fields-in-unreal-engine).

We can sample the global distance field in materials and get the distance and gradient at any point of the distance field (DF). This allows us to fake flow distortion around meshes and visually have the flow go around objects rather than just ignoring objects.

Distance Field can be sampled when using material instances with `DF` in the name. Level `Demo/LVL_05_DistanceFieldFlow` shows how to use the feature.

INSERT IMAGES. DF ON / OFF


## Tubulence Volumes

Turbulence Volumes are vector fields. They can be created in external DDC, such as Blender, Maya, Houdini, Embergen etc.

<img width="128" height="128" alt="Icon128" src="https://github.com/user-attachments/assets/896573e3-ddf7-44f0-9437-111a1fd5fc5a" />



Most custom volumes provided in the plugin were created with [Janga's FX FREE VectorayGen](https://jangafx.com/software/vectoraygen) software.

Creating volumes in Unreal Engine is surprisingly difficult. I've created and shared a FREE UE plugin that helps me convert SVT into Volume textures and Vector fields into Volume Textures. Hope this helps.


## Debug Visualizer


## Acronyms

* DV: Dense Volume
* HV: Heterogeneous Volume
* WS: World Space
* LS: Local Space
* UVW: volumetric coordinates
* WC: world coordinates

## Thanks

Thanks for being interested in this plugin. At the moment of the publication and to the best of my knowledge, it is the first and only plugin able to do volumetric flowmaps in a game engine.

If you've bought the plugin and like, please leave a good review, it really helps a lot! 

If you've noticed issues, bugs or missing features, please open an [Issue](https://github.com/thilamb/VolumetricFlowmaps-Fab/issues) and I'll try my very best to deal with it as soon as possible.

This plugin is my fifth publicly available Unreal Engine plugin. Have you tried [SVT / VDB helper](https://www.fab.com/listings/2cc1bd5a-40df-49a2-bd52-42fa06b114dc) that guides you with more complicated VDB setups in Unreal, and [RuntimeVDB](https://www.fab.com/listings/12fd5458-5d7f-4123-8614-138a689c030b) to be able to load OpenVDB files at runtime?
