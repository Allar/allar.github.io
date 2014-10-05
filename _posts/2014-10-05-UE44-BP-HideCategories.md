---
layout: post
title: "UE4.4.3 Pre-existing Blueprints Don't Reflect HideCategories Changes"
---

Relevant to Unreal Engine 4 version 4.4.3.

Today I found out that if you have some blueprints that you'd like to clean up editor wise so they are visually easier to change relevant settings through HideCategories, changes to HideCategories will only take effect on new blueprints of that class.

Pretty annoying. If you want HideCategories to take effect, you have to:

1. Duplicate your existing blueprint
2. Consolidate the duplicate and the old one together with references pointing to the new one
3. Fix up the redirector of the old asset if one is made(you can do this by right clicking the redirector if you have your content browser filters set to "Show Redirectors"
4. Rename new asset back to the original name
5. Fix up the redirector of the new asset if one is made

To fix up a redirector, right click a redirector in your content browser and hit "Fix Up". Your content browser must have the filter "Show Redirectors" on to see redirectors.

Please [upvote this answerhub question](https://answers.unrealengine.com/questions/40944/editing-hidecategories-does-not-update-existing-bl.html) regarding this issue. Or someone please fix it and submit a pull request and let me know of it. I might take a stab at fixing this later, but most likely not. 