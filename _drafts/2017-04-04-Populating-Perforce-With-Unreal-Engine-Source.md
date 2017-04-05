---
layout: post
title: "Populating Perforce with Unreal Engine Source"
tags: [Allar, perforce, source control, tutorial, ue4]
---

This guide is meant to shit on your face and tell you how to properly set up a source build of Unreal Engine on your Perforce server.

Please note that you should only do this if you specifically want to host your Engine source on your Perforce server. @TODO reword

This is a direct continuation from Setting Up A Perforce Server @TODO LINK. This tutorial assumes that you have an empty Perforce server with zero workspaces and you are populating it with the engine. Alternatively, you can also perform this tutorial on a brand new perforce depot. 

# Build The Unreal Engine From Source Code

Have a clean build of the Unreal Engine on your client machine. I recommend doing this in a clean folder with a very simple path, such as `d:\415`. We will be coming back to this later.

# Creating a Perforce Workspace

A Perforce workspace is basically a local copy a project that you can work on, while pulling updates and pushing changes to the Perforce server. You will have to do this for every client who is accessing the Perforce server.

To create a workspace, choose `New Workspace` from the Workspace dropdown.

[Image 9. Create Workspace]

We are going to create this workspace so that it is properly set up for working with Unreal Engine. The details of this are out of the scope of this guide, but using these settings will ensure a good workspace.

## Workspace Settings

1. Your Workspace Name should be short and simple. I tend to use my last name followed by where this workspace is, such as Allar_Office.

1. Your Workspace Root should be also as short as possible. I prefer to use the path `d:\depot`, but as I already have a depot there, for this guide I will use `d:\demodepot`.

1. Do not hit OK just yet, make sure your basic settings look something like this and then switch over to the Advanced tab.

[Image 10. Workspace Settings]

1. Leave all the settings at default except change the ones circled here:

[Image 11. Workspace Advanced Settings]

1. You will be prompted with an Add Files Wizard, simply click `Cancel` to dismiss it.

[Image 12. Workspace]

# Set Up Perforce File Mappings

Perforce treats files differently based on their extension. For example, any code files are treated as text files that can be `diffed`, and any files it doesn't recognize are usually treated as binary files that only one person can check out and modify at a time.

It is really helpful to set up file mappings for Unreal Engine related files so that Perforce knows how to handle them correctly.

1. First ensure your machine's environment variables are set by navigating to Connection->Environment Settings and clicking OK.

1. Then open a Command Prompt using the Start menu or by hitting Windows Key + R, typing in `cmd`, and hitting Enter.

1. In Command Prompt, enter the following command and hit Enter:

`p4 typemap`

Your text editor should load with a file that looks something like:

[Image 13. Typemap]

1. Now replace the contents of that file with the following. Please note that if you set up your Perforce server so that your depot name is no longer `depot`, please replace references to `depot` in the following text.

```
# Perforce File Type Mapping Specifications.
#
#  TypeMap:             a list of filetype mappings; one per line.
#                       Each line has two elements:
#
#                       Filetype: The filetype to use on 'p4 add'.
#
#                       Path:     File pattern which will use this filetype.
#
# See 'p4 help typemap' for more information.

TypeMap:
                binary+w //depot/....exe
                binary+w //depot/....dll
                binary+w //depot/....lib
                binary+w //depot/....app
                binary+w //depot/....dylib
                binary+w //depot/....stub
                binary+w //depot/....ipa
                binary //depot/....bmp
                text //depot/....ini
                text //depot/....config
                text //depot/....cpp
                text //depot/....h
                text //depot/....c
                text //depot/....cs
                text //depot/....m
                text //depot/....mm
                text //depot/....py
                binary+l //depot/....uasset
                binary+l //depot/....umap
                binary+l //depot/....upk
                binary+l //depot/....udk
```

1. Save and close the text file and your command prompt should print out:

`Typemap saved.`

If so, move on to the next step. If not, review the above steps and try again.

# Set Up Ignore File

De default Perforce is going to want to have control over a ton of files it doesn't need and would infact interfere with productivity. We can tell Perforce to ignore these files by using a `.p4ignore` file.

## Creating The Ignore File

1. Create a `.p4ignore` file.

To do this, open up Notepad and save a blank file with name `.p4ignore` inside your Workspace Root folder i.e. `d:\demodepot`.

1. Then in Notepad, copy paste the following into this file and save it:

```
Binaries/
Saved/
Intermediate/
DerivedDataCache/
*.pdb
obj/
*.vcxproj
*.sln
*-Debug.*
FileOpenOrder/
```

Note: If you know you would like to keep `.pdb` files on the server, remove that line from the ignore text above.

## Telling Perforce To Use This Ignore File

1. Open up a Command Prompt and navigate to your workspace root.

If your workspace root for example is `d:\demodepot`, you can do this by entering the following command and hitting Enter:

`cd d:\demodepot`

If your workspace root is not on C, such as my example here, you will then need to run the following command, replacing `d` with your workspace drive letter.

`d:`

2. Run the following command:

`p4 set P4IGNORE=.p4ignore`

If successful, this will not output anything. If it does output an error, review the above steps and try again.

## Submitting The Ignore File

We are now going to submit our first file to the Perforce server, specifically this new ignore file. To do this...

1. Find the file in the Workspace tree in the Perforce Client, right click it, and click `Mark for Add...`

[Image 14. Mark for Add]

This will add this ignore file to our default changelist. Changelists are a list of changes that you've made locally that have not been submitted to the Perforce server yet.

1. Find the default changelist in our Pending Changelists tab. If you do not see the Pending Changelists tab, open it by navigating to View->Pending Changelists.

1. Right-click the changelist and click Submit.

[Image 15. Submit]

1. Enter a description for this changelist such as 'Submitting Ignore File.' and then click Submit.

[Image 16. Submit Message]

This ignore file is now stored on the Perforce server.

# Putting The Unreal Engine Build On Perforce

1. Move everything in your build directory to your workspace. Your workspace root should look like this:

[Image 17. Copy Into Workspace]
