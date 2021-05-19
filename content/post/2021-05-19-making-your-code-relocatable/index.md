---
title: "Creating an AppImage, make your code relocatable"

date: 2021-05-19T00:00:00-06:00
draft: false
categories:
- appimage
- portable application
- linux
image: banner.png
--- 

The first step on the AppImage packaging process is to make the application code relocatable.
This implies that every application resource path must be resolved at runtime. This includes
icons, fonts, databases, plugins and any other resource that may be required at runtime.

Let's suppose that our application expects to be deployed with the following file structure using
`/usr` as prefix:

```
usr/
├── bin
│   └── appimage-demo-qt5
└── share
    ├── applications
    │   └── appimage-demo-qt5.desktop
    ├── demo-qt5
    │   └── image.png
    └── icons
        └── hicolor
            └── 256x256
                └── apps
                    └── appimage-demo-qt5.png
```



For example, in our code we may be attempting to access `image.png` by its absolute path. 
Using a `RESOURCES_DIR` constant that is set at build time (or worst it may be fully fixed path).
This will cause that our app can only be executed properly if installed in the expected path.

To fix this we can take several approaches, that can be combined depending on the developer
preferences. 

1. Using an environment variable instead of a constant for the `RESOURCES_DIR`. Use your language
capabilities to access the environment variables.
   
2. Resolving the image path relative to the executable path. Use `/proc/self/exec` to resolve the
executable path. Then use a prefix relative path to the resources e.g.: `../share/demo-qt5/image.png`

3. Using a configuration file that gets resolved relative to the executable path. Resolve the 
   executable path as in the example above them read the config file that may live at 
   `<myapp bin dir>/config.yml`
   
This is the only modification you will have to perform in your code in order to pack it as
an AppImage. While `appimage-builder` provides 
[a way of patching paths at runtime](https://appimage-builder.readthedocs.io/en/latest/reference/version_1.html#runtime)
it's not as efficient as having the right path from the beginning.

This was the first post of a series about AppImage creation using `appimage-builder`. Others will
come in the next days. Feel free to leave a comment if you like it and to share it with 
friends and colleagues.

