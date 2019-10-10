---
layout: post
title:  "Using Docker to produce AppImages"
date:   2019-06-16 13:01:32 +0200
categories: AppImage Build Tools
comments: true
---

# Introduction
In order to create an AppImage compatible with a wide range of GNU/Linux systems the 
developer must **choose as build base the oldest LTS system** available in the 
market (which is Centos 6 at the time of writing this post). Why?

An AppImage package is a trade off between size and compatibility where some system libraries 
are left outside the package for different reasons: _glibc_ doesn't get well with older or 
newer versions of itself so if a given application tries to interact with another and they 
use different versions of _glibc_ a crash is expected. _Next time you found a glibc developer 
please ask them to respect backward and forward compatibility_. Other situation is the case 
of _libssl_, if embed into the AppImage it may not receive updates and your app could end 
exposing your network traffic due and old bug.

In resume __not all dependencies are bundled but those that are not expected to be present by
default in your target systems__. So if our application is built over old enough base libs
and the libs developers were nice at keeping forward compatibility is guaranteed that your
final bundle will run in all the systems with core libraries newer than the ones used tho
build the product.

# The problem
Sound easy, right? It's just a matter to build your app on Centos 6 and we are good to go.
I'm sorry to tell you that provably there will be no binaries of your dependencies available
for Centos 6 and you will have to build it manually and you will also have to deal with weird
toolchains to get gcc-7 on it. But __don't despair__ we are here to
help. 

The [__AppImageCarfters__](https://github.com/AppImageCrafters) initiative aims to be a community driven effort to ease AppImage
production. By creating ready to use recipes and binaries of common dependencies and 
toolchains we want to reduce the work of creating an AppImage to just setting up your
app build script and sharing the effort of building dependencies.

In a [previous post](https://azubieta.net/appimage/conan/qt/example/2019/05/16/QtWidgetsApplicationToAppImage.html)
we show how to *conan could help in this task* now is time to give a chance to *Docker*. As a containerization solution
it allow us to create a whole system independent from the one started on your machine with really low overheat.
So we can create common base system with all requirements to ensure the major compatibility
of the produced AppImages, the tools too do it and the common dependencies like frameworks.

And that's exactly what is `apppimagecrafters/docker-linuxdeploy`. It gives you a build system
with gcc-7, glib-2.44.0, autotools-1.16.1, autoconf-2.69 and cmake-3.15.4 all ready to be used.

# Example

Here is an __example__ gitlab-ci script of how it could be used to build an AppImage from
a [HelloWorld C++-11 application](https://www.opencode.net/azubieta/cpp-appimage-template):
```yaml
build:AppImage:
  image: appimagecrafters/docker-linuxdeploy
  script:
    - cmake -DCMAKE_INSTALL_PREFIX=/usr
    - DESTDIR=AppDir make install
    - linuxdeploy --appdir=AppDir --output appimage --desktop-file=AppDir/usr/share/applications/net.azubieta.cpp_appimage_template.desktop
```
**Looks great, right?** Goodbye to hacks and hello to smooth AppImage creation. 

_I wonder if it could be simpler than that?_ 

Notice that the application built above is a full desktop app that installs its own 
desktop entry and icon, which are mandatory requirements for building an AppImage.

# Missing dependencies
If one of your dependencies is not in the _AppImageCrafters_ repo you can create a Docker
recipe using _appimagecrafters/docker-base_ as base system. Make sure it gets installed 
into `/usr/local` by setting it as install prefix. Finally in your target build image
use the _COPY_ Docker instruction like this:

`COPY --from=me/my-custom-built-library /usr/local /usr/local` 

You will get all the binaries built on `me/my-custom-built-library` into your system and
as they were built over the same base system there will be no compatibility issues.
 

# Contributing

If **you already distribute your app as an AppImage** we encourage you to submit the recipes of
your dependencies to the project so every one can benefit from them and also contribute.

**Are you a Docker expert?**. Well, we don't, so we will love to hear how the recipes can 
be improved.

**Use it**, **leave your impressions** and **spread the word**. That would be terrific! 

## Disclaimer
_Creating Docker build images for AppImage is a practice long used to build the 
tools  in the AppImageProject and the recipes found on the AppImageCrafters projects were
inspired from the ones used by them._

# Useful links:
- AppImageCrafters org: https://github.com/AppImageCrafters
- Docker repositories: https://cloud.docker.com/u/appimagecrafters/repository/list
- Example Hello World project: https://www.opencode.net/azubieta/cpp-appimage-template