---
layout: post
title: "Stop UE4 Editor From Automatically Opening The Launcher"
tags: [Allar, ue4, tutorial, launcher]
thumbnail: "UE4-Annoying-Hat.gif"
---

With the release of 4.10, the Epic Launcher seems to have improved its ability to pop up when you don't want it. Not only does it open the Launcher now if you don't have it open, it forces the Launcher to take focus and brings it to the top of your desktop. There is no user-facing setting to control this behavior at the time of this writing.

You can disable his auto-launching of the Launcher behavior without making any serious changes to your engine.

To do this, simply create a new text file called `PerforceBuild.txt` inside your `Engine\Build` folder. Make this text file non-empty, meaning, open it up and type some random stuff in it such as "Stop opening the launcher." Now when you open the editor, the Launcher should not open and steal focus automatically.

This does come with some side effects though:

1. Any time the version of the editor is listed, it'll show a much longer 'more technical' string.
1. It removes the Recompile C++ button in the Editor (I frankly never use this)
1. Disables "INI overrides", a feature rarely used by anyone. Might want to remove this text file if it appears in your distributed builds though, but it shouldn't affect anything.
1. Bug and analytic reports to Epic will indicate you are running a 'Perforce Build'
1. Crashes that don't report a runtime callstack might now report a runtime callstack (a good thing?)