---
layout: post
title: "UE4 4.5 UMG Workaround: UI loses focus when clicking on no widget"
---

In 4.5 UMG if you set a player controller's input mode to UI Only when trying to show a modal widget, clicking in the empty/dead space of the viewport causes focus to shift back to the player controller. If you don't want this behavior, the easiest workaround is to set the root CanvasPanel's visibility in your modal widget to Visible instead of Self Hit Test Invisible. This will cause the CanvasPanel to absorb all click events, including the click event that would normally send your focus back to the player controller.

![Canvas Panel Visibility](/assets/CanvasPanelVisibility.png)