---
title: "Exporting environment variables on appimage-builder"
date: 2021-02-15T19:59:20-06:00
draft: false
categories:
 - appimage
 - appimage-builder
image: appimage-builder-export-env.png
---

Being able to modify the runtime environment variables from
the scripts inside an appimage-builder recipe has been an
old feature request in our backlog. This would us to move
the whole build process of the binaries inside the recipe
and extract information from it to use it later.

This feature is available in development version (master branch)
and will be released in `appimage-builder v0.8.4`.

## Usage

- Export the variables to a special file located at 
  `$BUILD_ENV`.
- Use the variable in your recipe using double curly 
  brackets: `{{VAR_NAME}}`

```
script:
 - echo "MY_VAR=VALUE" >> $BUILDER_ENV
 
AppDIR:
  name: my-app {{BUILDER_ENV}}
```

## Limitations

Exported variables will be available once the script section
is completed. Therefore, you will need to use the `export`
command in order to make a variable reachable inside the 
current script as follows:

```
script:
 - echo "MY_VAR=VALUE" >> $BUILDER_ENV
 - export MY_VAR=VALUE
```

> Old style env variables declared using `!ENV` and `${VAR_NAME}`
are evaluated before running the script section and will not 
work in this scenario. Use double curly brackets instead.

## How it works ?

The implementation was inspired by the Github Actions way
of dealing with environment variables and works as follows.

For each script section `appimage-builder` creates a temporary
file and exports its path using the `BUILDER_ENV` variable.
This file can be modified from the script using an `echo` 
command or something alike. Once the script execution is 
completed, the entries in the file are appended to the
process environment.

The double curly bracket notation is used to resolve environment 
variables. Unlike the old notation, these variables are evaluated
only when they are required and can be undefined at the start
of `appimage-builder`

## Conclusions

Now you can set environment variables from the script sections and
use them later on `appimage-builder`. This feature can be used
to get some metadata from your application and use it later.

I wonder which other uses it may have?
Any ideas, leave a comment.
