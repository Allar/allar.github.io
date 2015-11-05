---
layout: post
title: "How To Convert a Blueprint Only Project to C++ (using Windows)"
tags: [Allar, ue4, tutorial]
thumbnail: "2015-11-05-Converting-BP-Project-To-CPP.gif"
---

This document covers how to convert a Blueprint Only project to a C++ project without writing any C++ code using Windows. This isn't intended to fold down your Blueprint Logic to C++, it is meant only to wrap your Blueprint Project with a C++ project. Some plugins and engine features require a C++ project and this is an easy way to still work in Blueprints but not be constrained by this limitation.<!-- more -->

# Requirements

+ For 4.9.X or older, you need [Visual Studio Community Edition 2013](https://www.visualstudio.com/en-us/news/vs2013-community-vs.aspx){:target="_blank"}
+ For 4.10 or newer, you need [Visual Studio Community Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs){:target="_blank"}
	- Make sure you install Visual C++. 2015 doesn't do this by default
	- [During installation, choose Custom, not Typical](/images/tutorials/converting-bp-project-to-cpp/1_ChooseCustom.PNG){:target="_blank"}
	- [Make sure Visual C++ and all children are selected](/images/tutorials/converting-bp-project-to-cpp/2_VisualCPP.PNG){:target="_blank"}
	- If you skipped installing Visual C++ and Unreal Engine 4 is complaining about not being able to find it, you can fix this by: re-running the downloaded installer and [choosing "Modify"](/images/tutorials/converting-bp-project-to-cpp/Appendix1_2015Modify.PNG){:target="_blank"}, then [selecting Visual C++ and clicking "Update"](/images/tutorials/converting-bp-project-to-cpp/Appendix2_VisualCPP.PNG){:target="_blank"}.

# Convert Process

1. Open Your Project in the Unreal Engine 4 Editor.
1. [Open up the New C++ Class Dialog by using File -> New C++ Class...](/images/tutorials/converting-bp-project-to-cpp/3_NewCPPClass.PNG){:target="_blank"}
1. [Choose a new "None" class and hit "Create Class."](/images/tutorials/converting-bp-project-to-cpp/4_NewNoneClass.PNG){:target="_blank"}
1. [You may be prompted with a warning message about being able to compile the game module](/images/tutorials/converting-bp-project-to-cpp/5_PossibleWarning.PNG){:target="_blank"} You can safely ignore this and hit No.
1. Close any pop-ups, warnings, or success notifications, then close the Editor
1. [Navigate to your project's folder, right click your project's .urpoject file, and click "Generate Visual Studio project files."](/images/tutorials/converting-bp-project-to-cpp/6_RegenerateFiles.PNG){:target="_blank"}
1. [Open your project in Visual Studio by double-clicking your project's .sln file.](/images/tutorials/converting-bp-project-to-cpp/7_SolutionFile.PNG){:target="_blank"}
1. Visual Studio should load. If it asks you what theme you would like, pick what looks best to you, all screenshots here will be using the Dark theme. [You should be looking at something like this.](/images/tutorials/converting-bp-project-to-cpp/8_VSOpen.PNG){:target="_blank"}
1. [Find and select your project in Visual Studio's Solution Explorer.](/images/tutorials/converting-bp-project-to-cpp/9_SolutionExplorer.PNG){:target="_blank"}
1. [Set the build configuration dropdown to "Development Editor"](/images/tutorials/converting-bp-project-to-cpp/10_DevelopmentEditor.PNG){:target="_blank"}
1. [Right-click your project in the Solution Explorer and click Build.](/images/tutorials/converting-bp-project-to-cpp/11_ClickBuild.PNG){:target="_blank"}
1. [This should result in your project being compiled successfully with zero errors.](/images/tutorials/converting-bp-project-to-cpp/12_EditorCompiled.PNG){:target="_blank"}
1. [Set the build configuration dropdown to "Development"](/images/tutorials/converting-bp-project-to-cpp/13_Development.PNG){:target="_blank"}
1. [Right-click your project in the Solution Explorer and click Build, again.](/images/tutorials/converting-bp-project-to-cpp/14_ClickBuild.PNG){:target="_blank"}
1. This build will take much longer, on the order of several minutes. [This should result in your project being compiled successfully with zero errors though, again.](/images/tutorials/converting-bp-project-to-cpp/15_GameCompiled.PNG){:target="_blank"}

# Finished

Your Blueprint Project is now a C++ Project. You are able to open up your project just as before and nothing has changed in terms of your project's functionaliy, but you are now able to use Engine features that require C++ Projects, such as third-party plugins, Subsystem integrations, dedicated servers, etc. You shouldn't have to open Visual Studio ever again unless you do some C++ code, but you still have to keep Visual Studio installed.