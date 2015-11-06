---
layout: post
title: "How To Setup the Linux x86 Cross-Compile Toolchain for UE4 On Windows"
tags: [Allar, ue4, tutorial, cross-compile, linux, windows]
thumbnail: "UE4-Annoying-Hat.gif"
---

This document serves as a lean and mean guide to setting up the Linux x86 Cross-Compile Toolchain for Windows. The process is already mostly animated by Epic but the existing documentation is a bit verbose. <!-- more -->

# Requirements

+ For 4.9.X or older, you need [Visual Studio Community Edition 2013](https://www.visualstudio.com/en-us/news/vs2013-community-vs.aspx){:target="_blank"}
+ For 4.10 or newer, you need [Visual Studio Community Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs){:target="_blank"}
	- Make sure you install Visual C++. 2015 doesn't do this by default
	- [During installation, choose Custom, not Typical](/images/tutorials/converting-bp-project-to-cpp/1_ChooseCustom.png){:target="_blank"}
	- [Make sure Visual C++ and all children are selected](/images/tutorials/converting-bp-project-to-cpp/2_VisualCPP.png){:target="_blank"}
	- If you skipped installing Visual C++ and Unreal Engine 4 is complaining about not being able to find it, you can fix this by: re-running the downloaded installer and [choosing "Modify"](/images/tutorials/converting-bp-project-to-cpp/Appendix1_2015Modify.png){:target="_blank"}, then [selecting Visual C++ and clicking "Update".](/images/tutorials/converting-bp-project-to-cpp/Appendix2_VisualCPP.png){:target="_blank"}
+ Unreal Engine 4 compiled from source code
	- [Epic's Documentation on how to do this](https://docs.unrealengine.com/latest/INT/Programming/Development/BuildingUnrealEngine/index.html){:target="_blank"}
	- [A Tutorial I wrote on how to do this](/2014/09/25/Build-Source/){:target="_blank"}
	
# Installation

1. [Open the automated PowerShell script in your browser.](https://gist.githubusercontent.com/megamorf/46a36a7be2979d43ee07/raw/399934f8dc59fddb946cd361ef6a5d74e7e3c1ba/Install-LinuxToolChain){:target="_blank"}
1. Open up an instance of PowerShell as an Administrator. [You can do this by searching for PowerShell in your start menu, right clicking it, and clicking Run As Administrator.](/images/tutorials/installing-linux-toolchain-on-windows/1_PowershellAsAdmin.png){:target="_blank"}
1. In your browser, copy the entire script to your clipboard. You can do this by hitting Control+A and then Control+C.
1. Paste the script into Powershell using Control-V, and then hit Enter to execute the script.
1. PowerShell will then download and automatically install the Linux x86 Cross-Compile Toolchain. [This will take some time.](/images/tutorials/installing-linux-toolchain-on-windows/2_PowerShellInstalling.png){:target="_blank"} The download is around 200MB.
1. When the PowerShell script is done, [it will look something like this.](/images/tutorials/installing-linux-toolchain-on-windows/3_SuccessfulInstall.png){:target="_blank"} If you get a [big red error that looks something like this,](/images/tutorials/installing-linux-toolchain-on-windows/4_FailedNoAdmin.png){:target="_blank"} you did not run PowerShell as an administrator in Step 2. Please go back to Step 2 and repeat the process. The script will ask you if you would like to overwrite and replace files if you run it a second time, please do. Go to jail, do not pass go, do not collect $200.
1. You can then close the window, PowerShell is no longer needed.
1. With the Linux x86 Cross-Compile Toolchain installed, you need to download the Linux UE4 dependencies. To do this, go to your Engine build where you should have a batch file called Setup.bat that you used to initially set up your Engine. Go ahead and simply run this again. [If it asks you if you want to overwrite changes you have](/images/tutorials/installing-linux-toolchain-on-windows/5_SetupWarning.png){:target="_blank"}, I recommend saying Yes as you can always rebuild those files later if needed, but saying No should also work. Setup.bat will now download all the Linux dependencies you need.
1. Regenerate your project's Visual Studio project files by right-clicking your project's .uproject file and choosing ["Generate Visual Studio Project files".](/images/tutorials/converting-bp-project-to-cpp/6_RegenerateFiles.png){:target="_blank"} This is the same as Step 6 in the ["Convert your BP Project to a C++ Project" tutorial.](/2015/11/05/Converting-BP-Project-To-CPP)

# Verify It Works By Compiling for Linux x86

1. Open up your project's Visual Studio solution file. This is the .sln file that you opened in the ["Convert your BP Project to a C++ Project" tutorial](/2015/11/05/Converting-BP-Project-To-CPP){:target="_blank"} if you came from there.
1. [Make sure your project is selected in the Solution Explorer](/images/tutorials/converting-bp-project-to-cpp/9_SolutionExplorer.png){:target="_blank"}
1. [You should now see the Linux option under the Platforms drop down.](/images/tutorials/installing-linux-toolchain-on-windows/6_LinuxPlatformConfiguration.png){:target="_blank"} Select it. If you don't see it, then your toolchain is not installed correctly or you did not run Setup.bat after installing the toolchain to pull down the Linux dependencies.
1. [Change the Build Configuration drop down to "Development".](/images/tutorials/converting-bp-project-to-cpp/13_Development.png){:target="_blank"} This tells Visual Studio to build the Linux binary for your packaged game.
1. [Right-click your project in the Solution Explorer and hit Build.](/images/tutorials/converting-bp-project-to-cpp/14_ClickBuild.png){:target="_blank"}
1. [Your build should succeed without issues](/images/tutorials/installing-linux-toolchain-on-windows/7_LinuxDoneCompiling.png){:target="_blank"}, unless you have some custom code causing problems on Linux.
1. Now you can package Linux x86 builds of your game using the UE4 editor! To do this, [use File -> Package Project -> Linux](/images/tutorials/installing-linux-toolchain-on-windows/8_PackageLinux.png){:target="_blank"}, and the rest is the same as packaging for Windows.