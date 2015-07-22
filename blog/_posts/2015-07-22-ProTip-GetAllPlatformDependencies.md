---
layout: post
title: "Get All Platform Dependencies Easily"
tags: [Allar, ue4, protip, tutorial]
---

If you plan on compiling Unreal Engine 4 across multiple platforms and don't want to have to deal with adding all the related dependencies per platform to your source control or making sure they are all in sync across platforms or simply don't want to run Setup.sh multiple times, you can choose to fetch dependencies for all platforms from a single machine. On Windows, the easiest thing to do is to edit Setup.bat and find the line that reads:

	.\Engine\Binaries\DotNET\GitDependencies.exe --prompt %*

and go ahead and change this to:

	.\Engine\Binaries\DotNET\GitDependencies.exe --all --prompt %*

I haven't looked into running this on "Not Windows" but as long as you find a way to run GitDependencies with the --all argument, it will fetch all dependencies for all platforms. Setup.sh would be your starting point. You should also still run Setup.sh on Linux/OSX hosts as there are non-Epic dependencies that need updated, for instance clang, Mono, Qt, GTK, and others, that you also wouldn't ever put on your source control anyway.