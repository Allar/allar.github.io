---
layout: post
title: "Enabling UE4's Crash Video Recorder"
tags: [Allar, ue4, tutorial]
thumbnail: "UE4-Annoying-Hat.gif"
---

Long time users of Unreal Engine 4 might remember an old feature that used be in the top right of the editor that would allow you to save the last 20 seconds of your editor's activity as an uncompressed .AVI. This recording would also auto-save on crash so that if you ever experienced a crash, you have a video recording of what caused it. You can enable it by using the `-forceenablecrashtracker` argument. More detailed information about this feature below. <!-- more -->

I believe this was disabled by default around ~4.5 with the following hardcoded `false`:

{% highlight cpp %}
#if UE_BUILD_DEVELOPMENT
		bCrashTrackerShouldBeEnabled =
			false && // Disable crash tracker by default for now unless someone enables it.
			!GIsDemoMode &&
			!FParse::Param( FCommandLine::Get(), TEXT("disablecrashtracker") ) &&
			!FApp::IsBenchmarking() &&
			!FPlatformMisc::IsDebuggerPresent();
#endif
{% endhighlight %}

The next line after this hardcoded disabling however allows you to re-enable it with a command-line argument:

	bool bForceEnableCrashTracker = FParse::Param( FCommandLine::Get(), TEXT("forceenablecrashtracker") );
	
So by passing `-forceenablecrashtracker` on your editor's command-line you can bring back this rolling 20 second video capture. I prefer to load my editor through a batch file but this will also work if you use a desktop shortcut. As long as you can add a command-line argument, you are able to launch your editor with this flag. My batch file for example runs:

	start Engine\Binaries\Win64\UE4Editor.exe GenericShooter -forceenablecrashtracker
	
The `start` keyword tells Windows to simply start the editor and not wait for it, thus preventing a command prompt window from floating around. Next you'll see the path to my editor, which here is relative. The third argument is the project I want to load, in this case `GenericShooter`, so that I can skip the project selection window. The last argument is what this post is about, `-forceenablecrashtracker`, and is the magic to restoring the rolling video capture.

Once enabled, as of 4.11 Preview 1 you will see a red circle on the top right of your editor. Clicking this will output an uncompressed `.AVI` and give you a notification as to where that `.AVI` was saved.

[![Red Circle](/images/blog/enablingvideorecorder/RedCircle.png)](/images/blog/enablingvideorecorder/RedCircle.png)

If your editor crashes, you will also see a `CrashVideo.avi` in your project's `Saved\Logs` folder. These videos only capture Unreal Engine windows but will always be the size of your full desktop. Anything that isn't Unreal Engine becomes filled with red. Here is an example of a frame of a recorded video of mine when running the editor in a ~2k window while on a 4k display.

[![Crash Video](/images/blog/enablingvideorecorder/CrashVideo.png)](/images/blog/enablingvideorecorder/CrashVideo.png)

Crash videos do not archive themselves unfortunately like logs do. If a `CrashVideo.avi` exists and a new crash occurs, the existing video will be erased. Manually saved videos do archive themselves by incrementing a number in the filename on each save.