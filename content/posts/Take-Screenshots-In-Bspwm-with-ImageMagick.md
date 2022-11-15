---
title: Take Screenshots in Bspwm with ImageMagick 
date: 2022-11-15T14:06:53+05:00
---

## Prerequisites

Packages:

* bspwm
* sxhkd
* imagemagick (in some repos 'ImageMagick')
* notify-send

## bspwmrc

Initialize `sxhkd` in your `bspwmrc` (by default, located in `$HOME/.config/bspwm/bspwmrc`);

```shell
pgrep -x sxhkd > /dev/null || sxhkd &
```
## Custom directory

You can create a custom directory to keep your screenshots in there, for example:

```shell
mkdir $HOME/Pictures/Screenshots/
```

## sxhkdrc

Let's create keybindingds in `sxhkdrc` (by default, located in `$HOME/.config/sxhkd/sxhkdrc`).

1. Take a screenshot of a certain area and save it:

```shell
super + p
  import $HOME/Pictures/Screenshots/Screenshot\ $(date).png
```
2. Take a screenshot of the entire screen (including external displays):

```shell
super + shift + p
  import -window root $HOME/Pictures/Screenshots/Screenshot\ $(date).png
```

> If you're using `xbanish` to hide the cursor when typing, it will cause the cursor to disappear after pressing a keybinding to take a screenshot of a certain area.

## Adding notification

You may add a pop-up notification after taking a screenshot, for example, modifying what we've previously written:

```shell
super + p
  import $HOME/Pictures/Screenshots/Screenshot\ $(date).png \
  && notify-send "Screenshot was taken"
```

