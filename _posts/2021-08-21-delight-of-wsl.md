---
title: "The Delight of Windows Subsystem for Linux"
date: 2021-08-21T22:00:00-00:00
last_modified_at: 2021-08-21T22:00:00-00:00
excerpt_separator: "<!--after-excerpt-->"
categories:
  - blog
tags:
  - programming
---

I recently purchased a Windows 10 machine, but wanted to still enjoy the developer ecosystem and experience found on MacOS / Linux, and of course this is doable through dual-booting or virtual machines, but seemed like a hassle to set up and continously swap between. So I wanted to know how the dev experience on Windows had progressed since I last used it for software engineering around 2ish years ago; I had spotted a few months back the release of Windows Terminal, which seemed slick and came with some bells and whistles around other similar command line apps such as Command Prompt (cmd) and PowerShell. It's through reading about Windows Terminal that I came across the relatively-recent introduction of "Windows Subsystem for Linux" (WSL), and it's been exactly what I needed to enjoy a delightful developer experience across Windows and Linux with barely any hassle.

Here I'm specifically referring to WSL version 2, which uses a lightweight virtualisation of a Linux kernel, as opposed to WSL version 1 which worked by instead using a partial emulation of a kernel (and as such lacked full system call compatibility and was slower).

<!--after-excerpt-->

# The Powerrrr!

The great advantage of using WSL is that I can use Windows and Linux side-by-side in ways that each of them couldn't accompish on their own. For example, WSL allows me to open files in the Linux filesystem using Windows apps. If there's an application that is exclusive to Windows, not a problem, it now becomes part of my day-to-day toolbelt when working with Linux.

Let's say I have a Java project in my Linux filesystem. It has a `.sdkmanrc` file in the repository - cool, I can use [SDKMAN](https://sdkman.io/) (Software Development Kit Manager), an application for Mac and Linux which cannot natively run on Windows, to set my current Java version to that required by the repo, with command `sdk env`.

Now that I've used a Unix-exclusive application, let's say I want to browse the repo directory using an integrated development environment (IDE) or similar text-parsing app that is Windows-exclusive. Well I can do that as my immediate next step without leaving the same command line I ran the SDK statement on. As long as the Windows application I desire is available on my environment variable PATH, I can access it from the Linux command line. One very quick example that does not require any setup once you have WSL running is, `notepad.exe ~/.bashrc`, which opens that file residing in my Linux system using the Windows app Notepad.

Another advantage is, for example, how I might use Git to source-control some of my Linux files, but take advantage of Windows Credentials to store my Github personal access token. Linux lacks a native credentials store similar to Windows Credentials or MacOS Keychain, so I can leverage my Windows system to provide this. Nice!

# Setting Up WSL

## Requirements

* Windows 10 operating system (mininum required version is `1903` for x64 systems or `2004` for ARM64 systems, and if you update to the latest possible build you'll have less setup to do).

## Enabling WSL

**Info:** If you're on a recent Windows 10 build (post-2020), you can skip this step as the following is enabled by default.
{: .notice--info}

Go to `Control Panel` > `Windows Features` and tick the following checkboxes:

* Virtual Machine Platform
* Windows Subsystem for Linux

Changing these settings will now require a system restart before continuing.

## Obtaining a Linux Distro

We will require a Linux distribution. I have used Ubuntu in the past and been happy with it, so that's what I will choose here.

Open the Windows Store app and search for "Ubuntu": you should find at least two official Ubuntu "apps", `Ubuntu 18.04 LTS` and `Ubuntu 20.04 LTS`. At time of writing, the latter is the most recent available Ubuntu "app" available; it is the long-term support (LTS) version of Ubuntu released in April 2020, so click into that and click `Get` and `Install`. The download is 400-500 MB.

Once the installation is completed, click `Launch` to be presented with a "Ubuntu 20.04 LTS" terminal app.

**Info:** My progress was halted here by an error in this terminal app (`WSL 2 requires an update to its kernel component.`). The solution is to simply install a WSL2 update package
listed on [Microsoft's WSL installation guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package), then once that is installed, re-launch the Ubuntu terminal app (this can still be done from the Windows Store).
{: .notice--info}

We are prompted to create a user account with a username (`meszerus`, in my case) and password. Afterwards, we should be informed that the installation was successful, and be presented with an Ubuntu welcome message. You'll find that you can run Linux commands in this terminal now, as you interact with the Ubuntu distro.

## WSL Versions

Now open Windows PowerShell and run command `wsl --list --verbose`. You should see your `Ubuntu-20.04` distro is listed as Version `2`: that is to say, it is configured to use WSL2, rather than the original WSL.

If our Ubuntu distro is listed as Version `1` it can be upgraded with the command `wsl --set-version Ubuntu-20.04 2`. Setting the default WSL version be 2 for future distro installations can be set with command `wsl --set-default-version 2`.

# Improving the Experience

The above is all that is strictly required to get WSL2 usable on your machine, but the following is the steps I performed next to improve my experience.

## Windows Terminal

So I quickly noticed that the Ubuntu terminal app had an issue with unicode characters, and I was wanting to try out Windows Terminal anyway, so moving over to that was my next step. It can be downloaded and installed via the Windows Store.

When you launch the Terminal, we can click the down arrow icon to see our options. You'll notice that the option to launch a Ubuntu command line is among them - it's cool to see our WSL setup has been automatically detected and added for us. But if we go into `Settings`, under `Startup` we can choose to set the `Default profile` to `Ubuntu-20.04` (as I did), and then under the `Ubuntu-20.04` profile menu option, we can configure aspects of that terminal view.

Note that the `Command line` field specifies to run the WSL executable with distribution Ubuntu-20.04, but does not specify a user. Since a user is not defined, it will default to the one we created earlier (`meszerus`, for me). I bring this up because if for some reason you need to access the Linux distro as `root` user, you can specify that within the `Command line` field found here to `wsl.exe -d Ubuntu-20.04 --user root`.