---
layout: post
title: "Setting Up A Perforce Server"
tags: [Allar, perforce, source control, tutorial, Digital Ocean, Ubuntu]
---

# Perforce / Helix Name Change

So Perforce Software Inc. has recently changed their Perforce product name to Helix Versioning Engine. I will continue to refer to their software as Perforce and not as Helix because its Perforce damn it.

# Clients

This tutorial assumes all clients are on Windows machines. For Linux clients, the steps will still work but you need to know the Linux commandline equivalents.

# Running a Perforce Server on Linux

@todo

# Running A Perforce Server On Windows

1. Download Perforce Server installer.

If you would like to install the 2015.1 version of Perforce which has a 20 user / 20 workspace limit as opposed to the newer version which has a 5 user / 20 workspace limit, use this link to download:

ftp://ftp.perforce.com/perforce/r15.1/bin.winxpx64/helix-versioning-engine-x64.exe

Otherwise, download Perforce from their website at:

https://www.perforce.com/downloads/helix

2. Install Perforce Server with all default options.

3. If you are accessing this server from a different machine, make sure Windows Firewall allows Inbound Connections via TCP on port 1666. 

# Creating Your User Account

1. Install Perforce Client (P4V) on client machine with all default options.

2. Open a connection to the Perforce server. You are going to need to supply either an ip address or hostname of the Perforce server, and the Perforce server must be reachable from your client machine.

Enter your server's hostname or ip address in the Server field and make sure to include the `:1666` port otherwise the client will fail to connect.

Because this is a brand new server installation, no users will exist. To create your user account, click the `New...` button next to the user field.

[Image 1. New User]

3. Fill out your new user details and hit `Save...`

[Image 2. New User]

4. Your new user name should be shown in the User field. If not, type it in now. Be sure to choose a strong password that has at least 8 characters, one uppercase character, a number, and one symbol character to prevent errors at a later step. Then hit OK at the bottom.

[Image 3. Connect]

If everything was done right, you will then be greeted with this window. If you are prompted with a Wizard of any kind, simply close the wizard by hitting buttons such as `Do Not Use Wizard` or `Close Wizard`.

If you do not get the window in the following image, review the steps above again.

[Image 4. Connect]


# Designate Yourself As SuperUser

5. Log In As `SuperUser` by navigating to Tools->Administration. You will then be greeted with `superuser` confirmation, so click yes.

[Image 5. Confirm SuperUser]

# Configuring Your Server

By default, user accounts do not need passwords and anyone can create an account. This is terrible behavior, so we will address it. 

6. Enforce User Account Passwords by going to Administration -> Password Security Level in the Administration tool Choose Security Level 3 and hit OK.

If you get errors after hitting OK, your password is not strong enough. Click through all the errors, then navigate to the User & Groups tab. Right click your user and choose `Change Password...`. Then proceed to change your password.

7. Disable Public User Account Creation

This is where it gets a little tricky.

1. Close the Administration Tool and go back to the Perforce Client.

1. Enforce Perforce Client Environment Settings by navigating to Connection->Enivronment Settings.. and then clicking `OK` in the resulting pop up box.

[Image 8. Environment Settings]

1. Open up a Command Prompt by clicking the Start Icon and searching for Command Prompt.

Alternatively, hit Windows Key + R, type in `cmd`, then hit Enter.

1. In the new Command Prompt, type the following and hit enter.

`p4 configure set dm.user.noautocreate=2`

You should see the resulting text:

`For server 'any', configuration variable 'dm.user.noautocreate' set to '2'`

If so, move on to the next step. If not, check your spelling of the above command and try again.

1. Run the following command to disable unauthorized viewing of your Perforce user list:

`p4 configure set run.users.authorize=1`

You should see the resulting text:

`For server 'any', configuration variable 'run.users.authorize' set to '1'`

If so, move on to the next step. If not, check your spelling of the above command and try again.

1. Run the following command to disable unauthorized viewing of your Perforce config settings:

`p4 configure set dm.keys.hide=2`

You should see the resulting text:

`For server 'any', configuration variable 'dm.keys.hide' set to '2'`

If so, you are done configuring the Perforce server and can close your command prompt. If not, check your spelling of the above command and try again.