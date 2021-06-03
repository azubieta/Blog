---
title: "Creating classic AppImages"

date: 2021-06-03T00:00:00-06:00
draft: false
categories:
- appimage
- appimage-builder
- linux
image: banner.png
--- 


`appimage-builder` was created to ease the packaging process of GNU/Linux applications. Originally it introduced a new
kind of bundles that had as main characteristic the inclusion of a `glibc` copy. These bundles could be built on modern
system and still be backward compatible. This flexibility is possible at a price of ~30mb.

In some scenario may not be desired therefore with the v0.9.0 release of `appimage-builder` is possible to create
classic AppImage bundles without glibc but keeping the AppRun and its benefits.

In order to make a non-backward compatible bundle (the classic ones) you just have to exclude the `opt/libc` folder in
your recipe as follows:

```yaml

AppDir:
  files:
    exclude:
      # exclude glibc for a smaller but non backward compatible bundles
      - opt/libc
```

**You may be wondering when should I prefer the classic format over the backward compatible one?**

The backward compatible format eases considerably the packaging process. You can use the very latest binaries (or the
ones shipped in your favorite distro) but you have to pay 30mb more. If you're packaging a small utility with few
dependencies, and those are available (or can be built on an old system) then use a classic bundle. Otherwise stick 
with the `appimage-builder` style.

You can check the [appimage-builder documentation](appimage-builder.readthedocs.io/) for more information about the
tool.

