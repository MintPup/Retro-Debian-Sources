# Retro-Debian-Sources

Slowly resuming my work on Retro-Debian project.
This repository includes the [source iso images.](https://github.com/MintPup/Retro-Debian-Sources/releases/tag/v.1.0)
Maybe other source packages and scripts will be uploaded here later.

I will try to follow [DFSG.](https://en.wikipedia.org/wiki/Debian_Free_Software_Guidelines) If you can find here something useful I'm glad but I will develop this project for my own needs only. The targets are very old Pentium-II and Pentium-III computers. I will use older version of live-boot-2.15 with some modifications I made [here a year ago.](https://github.com/MintPup/DebianDog-Wheezy/tree/master/live-boot-2)

You need some terminal command typing skills. The iso boots to command prompt. Login as **user** with password **retro** and you will have to change the password before continue any further. The root password is disabled. Read Debian documentation how to create new root password in case you need it.

Typing **startx** will start Jwm - Rox desktop. If it fails you need to reconfigure xorg for your GPU card with:

```
sudo dpkg-reconfigure xserver-xorg
```

Inside /etc/X11 you can find xorg.conf-* files for some of my machines.
 
Wireless network you will have to setup from command line using wpasupplicant.
 
The user account is set to ask password every time. To stop this behaviour add the user to sudo group.

Jwm custom settings must be made inside /etc/menu-methods/jwm and running update-menus and jwm -restart after that.

In short this system will play and convert with mencoder or ffmpeg (as separate squashfs module) most video formats (including x264) and will run fast on very old hardware. Transmission works with magnet links. The Etch official repository is obsolete but I still find many useful packages there. No security fixes or updates will be available but the kernel for example doesn't have the "Dirty Cow" bug, samba doesn't have the latest security flaws and bash includes the latest security fixes. You can't run modern browser (anything above Firefox 3.0 will not work in Etch without workarounds). Recommended to create custom /etc/X11/xorg.conf file to get the best RAM and CPU usage performance (in general with every linux).

[List of included packages in Retro-Etch.](https://github.com/MintPup/Retro-Debian-Sources/blob/master/live-etch/packages.X.txt)

If you decide to test check out the /scripts content (or [here](https://github.com/MintPup/Retro-Debian-Sources/tree/master/scripts)).

Type full path to the script (/scripts folder is out of $PATH).

**Use at your own risk. No warranty of any kind.**
