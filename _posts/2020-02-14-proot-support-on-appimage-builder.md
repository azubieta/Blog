---
layout: post
title:  "PRoot support on appimage-builder"
date:   2020-02-14 00:00:00 +0400
categories: AppImage Packaging Tools AppImageBuilder proot
---


PRoot allows running applications in a fake root fs without root permissions making it ideal to build portable bundles. 
`appimage-builder` now supports building AppImages using this approach. In this post, you will find how
it works and how to use it.

# Portable bundles and the resources resolution issue

To build an AppImage is required to pack the target application with all of its runtime dependencies and resources. 
Then comes the interesting part, configuring each piece of software to use the bundled resources and collaborate 
with the others. *In short, all the bundled binaries must be relocatable*.

Making relocatable binaries is not a trivial task if the source code is not accessible. Even if the source code is
available the components that aren't relocatable must be modified and rebuilt along with the app which implies a lot 
of effort that is not related to the app purpose.

A major part of the system components can be configured. Environment variables, configuration files, and other 
techniques are used for this purpose but there is no a standard way of telling every piece of software where to locate 
its dependencies and resources.

# chroot to the rescue

The Linux kernel allows changing the root filesystem for a given group (process). This can be used to solve the 
above-mentioned issue but there is a problem: `chroot` requires superuser privileges. Luckily for us, there are ways of 
*faking* it. 

[Ptrace](https://en.wikipedia.org/wiki/Ptrace) is a system call that allows us to control, intercept and manipulate 
other systems calls. This can be used to rewrite every file related operation making it seems like they are being opened 
in the root files system. By example: `/lib/x86_64-linux-gnu/libc-2.27.so` can be translated to 
`/tmp/.mount-032940234/lib/x86_64-linux-gnu/libc-2.27.so`. So if the files are deployed in the AppDir in the same 
places that they are expected to be deployed in the system everything should work as expected. There is no need to patch
*elf* files, writing *N+1* configuration files or patching source code.

[PRoot](https://proot-me.github.io/) does exactly what is mentioned above and a bit more. It allows binding parts of 
the real rootfs into the fake one making the system resources available into the app environment. This makes is a good 
candidate to be used while creating an AppImage bundle.


# PRoot support on appimage-builder

Creating AppImages using this approach is quite simple now in `appimage-builder`. To enable it, set 
`AppDir::runtime::generator` to `proot` and include the proot binaries in the bundle. The following `AppImageBuilder.yml`
snippet illustrates how to do it:

```yaml
AppDir:
  apt:
    # your whole apt configuration goes here
    include:
     - proot
  
  runtime:
    generator: proot
```

In the back *appimage-builder* generates an AppRun file which calls proot by means of the embed *ld.so* ensuring
it doesn't load any binary from the base system and effectively launching the app inside a fake root environment
with the following paths bound from the real file system:

 - /etc/host.conf
 - /etc/hosts
 - /etc/hosts.equiv
 - /etc/mtab
 - /etc/netgroup
 - /etc/networks
 - /etc/passwd
 - /etc/group
 - /etc/nsswitch.conf
 - /etc/resolv.conf
 - /etc/localtime
 - /dev/
 - /sys/
 - /proc/
 - /tmp/
 - /run/
 - /var/run/dbus/system_bus_socket
 - $HOME
 - /usr/share/icons

# Conclusions

As this is a quite new (at least to me) and promising way of creating AppImage bundles it's not quite tested yet and 
depends on the `proot` ability to really intercept every file related syscall. Therefore it issues may raise in certain
environment in such cases please drop by our [Github rerpo](https://github.com/AppImageCrafters/appimage-builder) and 
fill an issue so we can take a look at it and provide a solution.

Positive feedback in the form of github stars, word spreading or pull request is highly appreciated.
Thanks for reading.