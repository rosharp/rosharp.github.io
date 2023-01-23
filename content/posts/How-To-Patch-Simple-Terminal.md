---
title: How to install and patch st 
date: 2022-11-09T19:30:30+05:00
tags: suckless
---

[Patching documentation](https://suckless.org/hacking/)

Click the download link on the [st](https://st.suckless.org/) website at the very bottom and extract (replace the version number):

```shell
cd $HOME/Downloads/ 
tar xvf st-X.X.tar.gz
```

Now, you can build and have a working binary of st (although, very minimal):

```shell
cd st-X.X/
make clean install
```

Check out the available patches [here](https://st.suckless.org/patches/). Take the one you need and download with:
```shell
curl -O <download link>
```

Apply the patch:
```shell
patch -p1 < path/to/patch.diff
```

And rebuild the binary:

```shell
make clean install
```

Voilà! The patch should be applied.

