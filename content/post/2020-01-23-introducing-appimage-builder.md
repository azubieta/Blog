---
layout: post
title:  "Introducing AppImage Builder"
date:   2020-01-23 13:01:32 +0200
categories:
 - AppImage
 - appimage-builder
tags:   
 - Packaging 
 - Tools
---

[AppImage Builder](https://github.com/appimagecrafters/appimage-builder) is a recipe based [AppImage](https://appimage.org/) creation tool. That allows producing self-sufficient bundles compatible 
with a large array of GNU/Linux systems.

![AppImage Builder Logo](/assets/appimage-builder-logo.png){: .center-image }

Distributing a software application in GNU/Linux is a complex task. The only fixed thing is the Linux kernel, besides 
that, you can find a wide array of base libraries, services, graphic toolkits, audio systems, window management systems, 
desktop environments and other components of a "modern operating system". Therefore app developers aiming to create 
binaries that will run properly in the majority of the GNU/Linux flavors must bundle everything.

[Traditional AppImage creation tools](https://github.com/linuxdeploy/linuxdeploy) based its behavior on resolving runtime dependencies (shared library requirements). 
This method allows for gathering a quite large number of dependencies but has some significant flaws:
- the C standard library is not embedded, limiting the compatibility of the binaries along with the library backward and 
forward compatibility
- plugins and other types of binaries that are loaded at runtime are not embedded and the work is delegated to the 
developer

AppImage Builder, on the other hand, uses the information contained on the system packages to resolve all the 
application dependencies including icons, fonts, and other resources. Which reduces the developer's work to only 
listing the first level of dependencies.

The inclusion of the C standard library binaries and the related linker/loader as part of the resulting bundle 
increases overall package portability. This kind of packages can be produced using the latest versions of the components 
embed and yet it will work on older systems. An interesting side-effect is that a bundle produced for a foreign can be 
executed using `qemu-static` without any special effort.

There is much more to explore on the AppImage Builder and the bundle all AppImages world. In a future post, I'll explain 
in more detail how to create them.
Stay tuned!

The AppImage Builder source code can be found at [https://github.com/appimagecrafters/appimage-builder]()

