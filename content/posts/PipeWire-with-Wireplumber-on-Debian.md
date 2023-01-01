---
title: PipeWire with Wireplumber on Debian
date: 2023-01-01T17:30:15+05:00
---

## Add the repo

To be able to download Wireplumber, let's first add the repository.

Find your architecture [here](https://packages.debian.org/sid/wireplumber) and follow the instructions. Here's the exmaple for AMD64 machines, add this to your `/etc/apt/sources.list`:

```shell
deb http://ftp.de.debian.org/debian sid main
```

## Installation

Now, update the packages:

```shell
sudo apt update
```

Install PipeWire, Wireplumber and dependencies:

```shell
sudo apt install gstreamer1.0-pipewire \ 
                 libpipewire-0.3-{0,dev,modules} \
                 libspa-0.2-{bluetooth,dev,jack,modules} \
                 pipewire{,-{audio-client-libraries,pulse,bin,jack,alsa,v4l2,libcamera,locales,tests}} \
                 wireplumber{,-doc} \
                 gir1.2-wp-0.4 libwireplumber-0.4-{0,dev} \
                 pipewire-media-session
```

## Enable PipeWire

Disable PulseAudio and related services:

```shell
systemctl --user --now disable pulseaudio.{socket,service}
systemctl --user mask pulseaudio
```

Copy the configuration files:

```shell
sudo cp -vRa /usr/share/pipewire /etc/
```

Enable PipeWire services:

```shell
systemctl --user --now enable pipewire{,-pulse}.{socket,service} filter-chain.service
```

Start `pipewire-media-session`:

```shell
systemctl --user enable --now pipewire-media-session.service
```

## Enable Wireplumber

Finally, start Wireplumber:

```shell
systemctl --user --now enable wireplumber.service
```

Reboot and check whether it's running:

```shell
pactl info | grep '^Server Name'
```

Expected output:

```shell
Server Name: PulseAudio (on PipeWire 0.3.63)
```

## Autoswitch on connect

If you use bluetooth headphones, it's a neat feature to switch the audio to it automatically. You can enable it by adding the next line to `content.exec` in `/etc/pipewire/pipewire.conf`:

```shell
context.exec = [
    ...
    
    { path = "pactl" args = "load-module module-switch-on-connect" }
]
```

# Reference:

1. [PipeWire on Debian Wiki](https://wiki.debian.org/PipeWire)
2. [PipeWire on Arch Wiki](https://wiki.archlinux.org/title/PipeWire)
3. [pipewire-debian/pipewire-debian on GitHub](https://github.com/pipewire-debian/pipewire-debian)
4. [PipeWire Debian Package](https://packages.debian.org/sid/amd64/wireplumber/download)
