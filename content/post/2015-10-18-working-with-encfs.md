---
title:  "Encryption of private data with EncFS"
author: "Eric Dupertuis"
date:   "2015-10-18"
categories:
    - Encryption
tags:
    - encryption
    - linux
slug: encryption-of-private-date-with-encfs
---

## What's EncFS ?

Let's hear it from the mouth of [Wikipedia](http://wikipedia.org)

    EncFS is a Free (LGPL) FUSE-based cryptographic filesystem. It transparently encrypts files, using an arbitrary directory as storage for the encrypted files.

So what does EncFS do ? It simply takes a base directory that's encrypted using a key and mounts a virtual filesystem on another folder.

## Why would you use it ?

I personally use EncFS to protect some private data on my external hard drives. In case one of my hard drive is stolen, all sensitives informations are safely encrypted.
One of the advantage is that you can encrypt different folders using different passwords

EncFS isn't made for full-disk encryption, if that's what you're looking for, you should take a look at [this thread](http://askubuntu.com/questions/366749/enable-disk-encryption-after-installation) from the Ubuntu forum.

## Initial setup

You will need to install EncFS on your system :

For Ubuntu and Debian:

    sudo apt-get install encfs

If you use an older version of Ubuntu (< 14.04) you must also install `fuse-utils`

    sudo apt-get install fuse-utils

If you're using Fedora:

    su
    yum install fuse-encfs

For ArchLinux

    pacman -S encfs

If you're using another system, just google it.

## Creating a first encrypted folder

Using EncFS is really ease. Let's assume you have an external drive mounted on `/media/user/HDD1` with a `private` folder in it.
the encfs command takes two arguments, the path of the encrypted folder and the path of the folder on which you'd like to mount the virtual filesystem

    encfs /media/user/HDD1/private ~/private

If any of these folders doesn't exist, EncFS will ask you if it can be created.

    The directory "/media/user/HDD/private/" does not exist. Should it be created? (y,n) y
    The directory "/home/user/private" does not exist. Should it be created? (y,n) y

Just answer yes

After that, it will ask you to choose two configuration mode, expert or pre-configured

    Creating new encrypted volume.
    Please choose from one of the following options:
     enter "x" for expert configuration mode,
     enter "p" for pre-configured paranoia mode,
     anything else, or an empty line will select standard mode.
    ?>

The x mode allow you to select the algorithm (AES or Blowfish), key size, filesystem block size etc... Use this option if you know what you're doing. The p mode (or paranoia mode) gives you good pre-configured options.

EncFS will ask you for your password twice. Make sure to use a strong password. Remember that a long phrase is better than 5 random characters.

And everything is good to go, now you just need to put the files you whish to protect in the `private` folder of your home directory. All the files you put in there will automatically be encrypted in the first folder you specified.

## Unmount your encrypted folder

Once you're done and want to unmount the filesystem, just type :

    fusermount -u /home/user/private

And that's all, if you want to mount the filesystem again, just relaunch the original command

    encfs /media/user/HDD1/private ~/private

The folders already exist so it will only ask for your password. You just need the encrypted folder and if you'd like, you could mount it on whatever folder you want.

## Pros and cons

### Pros

- Really easy to use
- FUSE is integrated in the Linux Kernel
- Works on most filesystems (even NTFS)
- You can make this works on Windows using [encfs4win](http://members.ferrara.linux.it/freddy77/encfs.html)

### Cons

- You can still see the rights, size and number of files in the encrypted folder (names are encrypted though)
- The virtual filesystem has the same limitations as the host filesystem.