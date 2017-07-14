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

If you decide to test check out the /scripts content. Use at your own risk. No warranty of any kind.
