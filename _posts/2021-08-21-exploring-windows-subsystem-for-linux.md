---
title: "Windows and Linux, Best Buddies! Exploring Windows Subsystem for Linux"
date: 2021-08-21T22:00:00-00:00
last_modified_at: 2021-09-05T17:09:00-00:00
excerpt_separator: "<!--after-excerpt-->"
categories:
  - blog
tags:
  - programming
---

I recently purchased a Windows 10 machine, but wanted to still enjoy the developer ecosystem and experience found on MacOS / Linux, and of course this is doable through dual-booting or virtual machines, but seemed like a hassle to set up and continously swap between. So I wanted to know how the dev experience on Windows had progressed since I last used it for software engineering over 2 years ago; I had spotted a few months back the release of Windows Terminal, which seemed slick and came with some bells and whistles around other similar command line apps such as Command Prompt (cmd) and PowerShell. It's through reading about Windows Terminal that I came across the relatively-recent introduction of "Windows Subsystem for Linux" (WSL), and it's been exactly what I needed to enjoy a delightful developer experience across Windows and Linux with barely any hassle.

<!--after-excerpt-->

Here I'm specifically referring to WSL version 2, which uses a lightweight virtualisation of a Linux kernel, as opposed to WSL version 1 which worked by instead using a partial emulation of a kernel (and as such lacked full compatibility and was slower).

This blog post is basically me keeping track of what I've done, but done so others may benefit and I can practice at writing about software development.

# Features

## Today

The great advantage of using WSL is that I can use Windows and Linux side-by-side in ways that each of them couldn't accompish on their own. For example, WSL allows me to open files in the Linux filesystem using Windows apps. If there's an application that is exclusive to Windows, not a problem, it now becomes part of my day-to-day toolbelt when working with Linux.

Let's say I have a Java project in my Linux filesystem. It has a `.sdkmanrc` file in the repository - cool, I can use [SDKMAN](https://sdkman.io/) (Software Development Kit Manager), an application for Mac and Linux which cannot natively run on Windows, to set my current Java version to that required by the repo, with command `sdk env`. That's me using a Linux app on a Linux system - no surprises so far...

So after using that Unix-exclusive application, let's say I now want to browse the repo directory using an integrated development environment (IDE) or similar text-parsing app that is Windows-exclusive. Well I can do that as my immediate next step without leaving the same command line I ran the SDK statement on. As long as the Windows application I desire is available on my environment variable PATH, I can access it from the Linux command line. One very quick example that does not require any setup once you have WSL running is, `notepad.exe ~/.bashrc`, which opens that file residing in my Linux system using the Windows app Notepad. It's not the most exciting example, but it demonstrates the cool possibilites!

Another advantage is, for example, how I might use Git within my Linux system, but take advantage of Windows Credentials to store my Github personal access token. Linux lacks a native credentials store similar to Windows Credentials or MacOS Keychain, so I can leverage my Windows system to provide this. Nice OS teamwork!

FYI, Windows makes the Linux filesystem accessible through the virtual directory path `wsl$\<name of linux distribution>\` (for us this will be `wsl$\Ubuntu-20.04\`). Likewise, Linux makes the Windows drive partitions accessible through mount points, at the path of `/mnt/c/`, where "c" is the name of the Windows drive.

## Future

Whilst it is currently a preview-only feature, Microsoft is also working on expanding WSL such that we will be able to run graphical user-interface (GUI) Linux apps on our Windows machine too!

# Setting Up WSL

## Requirements

* Windows 10 operating system (mininum required version is `1903` for x64 systems or `2004` for ARM64 systems, and if you update to the latest possible build you'll have less setup to do).

## Enabling WSL

**Info:** If you're on a recent Windows 10 build (post-2020), you can skip this step as the following is enabled by default. Still, it doesn't hurt to check.
{: .notice--info}

Go to `Control Panel` > `Windows Features` and tick the following checkboxes:

* Virtual Machine Platform
* Windows Subsystem for Linux

Changing these settings will now require a system restart before continuing.

## Obtaining a Linux Distro

We will require a Linux distribution. I have used Ubuntu in the past and been happy with it, so that's what I will choose here.

Open the Windows Store app and search for "Ubuntu": you should find at least two official Ubuntu "apps", `Ubuntu 18.04 LTS` and `Ubuntu 20.04 LTS`. At time of writing, the latter is the most recent available Ubuntu "app" available; it is the long-term support (LTS) version of Ubuntu released in April 2020, so click into that and click `Get` and `Install`. The download is <500 MB.

Once the installation is completed, click `Launch` to be presented with the "Ubuntu 20.04 LTS" terminal app.

**Info:** My progress was halted here by an error in this terminal app (`WSL 2 requires an update to its kernel component.`). The solution is to simply install a WSL2 update package
listed on [Microsoft's WSL installation guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package), then once that is installed, re-launch the Ubuntu terminal app (this can still be done from the Windows Store).
{: .notice--info}

We are prompted to create a user account with a username (`meszerus`, in my case) and password. Afterwards, we should be informed that the installation was successful, and be presented with an Ubuntu welcome message. You'll find that you can run Linux commands in this terminal now, as you interact with the Ubuntu distro.

## WSL Versions

Now open Command Prompt or Windows PowerShell, and run command:

```
wsl --list --verbose
```

You should see your `Ubuntu-20.04` distro is listed as Version `2`: that is to say, it is configured to use WSL2, rather than the original WSL.

If our Ubuntu distro is listed as Version `1` it can be upgraded with the command:

```
wsl --set-version Ubuntu-20.04 2
```

You can make the default WSL version to be `2` for future distro installations can be set with command:

```
wsl --set-default-version 2
```

## More Info

Recommended pages for more info (which certainly helped me on the above):

* [Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10) on Microsoft Docs.
* [Comparing WSL 1 and WSL 2](https://docs.microsoft.com/en-us/windows/wsl/compare-versions) on Microsoft Docs.
* [WSL 2: Getting started](https://www.youtube.com/watch?v=_fntjriRe48) on Youtube (by David Bombal).

# Improving the Experience

The above is all that is strictly required to get WSL2 usable on your machine, but the following is the steps I performed next to improve my experience.

## Windows Terminal

So I quickly noticed that the Ubuntu terminal app had an issue with unicode characters, and I was wanting to try out Windows Terminal anyway, so moving over to that was my next step. It can be downloaded and installed via the Windows Store.

When you launch the Terminal, we can click the down arrow icon to see our options. You'll notice that the option to launch a Ubuntu command line is among them - it's cool to see our WSL setup has been automatically detected and added for us. But if we go into `Settings`, under `Startup` we can choose to set the `Default profile` to `Ubuntu-20.04` (as I did), and then under the `Ubuntu-20.04` profile menu option, we can configure aspects of that terminal view.

Note that the `Command line` field specifies to run the WSL executable with distribution Ubuntu-20.04, but does not specify a user. Since a user is not defined, it will default to the one we created earlier (`meszerus`, for me). I bring this up because if for some reason you need to access the Linux distro as `root` user, you can specify that within the `Command line` field found here to `wsl.exe -d Ubuntu-20.04 --user root`. You can of course switch to `root` user once you are on the Linux command line, but if you break your default shell (Bash) like I did (as mentioned later on), accessing `root` via this Terminal config is a nice back-up plan.

I'm liking Windows Terminal so far; the settings and customisations are handy, as is the native WSL support and ability to multi-tab.

## Homebrew

The package manager I'm familiar on MacOS is [Homebrew](https://brew.sh/), and sure enough we can make use of it here too. Homebrew supports Linux and WSL explicitly, and was made available under the former name "Linuxbrew" but now just goes by the same name of Homebrew.

Homebrew can be installed with the command:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

At risk of trusting random commands on someone's blog, you can verify this is the correct command (and for the next few commands) from [their official site](https://brew.sh/). It will install to the directory `/home/linuxbrew/.linuxbrew`.

Before we can crack on utlising Homebrew from our shell, we want to run some commands that verify the installation.

```
test -d ~/.linuxbrew && eval $(~/.linuxbrew/bin/brew shellenv)
test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
test -r ~/.bash_profile && echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.bash_profile
```

This next command in particular is important for us to use it from the shell.

```
echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.profile
```

You will need to open a new command line, which can be done as a new tab in Terminal. Verify that Homebrew was installed successfully with command:

```
brew --version
```

You can demonstrate the package installation process with:

```
brew install hello
```

Then view what packages are installed with:

```
brew list
```

## Zsh

Z shell (Zsh) utilising the framework Oh My Zsh (OMZ) is my shell of choice. First, let's obtain Zsh with command:

```
brew install zsh
```

And then OMZ can be installed through the following command (can be verified from [OMZ's official site](https://ohmyz.sh/#install)):

```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Zsh can now be made to be your default shell you use with your command line, rather than the Bash shell we've been using thus far. You can make the swap to Zsh + OMZ with the command:

```
sudo usermod -s /home/linuxbrew/.linuxbrew/bin/zsh meszerus
```

Where `meszerus` is my user account name. Open a new Terminal tab and you'll find that you're immediately using the Zsh shell instead of the Bash shell.


## More Info

Recommended pages for more info (which certainly helped me on the above):

* Windows Terminal
  * [Troubleshooting in Windows Terminal](https://docs.microsoft.com/en-us/windows/terminal/troubleshooting#set-your-wsl-distribution-to-start-in-the-home--directory-when-launched) on Microsoft Docs.
* Homebrew
  * [Homebrew on Linux](https://docs.brew.sh/Homebrew-on-Linux) on Homebrew Docs.
  * [Using Brew on Windows 10 with Windows Subsystem for Linux (WSL)](https://medium.com/@edwardbaeg9/using-homebrew-on-windows-10-with-windows-subsystem-for-linux-wsl-c7f1792f88b3) on Medium (by Edward Baeg).
  * [How to Install Homebrew on Windows WSL Ubuntu, and fix “zsh: brew command not found” error](https://stackoverflow.com/questions/64680855/how-to-install-homebrew-on-windows-wsl-ubuntu-and-fix-zsh-brew-command-not-fo) on Stack Overflow.
* Zsh
  * [Usermod command in Linux with Examples](https://www.geeksforgeeks.org/usermod-command-in-linux-with-examples/) on GeeksForGeeks.

# Utilising Windows Apps

## Git Credentials Manager

We'll obviously be wanting to use Git within our Linux environment, but there is one issue with this: when it comes to
private repositories, we will need to input our credentials every single time. Whereas Windows has "Credentials Manager"
and  MacOS has its "keychain" for remembering credentials, Linux has no built-in comparison.

First, let's install Git via Homebrew:

```
brew install git
```

Then, let's configure it how we need:

```
git config --global user.name "Your Name"
git config --global user.email "youremail@domain.com"
```

It's not unlikely that we'll also want to make use of Git within our Windows environment, and so if we install Git on
Windows ([current version 2.33.0](https://git-scm.com/download/win)), it comes with "Git Credential Manager", which is
the Git bridge to Windows Credentials Manager. After installing Git for Windows, Linux can piggyback off its credentials
ecosystem with command:

```
git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/libexec/git-core/git-credential-manager-core.exe"
```

Now if we check out a private repo in Linux, it will prompt us for our credentials (such as a Github username and
personal access token), we input them on this first occasion, and ta-da, they are remembered for us, and we can
use those credentials across Windows and Linux.

## Docker

We can obviously use Docker directly within Linux, but by making use of Docker on Windows, we can enjoy the GUI as well
as just have a single Docker installation across Windows and Linux. So let's install
[Docker Desktop for Windows](https://docs.docker.com/desktop/windows/install/) (current version 4.0.0, which bundles
Docker Engine 20.10.8 and Docker Compose 1.29.2, among other things), and during the installation process
making sure to tick the option for "Install required Windows components for WSL 2".

Once it's installed, run the Docker app, accept the service agreement, and it will inform you that the Docker service
for WSL2 is now running. Now open a new Linux terminal, and run the following command to verify the installation:

```
docker --version
```

Now let's try the functionality of pulling a remote image and running it:

```
docker run hello-world
```

**Info:** For me, that command failed with error `Got permission denied while trying to connect to the Docker daemon
socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/create": dial unix
/var/run/docker.sock: connect: permission denied.` This will happen in a Linux shell that is stale (initialised before
Docker's install), so simply open up a new Linux shell and it shouldn't pop up again.
{: .notice--info}

## IntelliJ IDEA

Install IntelliJ IDEA (ideally through the [JetBrains Toolbox app](https://www.jetbrains.com/toolbox-app/)) in your
Windows environment. I'm not sure from which version IntelliJ supports WSL, but I'm using Community version `2021.2.1`.

If we wanted to create a new Java project, we would do so using the Java SDK installed in Linux, and set the project
location to be within the `\\wsl$` filesystem (i.e., `\\wsl$\Ubuntu-20.04\home\meszerus\workspace\demo-project`).
Alternatively if you wish to open an existing project, you can browse the `\\wsl$` filesystem from within the IntelliJ
app to open it, and it works just as well.

We can also make it so that the Terminal provided within IntelliJ loads our WSL Zsh shell by default for this Linux
repo, rather than the Windows cmd shell. From the Terminal window, click the down-arrow icon, select Settings, and under
`Shell path`, select the following from the drop-down (or enter it manually if it's missing):

```
wsl.exe --distribution Ubuntu-20.04
```

## Visual Studio Code

## More Info

Recommended pages for more info (which certainly helped me on the above):

* Git
  * [Get started using Git on Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-git) on Microsoft Docs.
* IntelliJ IDEA
  * [IntelliJ IDEA - integrated WSL](https://www.jetbrains.com/help/idea/how-to-use-wsl-development-environment-in-product.html) on Jetbrains docs.
  * [Freeze on opening project from WSL2](https://youtrack.jetbrains.com/issue/IDEA-273398) on Jetbrains issue tracker.
* Visual Studio Code
  * [Visual Studio Code - Developing in WSL](https://code.visualstudio.com/docs/remote/wsl) on Visual Studio Docs.

# Utilising Linux GUI Apps

## More Info

* [Run Linux GUI apps on the Windows Subsystem for Linux (preview)](https://docs.microsoft.com/en-us/windows/wsl/tutorials/gui-apps) on Microsoft Docs.