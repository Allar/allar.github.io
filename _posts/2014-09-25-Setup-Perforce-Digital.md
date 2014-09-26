---
layout: post
title: "Setting up version control (Perforce) for Unreal Engine 4 using DigitalOcean"
---

A common question with [Unreal Engine 4 (UE4)](http://www.unrealengine.com) is "how do you set up version control?" I think this is a very important question and if you have never used version control for your projects you are crippling yourself from the start. I won't go into details as to [why you should use version control](http://gameindustry.about.com/od/game-development/a/The-Importance-Of-Version-Control-Management-In-Game-Development.htm) or [how to use version control](http://betterexplained.com/articles/a-visual-guide-to-version-control/). I will be just covering how to get a basic setup going.

There are many types of source control that exist in the wild but for game development generally [Perforce](http://www.perforce.com/), [Git](http://git-scm.com/), and [Subversion](https://subversion.apache.org/) are used. There are many differences between these source control systems and whether or not one is better than the other can be debated endlessly. UE4 has editor support for both Perforce and Subversion, but a [community plugin for Git](http://srombauts.github.io/UE4GitPlugin/) also exists, although I'm not too familiar with it.

I will be focusing on Perforce as I find that most game development houses use it, including Epic, and although it has the disadvantage of requiring a server, Perforce supports up to 20 users/workspaces for free now and does a much much better job at handling binary files than both Git and Subversion; better binary file handling meaning less server space required for revision histories.

This setup is more geared to those who [build UE4 from source code](/2014/09/25/Build-Source/) but is also applicable for those who just want to host project files.

<!--more-->

## Perforce Hosting

Perforce is a centralized version control system, meaning it requires a server to be hosted on. You could run it locally and it is rather straightforward to set up, but I will be focusing on a more "proper" solution by setting up the Perforce host on a seperate [Ubuntu](http://www.ubuntu.com/) machine. If you plan to work with others remotely, I highly suggest renting a cloud server to put Perforce on so you don't have to worry about hardware maintenance, uptime, or bandwidth. There are a lot of server hosting solutions available, but for small projects, [I highly recommend DigitalOcean](https://www.digitalocean.com/?refcode=070b959bc226).

Running a lightweight Perforce server using [DigitalOcean](https://www.digitalocean.com/?refcode=070b959bc226) will cost you $20 a month, ($10 a month if you know your project will be quite small) but if you are serious about development it will save you countless hours and cash when you inevitably break your project and need to revert some changes, or if you are working with someone else remotely.

If you do end up using [DigitalOcean](https://www.digitalocean.com/?refcode=070b959bc226), please sign up using the links to it on this page as you get a $10 discount and I will get a much appreciated referral credit. Other than this, I am not paid in any way by [DigitalOcean](https://www.digitalocean.com/?refcode=070b959bc226) and I genuinely love their service.

## Creating a DigitalOcean Droplet

I will be writing how to set up a DigitalOcean Droplet, which is their term for a cloud instance, however this isn't absolutely required. I will be writing about how to set this up on a 64-bit Ubuntu/Linux system, so if you already have a Linux server up and running please skip this section. If you are installing a Perforce server on a Windows machine, using the server installer it is pretty straightforward and theres really nothing to write about.

1. [Create a DigitalOcean account](https://www.digitalocean.com/?refcode=070b959bc226) by clicking Sign Up on the top right of [this page](https://www.digitalocean.com/?refcode=070b959bc226).
2. Confirm your account with the email that DigitalOcean will send you.
3. Enter your credit card information or provide a payment upfront using PayPal. If using PayPal, I recommend a payment of $20 to get started.
4. Now you will need to create a droplet using the following settings.
    - Hostname should be ideally be short
    - I recommend the $20/month tier as it has enough space and bandwidth to last for awhile for a small project and provides you with enough ram and cpu to run other server tasks such as issue tracking or a [continuous integration](http://en.wikipedia.org/wiki/Continuous_integration) system like [Jenkins](http://jenkins-ci.org/) if you choose to in the future. You could get away with the $10/month tier though.
    - Select the region closest to you for lower latency.
    - You do not need any of the "Available Settings" unless you are interested in any of them.

![DigitalOcean Create Droplet Screen](/assets/DigitalOcean_CreateDroplet.JPG)
![DigitalOcean Select Region Screen](/assets/DigitalOcean_SelectRegion.JPG)

## Accessing your DigitalOcean Droplet

After you create your droplet, you will recieve an email containing its ip address and credentials. You will need this to log in.

> ![DigitalOcean Root Email](/assets/DigitalOcean_RootEmail.PNG)

To access your DigitalOcrean droplet you will need to [SSH](http://en.wikipedia.org/wiki/Secure_Shell) into it. Basically, this is a way to remote control a server but instead of seeing any form of user interface you can only interact with its shell/terminal/command prompt. The easiest way to do this is to download a program named [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). On the downloads page, all you need is `putty.exe`. If you are on OS X, [use this instead](http://kb.mediatemple.net/questions/1599).

1. Once you load PuTTY, enter your droplet's IP address in the Host Name box and click Open.
2. A PuTTY Security Alert will pop up asking if you trust the host. Hit Yes.
3. A shell will appear with the prompt `login as:`. Type in `root` and hit Enter.
4. Type in your password given to you in the droplet email.

    > When you type in your password, no characters will appear, but rest assured your input is being sent. A shortcut you can use is if you put your password in your clipboard, you can right click once to paste in the shell terminal and then hit enter.

5. You will be forced to change your root password. Enter your root password again, then enter a new password twice to change it. **Be sure to remember this password.**
6. If you end up at a prompt of `root@yourhostname:~#` or something very similar, you are now logged in.

![PuTTY Configuration](/assets/DigitalOcean_Putty.PNG)
![PuTTY Shell Login](/assets/DigitalOcean_ShellLogin.PNG)

## Installing Perforce Server

There are a lot of steps here that can be customized depending on your needs, but I will run through a standard basic installation.

### Downloading Server Binary

The first step is to download Perforce Server. To find the latest version of Perforce, you can view all versions of Perforce [here](ftp://ftp.perforce.com/perforce/). As of this writing on September 25, 2014, the latest Linux 64-bit version is `r14.1/bin.linux26x86_64` with download url `ftp://ftp.perforce.com/perforce/r14.1/bin.linux26x86_64/p4d`. To download this on the server, we can use the shell command `wget` to... get it.

        wget ftp://ftp.perforce.com/perforce/r14.1/bin.linux26x86_64/p4d
        
Enter the command above and your server should download the file directly.

![PuTTY P4D Download](/assets/DigitalOcean_P4DDownload.PNG)

### Prepping Server Binary

Linux shells usually require you to set an executable flag on downloaded files if you want to run them. Ubuntu and Perforce are no exception. To make the file executable, use:

        chmod +x p4d
        
Once the `p4d` server binary is executable, lets move it to `/usr/local/bin`

        sudo mv p4d /usr/local/bin
        
### Setting Up Basic Permissions Security

It is __highly not recommended that you run your Perforce server as `root`__. `root` has far too much power and if someone were to run bad things through your Perforce server (which is highly unlikely), it should be isolated in terms of what it can do on the system. To do this we are going to add a new user.

        sudo adduser perforce
        
This will prompt you to create a password and enter some details about the new user. These details are not too important and I just set the user's full name to Perforce.

![PuTTY Add Perforce User](/assets/DigitalOcean_AddPerforceUser.PNG)

### Creating the Perforce Server Root

Now we need to create the directories that will house our Perforce server database files and logs. These files essentially store the settings of your server. We will also need to give the `perforce` user permission to use these files.

        sudo mkdir /perforce_depot
        sudo chown perforce /perforce_depot
        sudo mkdir /var/log/perforce
        sudo chown perforce /var/log/perforce
        
### Setting Up Perforce As A Boot Daemon

We don't want to keep manually starting our Perforce server every time the DigialOcean server goes down, even if the server will generally never crash or hang. If we need to restart the server for any reason, setting up Perforce as a boot daemon will ensure it will always be running. First we need to go to the directory that contains a bunch of system initialization controls.

        cd /etc/init.d
        
Now we need a control script for our `perforce` user. I've been using [this script](/assets/shell_scripts/perforce) for awhile without any issue. I'm not exactly sure who the original author of it was. Here is the script in full for reference in case the script file somehow disappears off host. If [this link to download this script](/assets/shell_scripts/perforce) is working, you will not have to type this up and instead can download this script directly to your `/etc/init.d` directory directly with the following command:

        wget http://allarsblog.com/assets/shell_scripts/perforce
        
Full script for reference, which if you have to create manually, should be saved as `/etc/init.d/perforce`

```shell
#!/bin/sh -e

  export P4JOURNAL=/var/log/perforce/journal
  export P4LOG=/var/log/perforce/p4err
  export P4ROOT=/perforce_depot
  export P4PORT=1666

  PATH="/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin"

  . /lib/lsb/init-functions

  p4start="p4d -d"
  p4stop="p4 admin stop"
  p4user=perforce

  case "$1" in
  start)
    log_action_begin_msg "Starting Perforce Server"
    daemon -u $p4user -- $p4start;
    ;;

  stop)
    log_action_begin_msg "Stopping Perforce Server"
    daemon -u $p4user -- $p4stop;
    ;;

  restart)
    stop
    start
    ;;

  *)
    echo "Usage: /etc/init.d/perforce (start|stop|restart)
    exit 1
    ;;

esac

exit 0
```
        

### Installing Daemon

`daemon` is a linux tool that turns a program into a daemon. A daemon is a background task that runs behind everything and isn't directly interactable. This is important as we want our Perforce server to always be running in the background, otherwise if you start the server in your PuTTY shell, as soon as you quit your shell your Perforce server would also shut down. To install `daemon`, use the following command:

        sudo apt-get daemon






