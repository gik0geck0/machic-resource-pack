# Machic Resource Pack for Minecraft

This pack was developed for Valhelsia 3 on v1.16.4. It was shaded with either BSLex or Complementary Shaders, and tries to take full advantage of their POM/PBR rendering capabilities.

## Simple Install

1. Download at https://github.com/gik0geck0/machic-resource-pack/releases
2. Add to resourcepacks folder
3. Load resource pack from Minecraft UI (suggested to be in a game when doing so ala some weirdness observed in Valhelsia 3's mod selection)

## Running from Source

1. Download and Install: https://github.com/null511/PixelGraph
2. git clone this repo
3. Open `src` folder from PixelGraph
4. Choose the profile/size in the top right (e.g. lab13-1024x)
5. Publish drop-down > Create ZIP Archive
6. Publish!
7. Use the output zip in minecraft resource packs

## Licensed Resources

Nearly all the textures in this pack come from https://cc0textures.com/. I have immense gratitude to Lennart for making such high quality textures available under such a permissive license. Consider subscribing on his patreon if you like his work.

TODO: call out exact sources for each texture in a source.txt file.

## Contributing

PRs are welcome, so long as the textures can be redistributed under a permissive license, like CC0. Issues, suggestions, or other formats can be submitted using a Github Issue.

## Following this repo as a template

There were a variety of issues I went through to build this repo, so I hope others can learn from this.

When I originally started texturing, I simply pulled the normal map, and called it `block_n.png`, and carried on, pretending everything was fine. One day, I looked at a video of someone showing their own pack and the parallax effect, and it got me wondering: "Are my textures really flat? Does my normal map even work?" As I spelunked through shader options, I found the format option, and started looking into the lab-pbr format, and learned that my textures really ARE flat, and my normal map was doing nothing. And thus, the rabbit hole began...

lab-pbr normal-texture format: https://github.com/rre36/lab-pbr/wiki/Normal-Texture-Details
CC0's format and basics of DX vs OGL formats (note: this is a very different format from lab-pbr): https://help.cc0textures.com/doku.php?id=usage:directx-opengl
Detailed DX vs OGL format: http://wiki.polycount.com/wiki/Normal_Map_Technical_Details

Okay, so I've learned a lot about normal maps... but how do I (easily) convert between formats? lab-pbr's tools are about Minecraft specific format conversion, but I want to go from DX textures (that can be used in Unreal/Unity for other projects) into lab-pbr format. Where the heck can I find a tool to do that, so that I don't have to write one myself?!

The Magic: https://github.com/null511/PixelGraph

On the surface, this sounds like the right tool. It supports outputting to lab-pbr, it claims to be highly customizable, and it's open source. Now I just need to learn how to use it. Thankfully, `null511` published some sample projects, which helped a lot in setting the foundation -- there were source CC0textures.com sources as well.

### Defining the input format as DX
These instructions are specific to the resources I have available. Other texture authors may provide different assets in different formats. I have not attempted to maintain a project with mixed sources, so YMMV. Lennart at CC0textures.com generally publishes 4k or 8k textures with:
- albedo
- ambient occlusion
- displacement (aka height-map)
- normal
- roughness

The main challenges in authoring https://github.com/gik0geck0/machic-resource-pack/blob/main/src/input.yml were figuring out what to map, what should be left empty/default, and what needs to be inverted. I fiddled with this by guessing the trial/error...
- `format: raw`
- map every channel for albedo with the proper range and scale
- map alpha from alpha:red with range and scale (take notice of the alpha in each asset yml as well)
- height is inverted (luckily this works when the source is displacement)
- occlusion is inverted (I never looked into why)
- normal-x <- red
- normal-y <- green inverted (because the DX format is -Y value)
- normal-z <- blue (eventually dropped per lab-pbr format)
- roughness <- rough red
- diffuse, specular, smooth, metal, f0, porosity, sss, and emissive are all empty `: {}`

Each asset/texture also has a `pbr.yml` file. I started by copying `null511`'s  sample, but made the following changes:
```
alpha:
  value: 255
```
Without setting this, my textures were invisible, because there is no alpha channel, so it chooses 0 as the default. Similar to the examples, if your texture does have an alpha channel, you could use that instead.... but none of mine do, so hard-coding it to opaque is the solution.

[Final example](https://github.com/gik0geck0/machic-resource-pack/blob/main/src/assets/minecraft/textures/block/andesite/pbr.yml)

### Using this project as a template

If you're also working on a texture pack that uses DX/OGL formatted normal maps, you may find this repo helpful to get started. Feel free to clone this, and make modifications as you see fit, as allowed by the CC0 license.

Make sure to follow https://github.com/null511/PixelGraph/wiki/Texture-Naming and read all `null511`'s very helpful documentation, and tweak as needed.
