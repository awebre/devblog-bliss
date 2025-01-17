---
title: 'Setting Up Le Potato'
date: 2022-11-15T22:45:00-6:00
summary: A step-by-step guide to setting up a Le Potato on MacOS.
tags:
  - post
  - le-potato
  - ubuntu
---

### Flashing the SD Card

To start, we'll need to download an appropriate ubuntu image from [Libre Computer's Distro Server](https://distro.libre.computer/ci/ubuntu/22.04/). The correct image will have the name of your Libre device's model number appended to it, in my case this was "ubuntu-22.04.1-preinstalled-server-arm64+aml-s905x-cc.img.xz". You will want to unzip this using a tool like 7zip - which the Libre Computer docs specifically recommend.

This is the command I used to unzip this image:

```
./7zz e ./ubuntu-22.04.1-preinstalled-server-arm64+aml-s905x-cc.img.xz "-o./ubuntu"
```

From here, it's time to burn the resulting image to the SD Card. Libre Computer recommends that you use Disk Utility to burn your image, but using the Disk Utility app on MacOS did not work for me. It might be that I'm on the bleeding edge (MacOS Ventura) or simply because MacOS is weird. Either way, it became clear that I would need to find another way.

Enter "dd" (aka Data Duplicator), an OSX command that can be used to copy data from one file to the next (or in this case, from an image to a disk). Note that you will need to use `diskutil list` to find out the identifier of the disk you wish to write to.

Here is the command that worked for me:

```
sudo dd if=/Users/myuseraccount/Downloads/ubuntu/ubuntu-22.04.1-preinstalled-server-arm64+aml-s905x-cc.img of=/dev/disk6 bs=1m
```

It is important to note that this command does not have any output, but you can check in by hitting "CMD + T", which should give you an output like this:

```
load: 28.98  cmd: dd 1783 uninterruptible 0.00u 0.03s
17+0 records in
17+0 records out
17825792 bytes transferred in 4.640612 secs (3841259 bytes/sec)
```

(This was especially helpful for me, as I wanted to make sure that _something_ was happening)

### Completing the Setup

With that, you should be able to pop your Micro SD card into the board, plug in power and HDMI, and watch as the OS boots up. Once that process is complete, you should be asked to login. The helpful Libre Computer docs mention that the default login/password is:

- Login: ubuntu
- Password: ubuntu

You will be prompted to change your password, however, the first prompt is for you to repeat the current password (i.e. "ubuntu"). Don't make the mistake of typing your new password here, as it will log you out and force you to login again - a mistake that I _definitely did not make_.

That's pretty much it! You should have Ubuntu running on your Le Potato! 🥔🇫🇷
