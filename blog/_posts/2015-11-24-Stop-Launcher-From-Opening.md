---
layout: post
title: "Stop UE4 Editor From Automatically Opening The Launcher"
tags: [Allar, ue4, tutorial, launcher]
thumbnail: "UE4-Annoying-Hat.gif"
---

With the release of 4.10, the Epic Launcher seems to have improved its ability to pop up when you don't want it. Not only does it open the Launcher now if you don't have it open, it forces the Launcher to take focus and brings it to the top of your desktop. There is no user-facing setting to control this behavior at the time of this writing.

You can disable his auto-launching of the Launcher behavior without making any serious changes to your engine.

To do this, simply create a new text file called `PerforceBuild.txt` inside your `Engine\Build` folder. Make this text file non-empty, meaning, open it up and type some random stuff in it such as "Stop opening the launcher." Now when you open the editor, the Launcher should not open and steal focus automatically.