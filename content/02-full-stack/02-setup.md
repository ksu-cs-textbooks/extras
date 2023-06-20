---
title: "Setup"
pre: "2. "
weight: 20
---

## Prerequisites

The basic environment required for this setup:

* A Linux/Unix system
  * Ubuntu/Debian is preferred. Mac OS X (Darwin) should also work.
* Docker Desktop
* Visual Studio Code
* Git and GitHub

This guide will use the following environment:

* Windows 10 Host OS (should work on Windows 11 as well)
* [Windows Subsystem for Linux WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install)
* [Ubuntu LTS on WSL 2](https://ubuntu.com/tutorials/install-ubuntu-on-wsl2-on-windows-11-with-gui-support#1-overview)
  * You do not need to install a GUI package as described in this guide
* [Git installed on Ubuntu in WSL 2](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-22-04)
  * You may also wish to either set up [Git Credential Manager](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git) to share Git credentials with Windows or set up [SSH Keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/about-ssh) to communicate with GitHub
* [Docker Desktop with WSL 2 Backend](https://docs.docker.com/desktop/windows/wsl/)
* [Visual Studio Code and WSL Extension](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode)
  * You may also wish to install the [Dev Containers](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers) extension. This guide will not use it, but that is an alternative way to develop within a container.

## Optional

To match the environment seen in the screenshots/videos, you can install these optional items:

* [Windows Terminal](https://learn.microsoft.com/en-us/windows/terminal/install)
* [FiraCode](https://github.com/tonsky/FiraCode) font in VSCode
* Zsh terminal and [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh) in Ubuntu
* [Powerlevel10k](https://github.com/romkatv/powerlevel10k) Zsh theme
  * [Meslo Nerd Font](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k) is recommended

## Other Systems

This guide should generally work for developing on both Ubuntu/Debian Linux systems as well as Mac OS X systems. Some modifications will need to be made, but in general you want to have Visual Studio Code, Docker Desktop, and Git installed. 

Likewise, deployment will mostly use GitHub actions but I will also include information for using GitLab runners. 

## Helpful Resources

* [Set up a WSL development environment](https://learn.microsoft.com/en-us/windows/wsl/setup/environment)
* [WSL Filesystems](https://learn.microsoft.com/en-us/windows/wsl/filesystems)