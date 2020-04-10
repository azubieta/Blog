---
layout: post
title:  "How to switch the ld.so used by a binary at runtime"
date:   2020-03-10 00:00:00 +0400
categories: AppImage AppImageBuilder LD_SO GLIBC
---


I'm experimenting with a new way of creating AppImages that embeds the GLibC and all the 
application dependencies except for the graphics stack (x11, libgl, libegl, and others). 
At runtime, the application will load some libs from the system and others from the bundle. 
Such libraries are linked to glibc, therefore they may require different versions of glic. 
Luckily, GLibC has good backward compatibility therefore using the latest binary does the 
job.

The issue arises when wen a binary is launched with the system ld-linux.so and the 
bundled GLibC is used. In short, it's a certain crash. Therefore we must ensure that 
the right ld-linux.so is used every time.

The current solution, patch every executable PT_INTERP specifying a unique route 
(i.e.: /tmp/ld-linux.so-appimage-<UUID>). Then at runtime copy to this location 
the interpreter that should be used.

This does the job, but as you can imagine coping a file (~125 Kb) at startup delays 
the process a bit (only noticeable on low-performance devices like RaspberryPi).

I also tried intercepting every execv call and prefixing the right ld-linux.so path. But 
some applications (like Qt ones) rely on the binary path to resolve its resource files. 
Therefore when using the system loader the app don't work.

So I would like to ask if there is a different way of setting the binaries PT_INTERP at 
runtime?
