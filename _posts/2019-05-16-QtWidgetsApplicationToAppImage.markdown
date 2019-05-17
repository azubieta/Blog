---
layout: post
title:  "Example QtWidgetsApplication packed as AppImage using Conan.io"
date:   2019-05-16 13:01:32 +0200
categories: AppImage Conan Qt Example
comments: true
---

# Introduction
In a previous [post]({% post_url 2019-05-03-appimage-dependencies-management %})
 was mentioned how well Conan.io and AppImage could work in order to ease the
 production and distribution of your software. In this post, we show a minimal 
 example of how to pack a Qt Widgets Application as an AppImage using
 conan.io as a dependencies management system.
 
 
_What's Conan?_ it's a decentralized package manager. That allows us to have
 binaries ready to be used to create AppImages.
 
_Why do you need such special binaries?_ In order to create an application
that could be run on almost any GNU/Linux distribution you will need
to build your application and it's dependencies on a very old base system
(by example Centos 6). So you will have to back-port every dependency to such system. Using our conan.io repository you can access to a wide range 
of package recipes that are ready to be built/used on almost any distribution.
Such packages are available on [bintray](https://bintray.com/beta/#/appimage-conan-community/public-conan)
and the recipes are on [github](https://github.com/appimage-conan-community)
You will find them as part of the _appimage-conan-community_ which is a group 
for those using this technology stack.

# The Code

Let's take a minimal Qt Widgets applications which uses `qmake` as build system. 
Then we will add a [desktop file](https://github.com/azubieta/QtWidgetsApplication-example/blob/master/resources/QtWidgetsApplication.desktop) 
and an [icon](https://github.com/azubieta/QtWidgetsApplication-example/blob/master/resources/QtWidgetsApplication.png). 
Finally, we will declare our project dependencies using a [conanfile.txt](https://github.com/azubieta/QtWidgetsApplication-example/blob/master/conanfile.txt).

```
[requires]
libpng/1.6.36@bincrafters/stable
qt/5.12.3@appimage-conan-community/stable

[build_requires]
# linuxdeploy is required to build the AppImage therefore is listed only as build require
linuxdeploy-plugin-appimage/continuous@appimage-conan-community/stable
linuxdeploy-plugin-qt/continuous@appimage-conan-community/stable
appimagetool_installer/11@appimage-conan-community/stable

[generators]
# allow to run qmake from the build dir
qmake
# create conan virtual environment (required to run linuxdeploy)
virtualrunenv

[options]
# Require shared libs
zlib:shared=True
qt:shared=True
```

Now we can proceed building our AppImage:

```
mkdir build && cd build;

# Install conan dependencies requiring them to be built using c++ 11 and linked to libstdc++11  
conan install .. -s compiler.cppstd=11 -s compiler.libcxx=libstdc++11 --build missing

# Enter conan virtual environment (just like python virtual environments)
. activate_run.sh

# build your app as your are used to
qmake CONFIG+=release PREFIX=/usr ../QtWidgetsApplication.pro

# Let's crete the AppImage
# install you app to an AppDir
INSTALL_ROOT=$BUILD_DIR/AppDir make install

# call linuxdeploy with the Qt plugin
linuxdeploy --appdir AppDir/ --plugin qt --output appimage

# exit conan virtual env
. deactivate_run.sh

```

You can find the whole project code on [Github](https://github.com/appimage-conan-community/QtWidgetsApplication-example).

Please fell free to leave your comments below and enjoy hacking!
