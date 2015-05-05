---
layout: post
title: "Setting up version control (Perforce) for Unreal Engine 4 using DigitalOcean and Ubuntu (Part 2)"
tags: [Allar, perforce, source control, ue4, tutorial, Digital Ocean, Ubuntu]
---

# Video Version

<iframe width="720" height="480" src="//www.youtube.com/embed/FfVFM9GVjDI" frameborder="0" allowfullscreen></iframe>

# Condensed Version

If you know how to use Perforce, basically...

Make all the .dlls and .pdbs writable in Engine/Binaries, Engine/Plugins, Engine/Programs, YourProject/Binaries.
Don't put any intermediate files on the server.
Don't put any DerivedDataCache and Saved folders on Perforce.