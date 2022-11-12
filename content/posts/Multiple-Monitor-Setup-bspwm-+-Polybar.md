---
title: Multimonitor setup in bspwm
date: 2022-11-10T19:49:41+05:00
---

# Multiple monitor setup - bspwm + Polybar

![Image](/Multiple-Monitor-Setup-bspwm-+-Polybar/IMG_0120.PNG)

Required packages:
* bspwm
* polybar
* xrandr
* autorandr

## Detect connected displays
First of all, we want to check the discriptors of connected displays with this command:

```shell
> xrandr --listmonitors

Monitors: 2
 0: +*eDP1 1920/280x1080/160+0+0  eDP1
 1: +HDMI2 1680/470x1050/300+1920+0  HDMI2
```

Let's remember these; I'll describe my monitors as eDP1 (my laptop's display) and HDMI2 (external monitor).

## Startup script
Next, we're going to create a script which detects an external monitor when connected, moves bspwm desktops to it and removes those when a monitor is disconnected.

You can store it where you like, just make sure to use the correct path to it.

```shell
#! /bin/sh

internal_monitor=eDP1
external_monitor=HDMI2

if [ external_monitor = *connected* ]; then
    xrandr --output internal_monitor --primary --mode 1920x1080 --rotate normal --output external_monitor --mode 1680x1050 --rotate normal --right-of internal_monitor
fi

monitor_add() {
	desktops=4 # How many desktops to move to the second monitor

	for desktop in $(bspc query -D -m $internal_monitor | sed "$desktops"q)
  do
		bspc desktop $desktop --to-monitor $external_monitor
  done

  # Remove "Desktop" created by bspwm
  bspc desktop Desktop --remove
}

monitor_remove() {
	bspc monitor $internal_monitor -a Desktop # Temp desktop because one desktop required per monitor

	# Move everything to external monitor to reorder desktops
	for desktop in $(bspc query -D -m $internal_monitor)
	do
		bspc desktop $desktop --to-monitor $external_monitor
	done

	# Now move everything back to internal monitor
	bspc monitor $external_monitor -a Desktop # Temp desktop

	for desktop in $(bspc query -D -m $external_monitor)
	do
		bspc desktop $desktop --to-monitor $internal_monitor
	done

	bspc desktop Desktop --remove # Remove temp desktops
}

if [ $(xrandr -q | grep "$external_monitor connected") ]; then
    monitor_add
else
    monitor_remove
fi
```

Make it executable:

```shell
chmod +x $HOME/path/to/script/startup
```

## bspwm
In `bspwmrc` add the needed desktop numbers by monitor descriptors like this and add the polybar launch script if you haven't already:

$HOME/.config/bspwm/bspwmrc
```shell
#! /bin/sh

bspc monitor eDP1 -d 1 2 3 4 
bspc monitor HDMI2 -d 5 6 7 8

if [ -x $HOME/path/to/polybar/launch.sh ]; then
  $HOME/path/to/polybar/launch.sh &
fi
```

## Polybar

Let's create a separate Polybar instance for the external monitor.

Pay attention to adding `pin-workspaces = true` to both main and secondary bars. 

$HOME/.config/polybar/config.ini
```
[bar/main]
width = 100%
height = 30
offset-y = 0
bottom = false 
fixed-center = true

monitor = eDP1

pin-workspaces = true

...

[bar/external]
width = 100%
height = 30
offset-y = 0
bottom = false 
fixed-center = true

monitor = HDMI2

pin-workspaces = true

...

```
Launch the secondary (external) Polybar instance conditionally when the external monitor is connected. My launch script looks like this:

$HOME/.config/polybar/launch.sh
```shell
#!/usr/bin/env bash

killall polybar
while pgrep -u $UID -x polybar >/dev/null; do sleep 1; done

CONFIG_DIR=$(dirname $0)/themes/$THEME/config.ini
polybar main -c $CONFIG_DIR &

# launch secondary Polybar if an external monitor is connected
if [[ $(xrandr -q | grep 'HDMI2 connected') ]]; then
  polybar external -c $CONFIG_DIR &
fi
```

# autorandr

This command will magically make your external monitor work: it sets the resolution and puts it to the right of your primary display.

```shell
xrandr --output internal_monitor --primary --mode 1920x1080 --rotate normal --output external_monitor --mode 1680x1050 --rotate normal --right-of internal_monitor
```

[Autorandr](https://github.com/wertarbyte/autorandr) automatically selects a display configuration based on connected devices, so you don't need to use the previous command all the time when connecting a display. Let's create a separate profile with an external display:

```shell
autorandr -s docked
```
Then, you can disconnect your external display and create a profile just for a primary display (I personally don't use it):

```shell
xrandr --output internal_monitor --primary --mode 1920x1080 --rotate normal
autorandr -s undocked
```
Finally, create a file called `postswitch` in the `autorandr` directory. Put our newly made startup in it in order to run it every time you connect an external display:

$HOME/.config/autorandr/postswitch
```shell
#! /bin/sh

$HOME/path/to/script/startup
```

And make it executable:

`chmod +x $HOME/.config/autorandr/postswitch`

That's it! Now, you will be able to switch between multiple separate desktops on each display. Everything should work fine after reconnecting an external display - you will still get several new desktops on it.

