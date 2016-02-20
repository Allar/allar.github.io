---
layout: post
title: "Rendered 360 Video With UE4"
thumbnail: "UE4-Annoying-Hat.gif"
image:
    feature: projects/360Video.gif
link: https://forums.unrealengine.com/showthread.php?71489-360-Panoramic-Stereoscopic-Export-Pipeline
tags: [Allar, projects]
---

One day a friend of mind asked if there was a way to export a sequence of Cubemap Render Targets suitable for making a 360 degree video. After some experimentation, I ended up with a plugin that would allow exporting of any Unreal asset through Blueprint, including Cubemap Render Targets. Combine this export ability and some additional blueprint, I was able to create a [360 Panoramic Video on YouTube](https://www.youtube.com/watch?v=AY5vQ0Pm1WE).

This ended up getting pretty popular and I gained a few clients because of it.

For more information, check out the [GitHub repo for my ExportObjectPlugin.](https://github.com/Allar/ExportObjectPlugin)