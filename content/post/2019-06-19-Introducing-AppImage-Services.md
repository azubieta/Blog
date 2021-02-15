---
layout: post
title:  "Introducing AppImage Services"
date:   2019-06-16 13:01:32 +0200
categories:
 - AppImage
tags:
 - UX 
 - Desktop Environments
comments: true
---

The AppImage project seeks to introduce into the GNU/Linux ecosystem to 
the best aspects of a MacOS app installation experience, while avoiding 
its drawbacks. But it faces the fact that there are many GNU/Linux Desktop 
Environments. Each one with different goal and opinion on how applications 
should be handled. **Trying to satisfy all of their requirements and 
restrictions seems to be a nearly impossible task**. 

A desktop environment is conformed by a set of different software components
such as file manager, applications launcher, software centers and others. All
of them interact in a different way with applications and this interaction
is ruled by the [FreeDesktop specifications](https://specifications.freedesktop.org/) 
(whether DE respect it or not is another topic). AppImage as single file
applications implies a new way of managing applications that need to be
fitted into the existent standards. This "way" must be consistent between
the different tools.

Therefore arises the need for an unified implementation of "the AppImage way" (TM).
Which summarizes to: applications packed as single files with icons, metadata, 
applications launcher integration, mime types integration and updates support. 
All of that available from the different desktop environment components.

# AppImage Services description
This tool should provide a API that could be used without restriction by 
every existent desktop environment. As there are many and each one uses
different technologies we can only rely on using a IPC technique being
DBus our best candidate as it\'s stable, tested and broadly adopted.

Each set of features will be exposed in a separated interface 
(like a micro-service) so the different clients applications doesn't have
to depend/implement and support a large API. Being the most relevant interfaces:
- Launcher: responsible for creating launcher entries and of course launch 
appimage files
- Updater: responsible for looking for updates and actually doing the update
- Inspector: responsible for reading applications metadata and contents
- Registry: responsible for keeping track of the applications in the system 
and the files they deploy

This solution should be packed in a self installable AppImage so no matter
the target system it could be installed and consumed. This represent our biggest 
source of uncertainty as it steeps away from the traditional package managers.

**Summarizing**, AppImage Services is a set of DBus services for managing
AppImage files in a FreeDesktop standards compatible way presented as a
self installable AppImage. 

The AppImage Services code can be found at https://www.opencode.net/azubieta/AppImageServices
binaries are available at https://www.pling.com/p/1315173/

# Installation
To install AppImage Services you will need a modern GNU/Linux system (>= Ubuntu 16.04)
with `systemd`. Upstart version still not available but contributions are welcome. 

```
wget https://www.opencode.net/azubieta/AppImageService/-/jobs/artifacts/master/raw/appimage-services-x86_64.AppImage?job=build:AppImage -O appimage-service.AppImage
chmod +x appimage-service.AppImage
# user only install
./appimage-service.AppImage self-install

# system wide install
sudo ./appimage-service.AppImage self-install
```