---
title: "Packing Portable Windows Apps for Linux"
date: 2021-06-23T00:00:00-06:00
draft: false
categories:
- appimage
- appimage-builder
- linux
- windows
image: banner.png
--- 

Do you have a portable application that is only available for Windows and you want to run it on Linux? Then you have
come to the right place. In this tutorial you will learn how to pack [Wine](https://www.winehq.org/) plus your portable
application binaries into an AppImage. This will allow you to keep the portable nature of the package and run it in
almost every GNU/Linux distribution.


Requirements
============

In order to follow this tutorial you will need an Ubuntu/Debian system
with [appimage-builder](https://appimage-builder.readthedocs.io/)
installed. Check
the [appimage-builder installation guide](https://appimage-builder.readthedocs.io/en/latest/intro/install.html)

You will also need a copy of your application binaries, in the tutorial we will be using
the [Notepad++ binaries](https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.1/npp.8.1.portable.x64.zip)

Creating the recipe
===================

Instead of creating a recipe from scratch we will be using the example wine recipe from the appimage-builder project.
Let's download it
from [here](https://raw.githubusercontent.com/AppImageCrafters/appimage-builder/master/examples/wine/AppImageBuilder.yml)
and place it in a file name "AppImageBuilder.yml".

```shell
  mkdir notepad++-appimage
  cd notepad++-appimage
  wget https://raw.githubusercontent.com/AppImageCrafters/appimage-builder/master/examples/wine/AppImageBuilder.yml 
```

Now let's proceed to edit the recipe with your favourite text editor. The first thing we are going to do is to add a
script section, this will be executed by appimage-builder at the begining of the packaging process. In this section we
will download the NotePad++ binaries and icon and place them into the AppDir as follows:

```yaml
script:
  # remove AppDir from previous builds, it's always a good idea to start fresh.
  - rm -rf $APPDIR || true
  # download npp binaries
  - wget -c https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.1/npp.8.1.portable.7z
  # create a new AppDir
  - mkdir -p $APPDIR/npp
  # extract binaries
  - 7z -o$APPDIR/npp x ./npp.8.1.portable.7z
  # download icon for our bundle
  - mkdir -p $APPDIR/usr/share/icons/hicolor/scalable/apps
  - wget https://notepad-plus-plus.org/images/logo.svg -O $APPDIR/usr/share/icons/hicolor/scalable/apps/npp.svg
```


Now we must proceed to edit the `app_info` section with our application information. Then we will add an `exec_args`
with the path to our application main executable:


```yaml
  app_info:
    id: org.notepad-plus-plus.npp
    name: npp
    # the icon mame will not contain the extension
    icon: npp
    version: "8.1"
    # we will use wine as entrypoint
    exec: usr/bin/wine
    # set the Windows application main executable as first argument, then append the rest of the arguments ($@)
    exec_args: $APPDIR/npp/notepad++.exe $@
```

Making the package
==================

We are ready to build our AppImage by running `appimage-builder`. This will run the script section first, then deploy 
the wine binaries into the AppDir, configure the runtime and run the tests. Your application will be executed in 
different target system, so you can be sure that it will also run on your users system. Finally, an AppImage file
will be created in your working directory.


*Tests are executed in docker containers therefore your user must be able to access docker without password. 
Check out [this link](https://docs.docker.com/engine/install/linux-postinstall/)* 


Complete recipe for NotePad++
=============================

```yaml
version: 1
script:
  # remove AppDir from previous builds, it's always a good idea to start fresh.
  - rm -rf $APPDIR || true
  # download npp binaries
  - wget -c https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.1/npp.8.1.portable.7z
  # create a new AppDir
  - mkdir -p $APPDIR/npp
  # extract binaries
  - 7z -o$APPDIR/npp x ./npp.8.1.portable.7z
  # download icon for our bundle
  - mkdir -p $APPDIR/usr/share/icons/hicolor/scalable/apps
  - wget https://notepad-plus-plus.org/images/logo.svg -O $APPDIR/usr/share/icons/hicolor/scalable/apps/npp.svg

AppDir:
  path: ./AppDir

  app_info:
    id: org.notepad-plus-plus.npp
    name: npp
    # the icon mame will not contain the extension
    icon: npp
    version: "8.1"
    # we will use wine as entrypoint
    exec: usr/bin/wine
    # set the Windows application main executable as first argument, then append the rest of the arguments ($@)
    exec_args: $APPDIR/npp/notepad++.exe $@

  apt:
    arch: [amd64, i386]
    sources:
      - sourceline: 'deb http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse'
      - sourceline: 'deb http://archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse'
      - sourceline: 'deb http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse'
        key_url: 'http://keyserver.ubuntu.com/pks/lookup?op=get&search=0x3b4fe6acc0b21f32'
      - sourceline: 'deb https://dl.winehq.org/wine-builds/ubuntu/ focal main'
        key_url: 'https://dl.winehq.org/wine-builds/winehq.key'

    include:
      - winehq-stable
      - libfreetype6
      - libfontconfig1
    exclude:
      - dpkg

  files:
    exclude:
      - usr/lib/x86_64-linux-gnu/gconv
      - usr/share/man
      - usr/share/doc/*/README.*
      - usr/share/doc/*/changelog.*
      - usr/share/doc/*/NEWS.*
      - usr/share/doc/*/TODO.*
  runtime:
    path_mappings:
      - /opt/wine-stable:$APPDIR/opt/wine-stable

  test:
    debian:
      image: appimagecrafters/tests-env:debian-stable
      command: "./AppRun --version"
      use_host_x: True
    centos:
      image: appimagecrafters/tests-env:centos-7
      command: "./AppRun --version"
      use_host_x: True
    fedora:
      image: appimagecrafters/tests-env:fedora-30
      command: "./AppRun --version"
      use_host_x: True
    ubuntu:
      image: appimagecrafters/tests-env:ubuntu-xenial
      command: "./AppRun --version"
      use_host_x: True
    arch:
      image: appimagecrafters/tests-env:archlinux-latest
      command: "./AppRun --version"
      use_host_x: True


AppImage:
  arch: x86_64

```
