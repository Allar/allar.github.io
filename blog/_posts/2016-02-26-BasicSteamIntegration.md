---
layout: post
title: "UE4 Steam Integration: First Steps"
tags: [Allar, ue4, tutorial, steam]
thumbnail: "UE4-Annoying-Hat.gif"
---

This is the first step in a series of guides that will show you how to enable the `OnlineSubsystemSteam` module for your project. It does not cover session creation, session management, app id association, or anything specific to `OnlineSubsystemSteam`. These steps are the first part in enabling a Steam integration into your Unreal Engine 4 Project whether its Blueprint or C++.<!-- more -->

If you are making a C++ project, you may need to follow some additional steps written here. (At the time of this writing, this information hasn't been written yet.)

## Results

The result of this guide should leave you with a project that has the Steam Overlay working correctly for `WindowsNoEditor` builds, with Steam showing you as playing Spacewar. Spacewar is a test game on Steam that everyone owns and eases Steam development.

If you already have session management working without Steam (i.e. your server browser works on LAN using `OnlineSubsystemNull`) then your session management logic should also work under Steam.

_PLEASE NOTE:_ By default, Steam sessions are created using 'Steam Presence', what this means is that instead of using Steam's master server list for global server searching, Steam will only return sessions within your Steam region and sometimes sessions related to the people on your friends list. If you enable Steam and are unable to see your buddy's server that happens to be on the other side of Earth, it is not an issue with your configuration but how Epic handles Steam sessions. To correct this, please refer to my `GenericPlatformTools` plugin. (At the time of this writing, `GenericPlatformTools` documentation does not exist.)

## Dedicated Server and Other Platform Support

If you are looking to get Steam running for any other build besides `WindowsNoEditor` such as a Windows dedicated server or any other platform, please follow this tutorial and move on to the next steps in this guide. (At the time of this writing, these tutorials do not exist, but please check back later.)

## Warning

Once you enable `OnlineSubsystemSteam`, testing your multiplayer games locally by having multiple clients run on the same machine will cause all sorts of failures as Steam is very against the idea of running multiple games under the same Steam account simultaneously. Instead, you will have to have multiple machines to test or run your game clients in isolated Virtual Machines.

If you need to test a multiplayer game locally, you should disable Steam by running `-nosteam` as a command argument or by switching back to `OnlineSubsystemNull`.

# Implementation

All the changes required for basic Steam implementation are done purely in the config files of your project, namely `DefaultEngine.ini`. To find `DefaultEngine.ini`, navigate to your project's directory then enter the `Config` subdirectory. You should see a list of config files including `DefaultEngine.ini`. Open up this file in a text editor such as (Notepad++)[https://notepad-plus-plus.org/] and make the following changes.

## Look For Existing OnlineSubsystem Settings

If you are using an Epic provided template, `OnlineSubsystem` settings should not exist in your `DefaultEngine.ini` config. If you are using a project such as [GenericShooter](https://www.unrealengine.com/marketplace/generic-shooter-sample-project), you may have some of these settings already defined.

Look through your config file and see if you can find config sections named `[OnlineSubsystem]` or `[OnlineSubsystemSteam]`. If you see these, be sure to change these sections of the config instead of creating new sections as described in the next step.

## Creating The OnlineSubSystem Settings

### [OnlineSubsystem]

The first batch of settings that we need to add to `DefaultEngine.ini` is the `[OnlineSubsystem]` block of settings. These settings here tell the engine what `OnlineSubsystem` to use for this project. In this case, we are setting it to Steam.

``` ini
[OnlineSubsystem]
PollingIntervalInMs=20
; Uncomment the following line to use the Null Subsystem
;DefaultPlatformService=Null
; Uncomment the following lines to use the Steam Subsystem
DefaultPlatformService=Steam
VoiceNotificationDelta=0.2 
```

If you don't have an `[OnlineSubsystem]` block of settings in your config, go ahead and add this to the very top of your config. Otherwise, edit your existing block to look like the one I provided.

The `;` character allows you to leave comments in config files. I have left these commented lines in so that if you would like to switch between `OnlineSubsystemNull` and `OnlineSubsystemSteam`, you can do so easily.

The `VoiceNotificationDelta` setting is an Epic recommended default when using Steam, however I'm not sure of the exact impact of this setting at this time.

### [OnlineSubsystemSteam]

Now that we told UE4 to use the Steam Subsystem for your project, we need to configure the `OnlineSubsystemSteam` module. Just like the setting block above, you will need to add/set these settings in your `DefaultEngine.ini` file.

``` ini
[OnlineSubsystemSteam]
bEnabled=true
SteamDevAppId=480
SteamAppId=480
GameServerQueryPort=27015
bRelaunchInSteam=false
GameVersion=1.0.0.0
bVACEnabled=1
bAllowP2PPacketRelay=true
P2PConnectionTimeout=90
; This is to prevent subsystem from reading other achievements that may be defined in parent .ini
Achievement_0_Id=""
```

These settings set various specific `OnlineSubsystemSteam` implementation details, such as what App ID to use, what port to listen for Steam Queries on, and achievement data. (At the time of this writing, my tutorials for the details about these settings such as Achievements is not written yet, but please check back soon.)

If you have your own Steam App ID, replace `480` above with your App ID.

### [/Script/Engine.Engine]

The last batch of settings we need to add to the `DefaultEngine.ini` config file are very likely to already exist in your current config. Search your config for a `[/Script/Engine.Engine]` section as that will be where we are making our changes. If this section absolutely does not exist in your `DefaultEngine.ini` files, please create it.

Once you find your `[/Script/Engine.Engine]` config section, remove any line that contains the word `NetDriverDefinitions`. We will be adding our own. Leave any other line in this section intact.

These are the settings you need to apply to your `[/Script/Engine.Engine]`:

``` ini
[/Script/Engine.Engine]
!NetDriverDefinitions=ClearArray
; Uncomment the next line if you are using the Null Subsystem
;-NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="/Script/OnlineSubsystemUtils.IpNetDriver",DriverClassNameFallback="/Script/OnlineSubsystemUtils.IpNetDriver")
; Uncomment the next line if you are using the Steam Subsystem
+NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="OnlineSubsystemSteam.SteamNetDriver",DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")
```

Once again, it is important that you remove any old `NetDriverDefinitions` settings and add these settings to the `[/Script/Engine.Engine]` block, but do not change any other settings you may have in this block. For example, if you are integrating Steam into the Vehicle Template, your final result would look like:

``` ini
[/Script/Engine.Engine]
+ActiveGameNameRedirects=(OldGameName="TP_VehicleBP",NewGameName="/Script/ProjectName")
+ActiveGameNameRedirects=(OldGameName="/Script/TP_VehicleBP",NewGameName="/Script/ProjectName")
!NetDriverDefinitions=ClearArray
; Uncomment the next line if you are using the Null Subsystem
;-NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="/Script/OnlineSubsystemUtils.IpNetDriver",DriverClassNameFallback="/Script/OnlineSubsystemUtils.IpNetDriver")
; Uncomment the next line if you are using the Steam Subsystem
+NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="OnlineSubsystemSteam.SteamNetDriver",DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")
```

These `NetDriverDefinitions` tell UE4 to use the Steam networking layer instead of the raw operating system based networking layer. This is important as Steam relies on this networking layer for better session management, voice, and packet relays.

# Testing Your Steam Implementation

Running your project within the UE4 Editor will not load the Steam Subsystem. In order to test your Steam implementation, you will need to run your game as either a Standalone game or as a cooked `WindowsNoEditor` game. Once you do, Steam should allow you to use its overlay by pressing Shift+Tab and if you are using the settings I provided, it should list you as playing SpaceWar, as shown in this picture.

[![Steam Overlay In-Game](/images/blog/steamguide/basic/SteamOverlayExample.png)](/images/blog/steamguide/basic/SteamOverlayExample.png)