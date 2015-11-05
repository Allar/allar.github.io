---
layout: post
title: "How To Set Up Dedicated Servers For Your Unreal Engine 4 Game"
tags: [Allar, ue4, tutorial]
thumbnail: "2015-10-30-UE4-Keyboard-Layout.png"
---

# Overview

This document covers the bare basics on how to get your Unreal Engine 4 game project able to build both Windows and Linux dedicated server builds.

# Requirements

+ Unreal Engine 4 compiled from source code
	- [Epic's Documentation on how to do this](https://docs.unrealengine.com/latest/INT/Programming/Development/BuildingUnrealEngine/index.html)
	- [A Tutorial I wrote on how to do this](http://allarsblog.com/2014/09/25/Build-Source/)
+ Project must be a C++ code project
	- If your project is a Blueprint Only project, follow this tutorial to wrap it in C++. Don't worry, you won't have to write any code.
+ Linux Toolchain installed for Linux builds