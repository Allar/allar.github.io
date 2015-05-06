---
layout: post
title: "Proof of Concept: Routing Unreal Engine 4 to Resolume through Spout"
tags: [Allar, ue4, proof of concept, code, spout, resolume]
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/Yy8NZFCBMm4" frameborder="0"> </iframe>

As a silly challenge, I made a solution to what I thought was the unasked question of "How to use UE4 with Spout". I set up a special Camera Actor in the world that would send its render texture target to a Spout listener using some really hacky UE4 RHI calls. To prove that it worked, I used a Spout listener in Resolume to see the live feed. Turns out it worked pretty well and people won't stop asking me about it. Some time I'd like to be able to spruce this up and open source it.