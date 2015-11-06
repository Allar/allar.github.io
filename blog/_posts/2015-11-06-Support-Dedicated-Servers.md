---
layout: post
title: "How To Set Up Dedicated Servers for Windows and Linux For Your UE4 Game (using Windows)"
tags: [Allar, ue4, tutorial, server]
thumbnail: "UE4-Annoying-Hat.gif"
---

This document covers the bare basics on how to get your Unreal Engine 4 game project able to build both Windows and Linux dedicated server builds, using just a Windows machine for compiling. <!-- more -->

# Requirements

+ For 4.9.X or older, you need [Visual Studio Community Edition 2013](https://www.visualstudio.com/en-us/news/vs2013-community-vs.aspx){:target="_blank"}
+ For 4.10 or newer, you need [Visual Studio Community Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs){:target="_blank"}

+ Unreal Engine 4 compiled from source code
	- [A Tutorial I wrote on how to do this](/2014/09/25/Build-Source/){:target="_blank"}
	- [Epic's Documentation on how to do this](https://docs.unrealengine.com/latest/INT/Programming/Development/BuildingUnrealEngine/index.html){:target="_blank"}
+ Project must be a C++ code project
	- If your project is a Blueprint Only project, [follow this tutorial](/2015/11/05/Converting-BP-Project-To-CPP){:target="_blank"} to wrap it in C++. Don't worry, you won't have to write any code.
+ Linux Toolchain for Windows installed for Linux x86 Server Support
	- [Link to my beginner's guide goes over this.](/2015/11/06/Installing-Linux-Toolchain-On-Windows/){:target="_blank"}
	- [There is also the UE4 Wiki documentation](https://wiki.unrealengine.com/Compiling_For_Linux){:target="_blank"}

# Adding Dedicated Server Support

Note: The word Project in any referenced file name or code will refer to your project's name. For example, my project for this tutorial is named `GenShooter`, so in my case `Project.Target.cs` refers to `GenShooter.Target.cs`. `ProjectTarget` in my case would be `GenShooterTarget`.

1. Navigate to your Project's Source folder. [You should see some `.Target.cs` files.](/images/tutorials/support-dedicated-servers/1_FindTargetFiles.png){:target="_blank"}
1. [Make a copy of `Project.Target.cs` file and rename it `ProjectServer.Target.cs`](/images/tutorials/support-dedicated-servers/2_CopyTarget.png){:target="_blank"}, be sure not to grab `ProjectEditor.Target.cs`.
1. Open up `ProjectServer.Target.cs` in your favorite text editor. I'll be using Visual Studio here.
1. Rename all instances of `ProjectTarget` to `ProjectServerTarget`.
1. Change `Type = TargetType.Game;` to `Type = TargetType.Server;`.
1. Save this file. Your `ProjectServer.Target.cs` file should look something like this now:

{% highlight csharp %}
// Your Copyright Text Here

using UnrealBuildTool;
using System.Collections.Generic;

public class GenShooterServerTarget : TargetRules
{
	public GenShooterServerTarget(TargetInfo Target)
	{
		Type = TargetType.Server;
	}

	//
	// TargetRules interface.
	//

	public override void SetupBinaries(
		TargetInfo Target,
		ref List<UEBuildBinaryConfiguration> OutBuildBinaryConfigurations,
		ref List<string> OutExtraModuleNames
		)
	{
		OutExtraModuleNames.AddRange( new string[] { "GenShooter" } );
	}
}
{% endhighlight %}

# Building your Dedicated Server

1. Right-click your project's `.uproject` file in your project's folder and ["Generate Visual Studio project files".](/images/tutorials/converting-bp-project-to-cpp/6_RegenerateFiles.png){:target="_blank"}
1. Now we need to build our project in Visual Studio with the `Development Server` configuration for the Windows platform, and for the Linux platform as well if you have the [Linux x86 Cross-Compile Toolchain](/2015/11/06/Installing-Linux-Toolchain-On-Windows){:target="_blank"} installed. To do this, build your game project just as we built it in the [past tutorials](/2015/11/06/Installing-Linux-Toolchain-On-Windows){:target="_blank"} but this time with the [`Development Server` build configuration](/images/tutorials/support-dedicated-servers/3_DevelopmentServer.png){:target="_blank"}. When the Windows server is done building, [your output should look like this.](/images/tutorials/support-dedicated-servers/4_WindowsServerCompiled.png){:target="_blank"} Here is the [build output for the Linux server.](/images/tutorials/support-dedicated-servers/5_LinuxServerCompiled.png){:target="_blank"}

Now your project supports building for dedicated servers, for all platforms, including Linux. Whether Linux will compile is dependant on if your [Linux x86 Cross-Compile Toolchain](/2015/11/06/Installing-Linux-Toolchain-On-Windows){:target="_blank"} is setup correctly.

# Packaging Your Dedicated Server

1. Open up your project in the UE4 Editor.
1. Open up the Project Launcher using [Window -> Project Launcher](/images/tutorials/support-dedicated-servers/6_OpenProjectLauncher.png){:target="_blank"}. This should greet you with a window that [looks like this.](/images/tutorials/support-dedicated-servers/7_ProjectLauncher.png){:target="_blank"} This window allows for launching various project deployment configurations.
1. To build your project in dedicated server form, we need to make a custom build profile. Click the "Add a new custom launch profile" button in the bottom panel that looks like a plus sign. [This should open up the custom profile editing screen.](/images/tutorials/support-dedicated-servers/8_CustomProfile.png){:target="_blank"}
1. Choose your Project in the Project drop down. [If you do not see it, click browse and feed it your project's .uproject file.](/images/tutorials/support-dedicated-servers/9_ChooseProject.png){:target="_blank"}
1. Change Cook mode from `On the fly` to `By the Book`. Select the `WindowsServer` platform under Cooked Platforms. Select the `LinuxServer` platform as well if you have the Linux x86 Cross-Compile Toolchain installed. Also select `en` under Cooked Cultures, or select your base language if your project is not English centric. [Click here to see what these settings look like.](/images/tutorials/support-dedicated-servers/10_CookSettings.png){:target="_blank"}
1. Change Package mode from `Do not package` to `Package & store locally`. [Leave all the settings in here blank by default.](/images/tutorials/support-dedicated-servers/11_PackageSettings.png){:target="_blank"}
1. [Change Deploy mode to `Do not deploy`.](/images/tutorials/support-dedicated-servers/12_DeploySettings.png){:target="_blank"}
1. Click "Back" on the top right of this window to go back to the main Project Launcher Window.
1. Click the "Launch This Profile" button next to your new custom profile. [This button looks like the Play button in the level editor window.](/images/tutorials/support-dedicated-servers/13_LaunchProfile.png){:target="_blank"}
1. This will begin the process of cooking and packaging your dedicated servers for your selected platforms. This will take a while. When it is done, [it should look like this.](/images/tutorials/support-dedicated-servers/14_AutomationComplete.png){:target="_blank"}

# Locating your Dedicated Server Builds

Now that you have packaged your dedicated server builds, you can find them in your project's `Saved\StagedBuild` directory. If you have packaged your regular game builds, you'll see them listed here as `WindowsNoEditor` and `LinuxNoEditor` as well. You are free to copy these builds to your target machines and distribute them as you like.

# Note about running the Windows Dedicated Server

If you load the Windows Dedicated Server, it will seem that nothing loads up and that there is no UI or command prompt of any kind. If you open up your Windows Task Manager, you will see that your server is in fact running, but it is invisible. If you would like to see the log output of your Windows Dedicated Server, you need to run it with the `-log` command argument. The easiest way to do this is:

1. [Hold Shift and Right-click the folder your Windows Dedicated Server is in and choose "Open command window here."](/images/tutorials/support-dedicated-servers/15_WindowsCmdWindow.png){:target="_blank"}
1. Type in `ProjectServer.exe -log` and hit `Enter`. In my case, this is named `GenShooterServer.exe -log`
1. [This will load your Windows Dedicated Server with a log window.](/images/tutorials/support-dedicated-servers/16_LoadServerWithLog.png){:target="_blank"}

# Note about running the Linux Dedicated Server

After copying your files to your Linux server (which is outside the scope of this tutorial), you will need to run `ProjectServer` located in your builds `Project/Binaries/Linux/` folder. 

In my case that is, loading it from a terminal would look like:
{% highlight bash %}
GenShooter/Binaries/Linux/GenShooterServer
{% endhighlight %}

If you want to load it and then send it to the background so that it will not terminate when you close your terminal session, you can load it with:
{% highlight bash %}
nohup GenShooter/Binaries/Linux/GenShooterServer &
{% endhighlight %}

To kill a server that has been sent to the background, find it's process name using the command `top`, then route that name to `pkill`, which would look like this:
{% highlight bash %}
pkill GenShooterServe
{% endhighlight %}

Your process name is usually your server binary's name limited to 16 characters.
