---
layout: post
title:  "AppImage dependencies management"
date:   2019-05-03 13:01:32 +0200
categories: 
 - AppImage 
tags:
 - dependencies
 - build
comments: true
---


Currently the process of producing AppImages is hindered by the burden of making the whole dependency tree relocatable and compatible at binary level with older software. A crowd-sourced repository of binaries could be used to share and reduce this work. Several binaries repositories technologies were analyzed finding Conan as the best match for the AppImage project needs. It address the same issues with binaries, opens a wide range of options for future integration between both projects and also improvements for the AppImage project.


## Introducction
The AppImage format allows to create a single package compatible with a wide range of GNU/Linux distributions. To achieve this the developer must ensure that all the binaries inside the package are relocatable and the binary compatibility of the package external dependencies.

In GNU/Linux is quite common to find software that relies on fixed paths to resolve its resources. This must be fixed (usually by means of a patch) before using it as part of an AppImage package. Creating and maintaining such patches becomes an extra job for the application developer.

Binaries compatibility is a more complicated issue. It's recommended that AppImage packages should have as few dependencies as possible. Only being considered as accepted dependencies: "glib", "libstdc", "libstdc++" and others (listed in the black-list). But even those highly common and well maintained libraries are infamous by continuous ABI breaks (specially "glib"). Usually there are workarounds for such issues but they need to be carefully applied to the whole application dependencies tree. Which is also more work for the developer.

In resume the current process of creating an AppImage puts into the developer the burden of creating/maintaining a whole set of patches and obscure workarounds for each one of the application dependencies. Notice that in some cases library developers are kind enough to create good (relocatable and backward compatible) binaries.

_Problem_: How to reduce the development and maintenance work associated to creating relocatable and backward compatible binaries as part of the AppImage production process?

_Hypothesis_: Having a crowd-sourced repository of relocatable and backward compatible binaries will reduce the overall maintenance effort as it will be shared between all the community members.

## Dependencies management systems 
Binary repositories is not a new idea at all so we must consider the technologies that already exists.

1. ### Traditional GNU/Linux repositories
    
    Traditional repositories have been around for a while and have been improved a lot. Now maintaining a deb, rpm or Arch pkgs repository is not "too" complicated. So we could have a repository of sources/binaries which meet the requirements to be embed into AppImages. The sources can be hosted on Github or Gitlab where potential developers can contribute.
    
    Packages will need to be created with a different prefix so they will not crash with the system packages. Developers will have to properly setup this new prefix in their build environment manually. The rest would be _regular app development_.
    
    **Strengths**
    - Established, tested and documented technologies
    - Development workflow will change little from what people are use to

    **Drawbacks**
    - repositories are tight to a given distribution.
    - annoying packaging rules
    - almost every package would have to be redefined/rebuilt on a _old enough_ system 


2. ### Snappy

    Parts is the name Snapcraft gives to dependencies. As the final apps binaries they are built against a common base system (snap-os) which should be present in every target system altogether with the dependencies resolution app: snappy. It would be possible to mix into a single AppImage the required sections of snap-os and all the dependencies. Additionally will be required to remove non-required files that could be part of a given package but are not used by the final application.
    
    Existent snap packages recipes could be used to produce AppImages. But they will need to be rebuilt and stored somewhere. Maybe in the Snappy Store or in a custom store. Developers will need to `snappy install` them and the snapd daemon should take care of exporting all the required environment variables.


**Strengths**
    - Snap packages uses squashfs which is also used in type 2 AppImages which could potentially ease the building process
    - Final package apps "could" be turned into AppImages

**Drawbacks**
    - Vendor lock-in courtesy of Canonical
    - Developers will need to have deal with yet another dependencies management system
    - Almost every package would have to be redefined/rebuilt on a _old enough_ system 

3. ### Flatpak
    Three different concepts are used in Flatpak to deal with dependencies: runtimes, bundled libraries and base apps. Runtimes provides a set of basic dependencies to be used by the applications. Bundled libraries are used to extend the runtimes and the bundling process is quite similar to the one used to make an AppImage (manual bundling). Base apps are like extended and specialized runtimes to be used while packaging by example an Electron application.
    
    As runtimes are meant to be 'portable' and base apps they "could" be used as base for building AppImages. Developers should only install the required runtime to develop their apps. Bundled libraries are not a big improvement compared to the current AppImage production process.
    
    **Strengths**
    - More "open" than snaps?
    - Final package apps "could" be turned into AppImages

    **Drawbacks**
    - Developers will need to have deal with yet another dependencies management system
    - Almost every package would have to be redefined/rebuilt on a _old enough_ system 
    - If a rare (not included in any runtime) dependency is required it doesn't provide any real improvement over manual dependency management.
  
4. ### Conan
    Conan is a portable package manager, intended for C and C++ developers, but it is able to manage builds from source, dependencies, and pre-compiled binaries for any language. It is focused on resolving development dependencies (which also implies run-time dependencies) so it allows to reconfigure/rebuild a given package and its dependencies with a minimal effort. In combination with [Artifactory](https://jfrog.com/artifactory/) a self-hosted dependencies repository could be easily created. It already has a solid integration with docker making tasks like cross-compilation really simple.
    
    Dependencies must be packed before using them. Conan uses python in its configuration files. Developers must create a `conan.py` file where the dependencies are specified. Then use `conan install` to download them. This will create a development environment equal to the packaging environment which save a lot of work while creating the packages.

    **Strengths**
    - Development oriented
    - Development environment equal to the packaging environment
    - Simple rebuild and reconfiguration of dependencies
    - AppImages could be created from the development environment without needing an _old enough_ system

    **Drawbacks**
    - Developers will need to have deal with yet another dependencies management system
    - Some libraries will required proper packaging

## Discussion
All of the review solutions allows to create a crowd-sourced repository of relocatable and backward compatible binaries. Which means that the dependencies packaging efforts will be shared among all the AppImage creators. But we can discriminate them attending to their strengths and drawbacks being an additional goal to also reduce development and maintenance efforts of the AppImage development team.

GNU/Linux distributions (at least Debian, OpenSuse, Centos, Ubuntu, Fedora, Arch) compatibility is required. Traditional distributions repositories are compatible with only one of the above mentioned. Having one repository for each distributions implies a lot of work. Therefore it's excluded. Flatpack and Snappy provides implementations for such distributions. Conan runs on python so it's also compatible.

Completely open-source is mandatory. Most of the Snappy technologies are open but the store in closed source. A new store will have to be developed. Flatpack and Conan + Artifactory are totally open source. Therefore it's excluded.

Comparing Flatpack and Conan it's found that both are a 'new dependencies management system' to the developer deal with. But this is not a big issue if we consider that almost any modern software development technology has one of those. Both solutions already has packages that could be turned into AppImages or could be used to build AppImages. And also there are many libraries that would require to be repacked. The main difference resides in the focus of each solution. Flatpack is focused only on distributing software. Conan on the other hand is focused on distributing development dependencies (which also include the run-time dependencies) and it also provides a set of tools to make such dependencies relocatable and backward compatible.

Additionally Conan provides simple rebuild and reconfiguration of dependencies to make any small adjustment required by the developer. By example choosing between using a shared library or a static one. And the most important feature of Conan would be making the development environment equal to the packaging environment. This allows to identify compatibility issues early in the development/packaging process and to create backward compatible binaries on modern systems.

Final applications could be packaged with Conan but the result is not quite user-friendly. Therefore AppImage could also be the right complement to Conan. `conan.py` files could be used to produce AppImages which contributes to the goal of having reproducible AppImage builds.

In conclusion Conan and AppImage seems to be done one for each other. Both solutions are a complementary and share similar goals regarding to binaries production. The AppImage project will gain dependency management system and Conan will gain an application packaging solution.


## Sources
- https://docs.appimage.org/
- http://docs.flatpak.org/en/latest/
- https://docs.snapcraft.io/
- https://docs.conan.io/en/latest/
