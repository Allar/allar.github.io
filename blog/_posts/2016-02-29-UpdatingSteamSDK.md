---
layout: post
title: "Updating UE4's Steam SDK"
tags: [Allar, ue4, tutorial, steam]
thumbnail: "UE4-Annoying-Hat.gif"
---

This document serves as a guide for updating the Steam SDK used by Unreal Engine 4. At the time of this writing, Unreal Engine 4 uses version v1.32 which is over a year old. Please note that you should only need to update the Steam SDK if you need newer features of the Steam platform, such as better Steam Controller support. <!-- more -->

Due to vast changes with the Steam Controller API, if you are using UE4 4.11 or older, you will need to drop in a replacement for the `SteamController` plugin as Epic's implementation is outdated and will not compile with newer Steam SDKs. I have provided a suitable replacement [here]().

_This guide was written using UE4 4.10. If using a later version, many steps here are subject to change and/or irrelevance_.

# Requirements

+ Unreal Engine 4 compiled from source code
	- [Epic's Documentation on how to do this](https://docs.unrealengine.com/latest/INT/Programming/Development/BuildingUnrealEngine/index.html){:target="_blank"}
	- [A Tutorial I wrote on how to do this](/2014/09/25/Build-Source/){:target="_blank"}
+ Access to the lastest Steamworks SDK
    - Download from the [Steamworks Portal](https://partner.steamgames.com/home/steamworks)
    - At the time of this writing, the latest version is v1.36

# Determining Your Current Version

You may not need to update if Epic updates the SDK after this post is written. Before trying yo update, you should determine your current version of the Steam SDK you are already using and consider if you need to update at all.

Navigate to your `Engine\Source\ThirdParty\Steamworks` folder and you should see a subfolder named something like of `Steamv132`. This folder name is indicative of the Steam SDK version you are running. At the time of this writing, UE4 4.11 uses version v1.32, and the current version of the Steam SDK is v1.36. If you find your Steam SDK is out of date and would like to update, please follow the rest of this guide.

# Updating the SDK

## Extracting The New Version

In addition to the SDK source files you will extract, you will also need to update the Engine's Steamworks binaries.

### Extracting Source Files

Create a folder in this `Engine\Source\ThirdParty\Steamworks` folder named `SteamvXXX` where `XXX` is the version you are updating to without the `.` punctionation mark. Be sure to include any letter suffixes. For example, when installing version v1.36, the folder you would create would be named `Steamv136`. If installing v1.35a, name the folder `Steamv135a`.

[![Source File Folder Creation](/images/tutorials/steam/update-sdk/SourceFolder.png)](/images/tutorials0/steam/update-sdk/SourceFolder.png)

Download the latest version of the Steamworks SDK from the Steamworks backend page, and extract the contents of the archive into the folder you created above. This should leave you with an `sdk` folder inside your `SteamvXXX` folder.

This will provide UE4 with the source files needed to be built against this version of the Steamworks SDK.

### Updating Steamworks Binaries

Create a new folder in `Engine\Binaries\ThirdParty\Steamworks` named the same as you did in the previous steps, for example, `Steamv136`. Within this folder, create folders named `Linux`, `Win32`, and `Win64`.

[![ThirdParty Binaries Folder](/images/tutorials/steam/update-sdk/ThirdPartyBinariesFolder.png)](/images/tutorials0/steam/update-sdk/ThirdPartyBinariesFolder.png)

For Windows 64-bit binaries, navigate to the folder `sdk\redistributable_bin\win64\` inside the Steamworks SDK version that you downloaded and copy all the files within to your Engine's `Engine\Binaries\ThirdParty\Steamworks\SteamvXXX\Win64`.

For Windows 32-bit binaries, navigate to the folder `sdk\redistributable_bin\` inside the Steamworks SDK version that you downloaded and copy the `steam_api.dll` and `steam_api.lib` files to your Engine's `Engine\Binaries\ThirdParty\Steamworks\SteamvXXX\Win32`.

For Linux binaries, navigate to the folder `sdk\redistributable_bin\linux32` inside the Steamworks SDK version that you downloaded and copy all the files within to your Engine's `Engine\Binaries\ThirdParty\Steamworks\SteamvXXX\Linux`. Even if you are running a 64-bit Linux, UE4 defaults to making 32-bit versions of your project for Linux.

## Update UE4's Steamworks SDK References
 
### Steamworks.build.cs

Inside the `Engine\Source\ThirdParty\Steamworks` folder there lies a `Steamworks.build.cs` file. We will need to make a change inside this file to tell UE4 to use the newer version of the Steam SDK rather than the current one.

Near the top of the file, you should see lines that read similar to:

``` cs
/** Mark the current version of the Steam SDK */
string SteamVersion = "v132";
```

Change the version here to the version you are installing, for example, if you are installing v1.36, your updated `Steamworks.build.cs` file should read:

``` cs
/** Mark the current version of the Steam SDK */
string SteamVersion = "v136";
```

### SteamController.cpp

Open `Engine\Plugins\Runtime\Steam\SteamController\Source\SteamController\Private\SteamController.cpp`.

Similar to the last step, near the top of the file you should see a line of code defining the Steam SDK Version. Change it to match your target SDK. For example, if installing v1.36:

``` cpp
/** Name of the current Steam SDK version in use (matches directory name) */
#define STEAM_SDK_VER TEXT("Steamv136")
```

### OnlineSubsystemSteamPrivatePCH.h

Open `Engine\Source\Runtime\Online\OnlineSubsystemSteam\Private\OnlineSubsystemSteamPrivatePCH.h`.

Near the top, look for the `STEAM_SDK_VER` defines and change them to your target SDK. Using v1.36 as an example:

``` cpp
/** Compile Steam SDK version in use */
#define STEAM_SDK_VER TEXT("1.36")
/** Path of the current Steam SDK version in use */
#define STEAM_SDK_VER_PATH TEXT("Steamv136")
```

### UEBuildConfiguration.cs

Open `Engine\Source\Programs\UnrealBuildTool\Configuration\UEBuildConfiguration.cs`.

Around line 242 you should see a function called `PostReset` and within that a `SteamVersion` reference. Update this to match your target version. Using v1.36 as an example:

``` cs
		/// <summary>
		/// Function to call to after reset default data.
		/// </summary>
		public static void PostReset()
		{
			// Configuration overrides.
			string SteamVersion = "Steamv136";
            
            // ...
        }
```

### LinuxPlatform.Automation.cs

Open `Engine\Source\Programs\AutomationTool\Linux\LinuxPlatform.Automation.cs`.

Around line 53, you should see another `SteamVersion` reference. Update this to match your target version. Using v1.36 as an example:

``` cs
// assume that we always have to deploy Steam (FIXME: should be automatic - UEPLAT-807)
{
    string SteamVersion = "Steamv136";
    
    // ...
}
```

### MacPlatform.Automation.cs

Open `Engine\Source\Programs\AutomationTool\Mac\MacPlatform.Automation.cs`.

Around line 130, you will see a long line of code that contains a directory path containing the Steam version. Update this path to match your target version. Using v1.36 as an example:

``` cs
if (Params.bUsesSteam)
{
    SC.StageFiles(StagedFileType.NonUFS, CombinePaths(SC.LocalRoot, "Engine/Source/ThirdParty/Steamworks/Steamv135a/sdk/redistributable_bin/osx32"), "libsteam_api.dylib", false, null, CombinePaths(AppBundlePath, "Contents/MacOS"), true);
}
```

## Compiling With the New Steamworks SDK

Provided that no breaking API changes were made, these are all the changes required to update the Steamworks SDK. You should be able to regenerate your Engine solution, compile your projects, and run game builds with Steam loading your new SDK version without issue.

At the time of this writing however, if you are performing an update from v1.32 to a newer version on 4.11 or older, you will encounter many errors related to the `SteamController` module. The Steamworks SDK introduced breaking changes to the Steam Controller API after version v1.32 so Epic's provided plugin will fail to compile. If you are encountering these errors, please follow the next section.

## Updating Epic's SteamController Plugin

At the time of this writing, UE4 4.11 and older provide an outdated implementation of the Steam Controller API in their `SteamController` plugin. In order to fix this issue when updating the Steamworks SDK, I have written a replacement plugin the `SteamController` module.

This section may become irrelevant if Epic ever updates their implementation.

### Downloading the SteamController Plugin Replacement

I have made my SteamController Plugin replacement available [at a repo on my GitHub](https://github.com/Allar/ue4-steamcontroller-update). Simply download this repo as a .zip.

This serves as a full drop-in replacement, so first you will need to delete your pre-existing SteamController plugin. To do this, navigate to your Engine's `Engine\Plugins\Runtime\Steam\SteamController` and delete *everything* inside.

Once the pre-existing plugin is deleted, extract the contents of the replacement's `Engine\Plugins\Runtime\Steam\SteamController` into this folder.

This replacement plugin is set to use Steamworks SDK v1.36. If you are not using v1.36, you will need to repeat the `SteamController.cpp` edit <a href="#steamcontrollercpp">detailed above</a>.

You should be able to regenerate your project files and compile as usual. Your Engine and game projects should now all be running the version of the Steamworks SDK you've updated to and initialize correctly.