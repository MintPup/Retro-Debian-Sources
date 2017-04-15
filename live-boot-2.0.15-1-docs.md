**live boot v2.0.15-1 docs**

live-boot - System Boot Scripts

DESCRIPTION

   live-boot  contains  the  scripts  that  configure a Debian Live system
   during the boot process (early userspace).

   live-boot is a  hook  for  the  initramfs-tools,  used  to  generate  a
   initramfs  capable  to  boot  live  systems,  such  as those created by
   live-helper(7). This includes the Debian Live isos,  netboot  tarballs,
   and usb stick images.

   At  boot time it will look for a (read-only) media containing a "/live"
   directory where a root filesystems (often a compressed filesystem image
   like  squashfs)  is  stored.  If  found,  it  will  create  a  writable
   environment, using aufs, for Debian like systems to boot from.

CONFIGURATION

   live-boot can be configured through a boot parameter or a configuration
   file.

Kernel Parameters live-boot is only activated if 'boot=live' was used as a kernel parameter.

   In addition, there are some  more  boot  parameters  to  influence  the
   behaviour, see below.

Configuration Files live-boot can be configured (but not activated) through configuration files. Those files can be placed either in the root filesystem itself (/etc/live/boot.conf, /etc/live/boot.d/), or on the live media (live/boot.conf, live/boot.d/).

OPTIONS


   live-boot currently features the following parameters.
   

   access=ACCESS
   
       Set the accessibility level  for  physically  or  visually  impared
       users.  ACCESS  must  be  one  of  v1, v2, v3, m1, or m2. v1=lesser
       visual impairment,  v2=moderate  visual  impairment,  v3=blindness,
       m1=minor motor difficulties, m2=moderate motor difficulties.

   console=TTY,SPEED
   
       Set  the  default  console to be used with the "live-getty" option.
       Example: "console=ttyS0,115200"

   debug
   
       Makes initramfs boot process more verbose.
       Use: debug=1
       Without setting debug to a value the messages may not be shown.

   fetch=URL
   

   httpfs=URL
   
       Another form of netboot by downloading  a  squashfs  image  from  a
       given url.  The fetch method copies the image to ram and the httpfs
       method uses fuse and httpfs2 to mount the image in  place.  Copying
       to  ram  requires  more memory and might take a long time for large
       images. However, it is more likely to  work  correctly  because  it
       does  not  require  networking  afterwards  and the system operates
       faster once booted because it  does  not  require  to  contact  the
       server anymore.
       Due  to current limitations in busyboxs wget and DNS resolution, an
       URL can not contain a hostname but an IP only.
       Not working: http://example.com/path/to/your_filesystem.squashfs
       Working: http://1.2.3.4/path/to/your_filesystem.squashfs
       Also note that therefore it's currently not possible  to  fetch  an
       image from a namebased virtualhost of an httpd if it is sharing the
       ip with the main httpd instance.
       You may also use the live iso image in place of the squashfs image.

   iscsi=server-ip[,server-port];target-name
   
       Boot from an iSCSI target that has an iso or disk live image as one
       of its LUNs. The specified target is searched for a LUN which looks
       like a live media. If you use the iscsitarget software iSCSI target
       solution which is packaged in Debian your ietd.conf might look like
       this:
       # The target-name you specify in the iscsi= parameter
       Target <target-name>
         Lun 0 Path=<path-to-your-live-image.iso>,Type=fileio,IOMode=ro
         # If you want to boot multiple machines you might want to look at
       tuning some parameters like
         # Wthreads or MaxConnections

   fromiso=/PATH/TO/IMAGE
   
       Allows  to  use  a  filesystem  from  within  an  iso  image that's
       available on live-media.

   ignore_uuid
   
       Do not check that any UUID embedded in the  initramfs  matches  the
       discovered  medium.  live-boot  may  be  told to generate a UUID by
       setting LIVE_GENERATE_UUID=1 when building the initramfs.

   integrity-check
   
       If specified, an MD5 sum is calculated on  the  live  media  during
       boot  and  compared  to  the value found in md5sum.txt found in the
       root directory of the live media.

   ip=[CLIENT_IP]:[SERVER_IP]:[GATEWAY_IP]:[NETMASK]:[HOSTNAME]:[DEVICE]:[AUTOCONF]
   
   [,[CLIENT_IP]:[SERVER_IP]:[GATEWAY_IP]:[NETMASK]:[HOSTNAME]:[DEVICE]:[AUTOCONF]]
   
       Let you specify the name(s) and the  options  of  the  interface(s)
       that  should be configured at boot time. Do not specify this if you
       want to use dhcp (default). It will be changed in a future  release
       to   mimick   official   kernel   boot  param  specification  (e.g.
       ip=10.0.0.1::10.0.0.254:255.255.255.0::eth0,:::::eth1:dhcp).

   ip=[frommedia]
   
       If this variable is set, dhcp and  static  configuration  are  just
       skipped  and  the system will use the (must be) media-preconfigured
       /etc/network/interfaces instead.

   {live-media|bootfrom}=DEVICE
   
       If you specify one of this two  equivalent  forms,  live-boot  will
       first  try  to find this device for the "/live" directory where the
       read-only root  filesystem  should  reside.  If  it  did  not  find
       something usable, the normal scan for block devices is performed.
       Instead of specifing an actual device name, the keyword 'removable'
       can be used to  limit  the  search  of  acceptable  live  media  to
       removable  type only. Note that if you want to further restrict the
       media to usb mass storage only, you  can  use  the  'removable-usb'
       keyword.

   {live-media-encryption|encryption}=TYPE
   
       live-boot   will  mount  the  encrypted  rootfs  TYPE,  asking  the
       passphrase,  useful  to  build  paranoid  live  systems  :-).  TYPE
       supported so far are "aes" for loop-aes encryption type.

   live-media-offset=BYTES
   
       This  way you could tell live-boot that your image starts at offset
       BYTES in the above specified or autodiscovered device,  this  could
       be  useful  to hide the Debian Live iso or image inside another iso
       or image, to create "clean" images.

   live-media-path=PATH
   
       Sets the path to the live filesystem on the medium. By default,  it
       is  set  to  '/live' and you should not change that unless you have
       customized your media accordingly.

   live-media-timeout=SECONDS
   
       Set  the  timeout  in  seconds  for   the   device   specified   by
       "live-media=" to become ready before giving up.

   module=NAME
   
       Instead of using the default optional file "filesystem.module" (see
       below) another  file  could  be  specified  without  the  extension
       ".module";  it  should  be  placed on "/live" directory of the live
       medium.

   netboot[=nfs|cifs]
   
       This tells live-boot to perform  a  network  mount.  The  parameter
       "nfsroot="  (with optional "nfsopts="), should specify where is the
       location of the root filesystem.   With  no  args,  will  try  cifs
       first, and if it fails nfs.

   nfsopts=
   
       This lets you specify custom nfs options.

   nofastboot
   
       This  parameter disables the default disabling of filesystem checks
       in /etc/fstab. If you have static filesystems on your harddisk  and
       you  want  them  to  be  checked  at boot time, use this parameter,
       otherwise they are skipped.

   nopersistent
   
       disables the "persistent" feature, useful if the  bootloader  (like
       syslinux) has been installed with persistent enabled.

   noprompt
   
       Do  not  prompt  to  eject  the CD or remove the USB flash drive on
       reboot.

   swapon
   
       This parameter enables usage of local swap partitions.

   persistent[={nofiles|cryptsetup}]
   
       live-boot will look for persistent and snapshot partitions or files
       labeled   "live-rw",   "home-rw",   and  files  called  "live-sn*",
       "home-sn*" and will try to, in order:  mount  as  /cow  the  first,
       mount the second in /home, and just copy the contents of the latter
       in appropriate locations (snapshots). Snapshots will be tried to be
       updated  on  reboot/shutdown.  Look  at  live-snapshot(1)  for more
       informations.
       If "nofiles" is specified, only filesystems  with  matching  labels
       will  be  searched;  no  filesystems  will be traversed looking for
       archives or image files. This results in shorter boot times.
       If "cryptsetup" is specified, filesystems stored on  Luks-encrypted
       devices  will  be considered as well as others when searching for a
       persistence filesystem; the user will be prompted  for  any  needed
       decryption passphrase.

   persistent-path=PATH
   
       live-boot  will look for persistency files in the root directory of
       a partition, with this parameter, the path  can  be  configured  so
       that  you  can  have  multiple directories on the same partition to
       store persistency files.

   persistent-subtext=SUFFIX
   
       Add a suffix when searching for the image  filenames  or  partition
       labels  to  use  for  the  above  mentioned persistent feature, the
       SUFFIX will be added after a dash (e.g.: "live-sn" would  transform
       to  "live-sn-SUFFIX").  This  is  handy  to test multiple live-boot
       based live-systems with different persistent storage choices.

   {preseed/file|file}=FILE
   
       A path to a file present on the rootfs could  be  used  to  preseed
       debconf database.

   package/question=VALUE
   
       All  debian installed packages could be preseeded from command-line
       that way,  beware  of  blanks  spaces,  they  will  interfere  with
       parsing, use a preseed file in this case.

   quickreboot
   
       This  option causes live-boot to reboot without attempting to eject
       the media and without asking the user to remove the boot media.

   showmounts
   
       This  parameter  will  make  live-boot  to  show  on  "/"  the   ro
       filesystems  (mostly compressed) on "/live". This is not enabled by
       default because could lead to problems by applications like  "mono"
       which store binary paths on installation.

   silent
   
       If  you  boot with the normal quiet parameter, live-boot hides most
       messages of its own. When adding silent, it hides all.

   todisk=DEVICE
   
       Adding this parameter,  live-boot  will  try  to  copy  the  entire
       read-only  media  to  the specified device before mounting the root
       filesystem. It probably needs a lot of free space. Subsequent boots
       should then skip this step and just specify the "live-media=DEVICE"
       boot parameter with the same DEVICE used this time.

   toram
   
       Adding this  parameter,  live-boot  will  try  to  copy  the  whole
       read-only  media  to  the  computer's  RAM before mounting the root
       filesystem. This could need a lot of ram, according  to  the  space
       used by the read-only media.

   union=aufs|unionfs
   
       By  default,  live-boot  uses  aufs.  With  this parameter, you can
       switch to unionfs.

FILES (old)

   /etc/live.conf
   
       Some variables can be configured via this config file  (inside  the
       live system).

   live/filesystem.module
   
       This  optional  file  (inside  the  live  media) contains a list of
       white-space or carriage-return-separated file  names  corresponding
       to  disk images in the "/live" directory. If this file exists, only
       images listed here will be merged into the root aufs, and they will
       be  loaded  in  the order listed here. The first entry in this file
       will be the "lowest" point in the aufs, and the last file  in  this
       list  will  be  on  the  "top"  of  the  aufs, directly below /cow.
       Without this file, any images in the "/live" directory  are  loaded
       in alphanumeric order.

   /etc/live-persistence.binds
   
       This  optional file (which resides in the rootfs system, not in the
       live media) is used as a list of  directories  which  not  need  be
       persistent: ie. their content does not need to survive reboots when
       using the persistence features.
       This saves expensive writes and speeds up  operations  on  volatile
       data  such  as  web  caches and temporary files (like e.g. /tmp and
       .mozilla) which are regenerated each time. This is achieved by bind
       mounting each listed directory with a tmpfs on the original path.

FILES

   /etc/live/boot.conf

   /etc/live/boot.d/

   live/boot.conf

   live/boot.d/

Written by Daniel Baumann daniel@debian.org.

***

live-snapshot - simple script to ease persistence usage

SYNOPSIS

   live-snapshot     [-c|--cow     DIRECTORY]     [-d|--device     DEVICE]     [-e|--exclude-list FILE] [-o|--output FILE] [-t|--type TYPE]
   
   live-snapshot [-r|--resync-string STRING]
   
   live-snapshot [-h|--help]
   
   live-snapshot [-u|--usage]
   
   live-snapshot [-v|--version]

DESCRIPTION

   live-snapshot is a script which can be used to build the right types of
   persistent  image  files  supported by live-boot(7). It is also used on
   exit by the live-boot init script to resync  the  boot-found  snapshots
   devices.

OPTIONS

   -c, --cow DIRECTORY
   
       specifies  the  input directory to be cloned in the image file. Its
       default value "/live/cow" should be right for most uses. However it
       could  be  handy  to  specify "/home" and type ext2 for the type to
       prepare an image file suited to be directly mounted by live-boot as
       home.

   -d, --device DEVICE
   
       sets  the  device  where  the  media which the snapshot/persistence
       file/partition will be put. If it is not specified, a tmpfs will be
       used  and  linked  to  the  user's  desktop  to move it where it is
       needed. If the device  has  no  filesystem,  an  ext2  fs  will  be
       automatically   created   and  labelled  according  to  the  values
       specified after the "--output" value or with a sane default.

   -e, --exclude-list FILE
   
       a file containing a list of  filenames/paths  that  should  not  be
       saved.  This  exclude list will be remebered on the target snapshot
       media for reuse.

   -o, --output FILE
   
       the filename/label used for  the  output  file/partition.  If  left
       blank, live-snapshot will search for a proper file on the device or
       use the whole partition.

   -r, --resync-string STRING
   
       internally used on resyncs.

   -f, --refresh
   
       try to do the same operation that should be done at reboot or halt,
       resyncing  boot-time auto discovered snapshots. Useful to prevent a
       crash or surge power-off.

   -t, --type TYPE
   
       Type could be one of "cpio", "squashfs", "ext2", "ext3", "ext4", or
       "jffs2".

   -h, --help
   
       display help and exit.

   -u, --usage
   
       show usage and exit.

   -v, --version
   
       output version information and exit.

FILES

   /etc/live.conf
   
       Some  variables  can be configured via this config file (inside the
       live system).

   live/filesystem.module
   
       This optional file (inside the  live  media)  contains  a  list  of
       white-space  or  carriage-return-separated file names corresponding
       to disk images in the "/live" directory. If this file exists,  only
       images listed here will be merged into the root aufs, and they will
       be loaded in the order listed here. The first entry  in  this  file
       will  be  the "lowest" point in the aufs, and the last file in this
       list will be on  the  "top"  of  the  aufs,  directly  below  /cow.
       Without  this  file, any images in the "/live" directory are loaded
       in alphanumeric order.

   /etc/live-persistence.binds
   
       This optional file (which resides in the rootfs system, not in  the
       live  media)  is  used  as  a list of directories which not need be
       persistent: ie. their content does not need to survive reboots when
       using the persistence features.
       This  saves  expensive  writes and speeds up operations on volatile
       data such as web caches and temporary files  (like  e.g.  /tmp  and
       .mozilla) which are regenerated each time. This is achieved by bind
       mounting each listed directory with a tmpfs on the original path.

   /etc/live-snapshot.list
   
       This  optional  file,  if  present   changes   the   behaviour   of
       live-snapshot: only files and directories listed there are included
       (integrally) in the snapshot. Beware, it is an experimental feature
       that only works for cpio targets now.

Written by Daniel Baumann daniel@debian.org.

***

live-build - the Debian Live tool suite

SYNOPSIS

   lb  COMMAND  [-h|--help]  [-u|--usage]  [-v|--version]  [--breakpoints]
   [--conffile] [--debug] [--force] [--quiet] [--verbose]

DESCRIPTION

   live-build is a set of scripts to build Debian Live system images.  The
   idea  behind  live-build  is  a  tool  suite  that uses a configuration
   directory to completely automate and customize all aspects of  building
   a Live image.

   The COMMAND is a name of a live-build command (see below).

   More  documentation  about  how  to  use live-build is available in the
   individual  manpages  for  each   helper   and   in   the   manual   at
   <http://live.debian.net/manual/>.

OPTIONS

Shared live-build options The following command line options are supported by all live-build programs.

   -h, --help
   
       display help and exit.

   -u, --usage
   
       show usage and exit.

   -v, --version
   
       output version information and exit.

Common live-build options The following command line options are supported by most live-build programs. See the man page of each program for a complete explanation of what each option does.

   --breakpoints
   
       run with breakpoints.

   --conffile
   
       use custom configuration file.

   --debug
   
       show debug information.

   --force
   
       force helper execution, even if stage file exists.

   --quiet
   
       be quiet.

   --verbose
   
       be verbose.

LIVE-BUILD COMMANDS

   We divide live-build into high level  ("porcelain")  commands  and  low
   level ("plumbing") commands.

   Here  is  the  complete  list of all available live-build commands. See
   their man pages for additional documentation.

HIGH-LEVEL COMMANDS (PORCELAIN)

   We separate the porcelain commands into  the  main  commands  and  some
   ancillary user utilities.

Main porcelain commands lb_config(1) create configuration for live-build

   lb_bootstrap(1)
       create the first stage by bootstrapping a basic debian system

   lb_chroot(1)
       create the second stage by customizing the chroot

   lb_binary(1)
       create the third stage by generating a binary image

   lb_source(1)
       create the optional fourth stage by generating a source image

   lb_clean(1)
       clean up system build directories

Ancillary Commands lb(1) generic live-build wrapper

   lb_build(1)
       alias for all stages

   lb_local(1)
       helper for using a local live-build

   lb_testroot(1)
       ensure that a system is built as root

LOW-LEVEL COMMANDS (PLUMBING)

   The actual work of live-build is implemented in the low-level commands,
   called plumbing. They are not supposed to be used by  end  users,  they
   should  stick  with  porcelains  as  they ensure that all the different
   plumbing commands are executed in the  right  order.  However,  if  you
   intend  to  reuse  live-build  commands  in  your own scripts, then the
   plumbings might be of interest for you.

   Note that the interface (set of options and  the  semantics)  to  these
   low-level  commands  are  meant  to be a lot more stable than Porcelain
   level commands. The interface to Porcelain commands on the  other  hand
   are subject to change in order to improve the end user experience.

Bootstrap commands lb_bootstrap_cache(1) cache bootstrap stage

   lb_bootstrap_cdebootstrap(1)
       bootstrap a Debian system with cdebootstrap(1)

   lb_bootstrap_copy(1)
       bootstrap by copying the host system

   lb_bootstrap_debootstrap(1)
       bootstrap a Debian system with debootstrap(8)

Chroot commands lb_chroot_apt(1) manage /etc/apt/apt.conf

   lb_chroot_cache(1)
       cache chroot stage

   lb_chroot_debianchroot(1)
       manage /etc/debian_chroot

   lb_chroot_devpts(1)
       mount /dev/pts

   lb_chroot_dpkg(1)
       manage /sbin/dpkg

   lb_chroot_hacks(1)
       execute hacks in chroot

   lb_chroot_hooks(1)
       execute hooks in chroot

   lb_chroot_hostname(1)
       manage /bin/hostname

   lb_chroot_hosts(1)
       manage /etc/hosts

   lb_chroot_install-packages(1)
       install queued packages into chroot

   lb_chroot_interactive(1)
       make build interactive

   lb_chroot_linux-image(1)
       manage /etc/kernel-img.conf

   lb_chroot_local-hooks(1)
       execute local hooks in chroot

   lb_chroot_local-includes(1)
       copy local files into chroot

   lb_chroot_localization(1)
       install localization packages into chroot

   lb_chroot_local-packages(1)
       queue install of local packages into chroot

   lb_chroot_local-patches(1)
       apply local patches against chroot

   lb_chroot_local-preseed(1)
       use debconf local preseeding file

   lb_chroot_packages(1)
       queue install of packages into chroot

   lb_chroot_packageslists(1)
       queue install of packages lists into chroot

   lb_chroot_preseed(1)
       use debconf preseeding file

   lb_chroot_proc(1)
       mount /proc

   lb_chroot_resolv(1)
       manage /etc/resolv.conf

   lb_chroot_selinuxfs(1)
       mount /selinux

   lb_chroot_sources(1)
       manage /etc/apt/sources.list

   lb_chroot_symlinks(1)
       convert symlinks

   lb_chroot_sysfs(1)
       mount /sys

   lb_chroot_sysvinit(1)
       configure sysvinit

   lb_chroot_sysv-rc(1)
       manage /usr/sbin/policy-rc.d

   lb_chroot_tasks(1)
       install tasks into chroot

Binary commands lb_binary_chroot(1) copy chroot into chroot

   lb_binary_debian-installer(1)
       install debian-installer into binary

   lb_binary_disk(1)
       install disk information into binary

   lb_binary_encryption(1)
       encrypts rootfs

   lb_binary_grub(1)
       installs grub into binary

   lb_binary_grub2(1)
       installs grub2 into binary

   lb_binary_includes(1)
       copy files into binary

   lb_binary_iso(1)
       build iso binary image

   lb_binary_linux-image(1)
       install linux-image into binary

   lb_binary_local-hooks(1)
       execute local hooks in binary

   lb_binary_local-includes(1)
       copy files into binary

   lb_binary_local-packageslists(1)
       install local packages lists into binary

   lb_binary_manifest(1)
       create manifest

   lb_binary_checksums(1)
       create binary checksums (md5, sha1, and/or sha256)

   lb_binary_memtest(1)
       installs a memtest into binary

   lb_binary_net(1)
       build netboot binary image

   lb_binary_rootfs(1)
       build rootfs image

   lb_binary_silo(1)
       installs silo into binary

   lb_binary_syslinux(1)
       installs syslinux into binary

   lb_binary_tar(1)
       build harddisk binary image

   lb_binary_usb(1)
       build binary usb-hdd image

   lb_binary_virtual-hdd(1)
       build binary virtual-hdd image

   lb_binary_win32-loader(1)
       installs win32-loader into binary

   lb_binary_yaboot(1)
       installs yaboot into binary

Source commands lb_source_debian(1) download sources

   lb_source_debian-live(1)
       copy debian-live config into source

   lb_source_disk(1)
       install disk information into source

   lb_source_iso(1)
       build iso source image

   lb_source_checksums(1)
       create source checksums (md5, sha1, and/or sha256)

   lb_source_net(1)
       build source net image

   lb_source_tar(1)
       build source tarball

   lb_source_usb(1)
       build source usb-hdd image

   lb_source_virtual-hdd(1)
       build source virtual-hdd image

CONFIG FILES

   Many  live-build commands make use of files in the config/ directory to
   control what they do. Besides the common config/common, which  is  used
   by  all  live-build  commands,  some  additional  files  can be used to
   configure the behavior of specific live-build commands. These files are
   typically  named  config/stage or config/stage_helper (where "stage" of
   course, is replaced with the name of the stage that they belong to, and
   "helper" with the name of the helper).

   For example, lb_bootstrap_debootstrap uses files named config/bootstrap
   and config/bootstrap_debootstrap to read the options it will  use.  See
   the  man  pages  of individual commands for details about the names and
   formats of the files they use. Generally, these files contain variables
   with   values  assigned,  one  variable  per  line.  Some  programs  in
   live-build use pairs of values or slightly  more  complicated  variable
   assignments.

   Note  that  live-build  will  respect  environment  variables which are
   present in the context of the shell it is running. If variables can  be
   read  from  config files, then they override environment variables, and
   if command line options are used,  they  override  values  from  config
   files. If no value for a given variable can be found and thus is unset,
   live-build will automatically set it to the default value.

   In some rare cases, you may want to have different  versions  of  these
   files  for  different  architectures  or  distributions. If files named
   config/stage.arch or config/stage_helper.arch, and config/stage.dist or
   config/stage_helper.dist  exist, where "arch" is the same as the output
   of "dpkg --print-architecture" and "dist" is the same as  the  codename
   of  the  target  distribution,  then they will be used in preference to
   other, more general files.

   All config files are shell scripts which are sourced  by  a  live-build
   program.  That  means  they have to follow the normal shell syntax. You
   can also put comments in these files;  lines  beginning  with  "#"  are
   ignored.

Written by Daniel Baumann daniel@debian.org.

***

live-config - System Configuration Scripts

DESCRIPTION

   live-config  contains  the  scripts that configure a Debian Live system
   during the boot process (late userspace).

CONFIGURATION

   live-config can be configured through boot parameters or  configuration
   files.  If  both  mechanisms  are  used  for a certain option, the boot
   parameters take precedence over the  configuration  files.  When  using
   persistency, live-config scripts are only run once.

Boot Parameters (scripts) live-config is only activated if 'boot=live' is used as a boot parameter. Additionally, live-config needs to be told which scripts to run through the 'live-config' parameter or which scripts to not run through the 'live-noconfig' parameter. If both 'live-config' and 'live-noconfig' are used, or, if either one is specified multiple times, always the later one takes precedence over the previous one(s).

   live-config | config
   
       All scripts are run.  This  is  what  Debian  Live  images  use  by
       default.

   live-config=SCRIPT1,SCRIPT2,  ... SCRIPTn | config=SCRIPT1,SCRIPT2, ...
   SCRIPTn
   
       Only the specified scripts are run. Note that  the  order  matters,
       e.g.  'live-config=sudo,user-setup'  would  not work since the user
       needs to be added before it can be configured for sudo. Look at the
       filenames  of  the  scripts  in /lib/live/config for their ordering
       number.

   live-noconfig | noconfig
   
       No  script  is  run.  This  is  the  same  as  not  using  any   of
       'live-config' or 'live-noconfig'.

   live-noconfig=SCRIPT1,SCRIPT2,  ... SCRIPTn | noconfig=SCRIPT1,SCRIPT2,
   ... SCRIPTn
   
       All scripts are run, except the specified ones.

Boot Parameters (options) Some individual scripts can change their behaviour upon a boot parameter.

   live-config.hostname=HOSTNAME | hostname=HOSTNAME
   
       Allows to set the hostname of the system. The default is 'debian'.

   live-config.username=USERNAME | username=USERNAME
   
       Allows  to  set  the  username that gets created for autologin. The
       default is 'user'.

   live-config.user-fullname="USER   FULLNAME"    |    user-fullname="USER
   FULLNAME"
   
       Allows  to  set  the  fullname  of  the users that gets created for
       autologin. The default is 'Debian Live user'.

   live-config.locales=LOCALE1,LOCALE2        ...        LOCALEn         |
   locales=LOCALE1,LOCALE2 ... LOCALEn
   
       Allows  to  set  the  locale of the system, e.g. 'de_CH.UTF-8'. The
       default is 'en_US.UTF-8'.  In  case  the  selected  locale  is  not
       already  available  on the system, it is automatically generated on
       the fly.

   live-config.timezone=TIMEZONE | timezone=TIMEZONE
   
       Allows to set the timezone of the system, e.g. 'Europe/Zurich'. The
       default is 'UTC'.

   live-config.utc=yes|no | utc=yes|no
   
       Allows  to change if the system is assuming that the hardware clock
       is set to UTC or not. The default is 'yes'.

   live-config.keyboard-model=KEYBOARD_MODEL                             |
   keyboard-model=KEYBOARD_MODEL
   
       Allows to change the keyboard model. There is no default value set.

   live-config.keyboard-layouts=KEYBOARD_LAYOUT1,KEYBOARD_LAYOUT2      ...
   KEYBOARD_LAYOUTn  |  keyboard-layouts=KEYBOARD_LAYOUT1,KEYBOARD_LAYOUT2
   ... KEYBOARD_LAYOUTn
   
       Allows  to  change  the  keyboard  layouts.  If  more  than  one is
       specified, the tools of  the  desktop  environment  will  allow  to
       switch it under X11. There is no default value set.

   live-config.keyboard-variant=KEYBOARD_VARIANT                         |
   keyboard-variant=KEYBOARD_VARIANT
   
       Allows to change the keyboard variant. There is  no  default  value
       set.

   live-config.keyboard-options=KEYBOARD_OPTIONS                         |
   keyboard-options=KEYBOARD_OPTIONS
   
       Allows to change the keyboard options. There are no  default  value
       set.

   live-config.sysv-rc=SERVICE1,SERVICE2     ...    SERVICEn    |    sysv-
   rc=SERVICE1,SERVICE2 ... SERVICEn
   
       Allows to disable sysv services through update-rc.d.

   live-config.x-session-manager=X_SESSION_MANAGER                       |
   x-session-manager=X_SESSION_MANAGER
   
       Allows to set the x-session-manager through update-alternatives.

   live-config.xorg-driver=XORG_DRIVER | xorg-driver=XORG_DRIVER
   
       Allows to set xorg driver instead of autodetecting it.

   live-config.xorg-resolution=XORG_RESOLUTION                           |
   xorg-resolution=XORG_RESOLUTION
   
       Allows to set xorg resolution instead of autodetecting it.

   live-config.hooks=filesystem|medium|URL1|URL2|     ...     |URLn      |
   hooks=medium|filesystem|URL1|URL2| ... |URLn
   
       Allows  to fetch and execute one or more arbitrary files. Note that
       the URLs must be fetchable by wget  (http,  ftp  or  file://),  the
       files are executed in /tmp of the running live system, and that the
       files needs their dependencies, if any, already installed, e.g.  if
       a  python  script  should  be  executed  the  system  needs  python
       installed. Some hooks for some common use-cases  are  available  at
       /usr/share/doc/live-config/examples/hooks/                      and
       <http://live.debian.net/other/hooks>.
       
       If the file is placed on the live medium, it can  be  fetched  with
       file:///live/image/FILE,  or with file:///FILE if it is in the root
       filesystem of the live system itself.
       
       All hooks in /lib/live/hooks/ in the root filesystem  of  the  live
       system   can   be   automatically   be  enabled  with  the  keyword
       'filesystem'.
       
       All hooks in /live/hooks/ of the live medium can  be  automatically
       be enabled with the keyword 'medium'.
       If  several  mechanisms  are  combined,  then  filesystem hooks are
       executed first, then medium hooks, and last the network hooks.

Boot Parameters (shortcuts) For some common use cases where it would require to combine several individual parameters, live-config provides shortcuts. This allows both to have full granularity over all the options, as well keep things simple.

   live-config.noroot | noroot
   
       Disables  the  sudo  and  policykit,  the  user  cannot  gain  root
       privileges on the system.

   live-config.noautologin | noautologin
   
       Disables  both  the  automatic  console  login  and  the  graphical
       autologin.

   live-config.nottyautologin | nottyautologin
   
       Disables  the  automatic  login  on  the console, not affecting the
       graphical autologin.

   live-config.nox11autologin | nox11autologin
   
       Disables  the  automatic  login  with  any  display  manager,   not
       affecting tty autologin.

Boot Parameters (special options) For special use cases there are some special boot paramters.

   live-config.debug | debug
   
       Enables debug output in live-config.

Configuration Files live-config can be configured (but not activated) through configuration files. Everything but the shortcuts that can be configured with a boot parameter can be alternatively also be configured through one or more files. If configuration files are used, the 'boot=live' parameter is still required to activate live-config.

   Configuration  files can be placed either in the root filesystem itself
   (/etc/live/config.conf, /etc/live/config.d/),  or  on  the  live  media
   (live/config.conf,  live/config.d/).  If  both  places  are  used for a
   certain option, the ones from the live media take precedence  over  the
   ones from the root filesystem.

   Although  the  configuration  files placed in the conf.d directories do
   not require a particular name or suffix, it's suggest  for  consistency
   to  either  use  'vendor.conf'  or  'project.conf'  as  a naming scheme
   (whereas 'vendor' or  'project'  is  replaced  with  the  actual  name,
   resulting in a filename like 'debian-eeepc.conf').

   LIVE_CONFIGS=SCRIPT1,SCRIPT2, ... SCRIPTn
   
       This variable equals the 'live-config=SCRIPT1,SCRIPT2, ... SCRIPTn'
       parameter.

   LIVE_NOCONFIGS=SCRIPT1,SCRIPT2, ... SCRIPTn
   
       This  variable  equals  the   'live-noconfig=SCRIPT1,SCRIPT2,   ...
       SCRIPTn' parameter.

   LIVE_HOSTNAME=HOSTNAME
   
       This variable equals the 'live-config.hostname=HOSTAME' parameter.

   LIVE_USERNAME=USERNAME
   
       This variable equals the 'live-config.username=USERNAME' parameter.

   LIVE_USER_FULLNAME="USER FULLNAME"
   
       This    variable    equals   the   'live-config.user-fullname="USER
       FULLNAME"' parameter.

   LIVE_LOCALES=LOCALE1,LOCALE2 ... LOCALEn
   
       This variable equals the  'live-config.locales=LOCALE1,LOCALE2  ...
       LOCALEn' parameter.

   LIVE_TIMEZONE=TIMEZONE
   
       This variable equals the 'live-config.timezone=TIMEZONE' parameter.

   LIVE_UTC=yes|no
   
       This variable equals the 'live-config.utc=yes|no' parameter.

   LIVE_KEYBOARD_MODEL=KEYBOARD_MODEL
   
       This                variable               equals               the
       'live-config.keyboard-model=KEYBOARD_MODEL' parameter.

   LIVE_KEYBOARD_LAYOUTS=KEYBOARD_LAYOUT1,KEYBOARD_LAYOUT2             ...
   KEYBOARD_LAYOUTn
   
       This                variable               equals               the
       'live-config.keyboard-layouts=KEYBOARD_LAYOUT1,KEYBOARD_LAYOUT2 ...
       KEYBOARD_LAYOUTn' parameter.

   LIVE_KEYBOARD_VARIANT=KEYBOARD_VARIANT
   
       This                variable               equals               the
       'live-config.keyboard-variant=KEYBOARD_VARIANT' parameter.

   LIVE_KEYBOARD_OPTIONS=KEYBOARD_OPTIONS
   
       This               variable               equals                the
       'live-config.keyboard-options=KEYBOARD_OPTIONS' parameter.

   LIVE_SYSV_RC=SERVICE1,SERVICE2 ... SERVICEn
   
       This variable equals the 'live-config.sysv-rc=SERVICE1,SERVICE2 ...
       SERVICEn' parameter.

   LIVE_XORG_DRIVER=XORG_DRIVER
   
       This  variable  equals  the   'live-config.xorg-driver=XORG_DRIVER'
       parameter.

   LIVE_XORG_RESOLUTION=XORG_RESOLUTION
   
       This                variable               equals               the
       'live-config.xorg-resolution=XORG_RESOLUTION' parameter.

   LIVE_HOOKS=filesystem|medium|URL1|URL2| ... |URLn
   
       This               variable               equals                the
       'live-config.hooks=filesystem|medium|URL1|URL2|      ...     |URLn'
       parameter.

CUSTOMIZATION

   live-config can be easily customized for downstream projects  or  local
   usage.

Adding new config scripts Downstream projects can put their scripts into /lib/live/config and don't need to do anything else, the scripts will be called automatically during boot.

   The  scripts  are best put into an own debian package. A sample package
   containing    an     example     script     can     be     found     in
   /usr/share/doc/live-config/examples.

Removing existing config scripts It's not really possible to remove scripts itself in a sane way yet without requiring to ship a locally modified live-config package. However, the same can be acchieved by disabling the respective scripts through the live-noconfig mechanism, see above. To avoid to always need specifing disabled scripts through the boot parameter, a configuration file should be used, see above.

   The configuration files for the live system itself are best put into an
   own   debian   package.   A   sample   package  containing  an  example
   configuration can be found in /usr/share/doc/live-config/examples.

SCRIPTS

   live-config   currently   features    the    following    scripts    in
   /lib/live/config.

   hostname
   
       configures /etc/hostname and /etc/hosts.

   user-setup
   
       adds an live user account.

   sudo
   
       grants sudo privileges to the live user.

   locales
   
       configures locales.

   tzdata
   
       configures /etc/timezone.

   gdm 
   
       configures autologin in gdm.

   gdm3
   
       configures autologin in gdm3 (squeeze and newer).

   kdm 
   
       configures autologin in kdm.

   lxdm
   
       configures autologin in lxdm.

   nodm
   
       configures autologin in nodm.

   slim
   
       configures autologin in slim.

   xinit
   
       configures autologin with xinit.

   console-common,  console-setup (lenny), keyboard-configuration (squeeze
   and newer)
   
       configures the keyboard.

   sysvinit
   
       configures sysvinit.

   sysv-rc
   
       configures sysv-rc by disabling listed services.

   login
   
       disables lastlog.

   apport (ubuntu only)
   
       disables apport.

   gnome-panel-data
   
       disables lock button for the screen.

   gnome-power-manager
   
       disables hibernation.

   gnome-screensaver
   
       disables the screensaver locking the screen.

   initramfs-tools
   
       makes update-initramfs to also update the  live  media  when  using
       persistency.

   kaboom
   
       disables kde migration wizard (squeeze and newer).

   kde-services
   
       disables some unwanted KDE services (squeeze and newer).

   kpersonalizer
   
       disables kde configuration wizard (lenny).

   debian-installer-launcher
   
       adds debian-installer-launcher on users desktop.

   module-init-tools
   
       automatically load some modules on some architectures.

   policykit
   
       grant user privilegies through policykit.

   sslcert
   
       regenerating ssl snake-oil certificates.

   update-notifier
   
       disables update-notifier.

   anacron
   
       disables anacron.

   util-linux
   
       disables util-linux' hwclock.

   login
   
       disables lastlog.

   xserver-xorg
   
       configures xserver-xorg.

   ureadahead (ubuntu only)
   
       disables ureadahead.

   openssh-server
   
       recreates openssh-server host keys.

   hooks
   
       allows  to  run arbitrary commands from a script placed on the live
       media or an http/ftp server.

FILES

   /etc/live/config.conf

   /etc/live/config.d/

   live/config.conf

   live/config.d/

   /lib/live/config.sh

   /lib/live/config/

   /var/lib/live/config/

Written by Daniel Baumann daniel@debian.org.

***

Below is a copy from /usr/share/doc/live-boot/copyright in live-boot_2.0.15-1_all.deb package:

Files: *

Copyright:

 (C) 2007-2011 Daniel Baumann <daniel@debian.org>
 
 (C) 2005-2008 Canonical Ltd. <http://www.cannonical.com/>
 
 (C) 2008 Chris Lamb <chris@debian.org>
 
 (C) 2006-2007 Marco Amadori <marco.amadori@gmail.com>
 
 (C) 2008 Dell Inc. <http://www.dell.com/>
 
License: GPL-3+

 This program is free software: you can redistribute it and/or modify
 it under the terms of the GNU General Public License as published by
 the Free Software Foundation, either version 3 of the License, or
 (at your option) any later version.
 
 .
 
 This program is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 GNU General Public License for more details.
 
 .
 
 You should have received a copy of the GNU General Public License
 along with this program. If not, see <http://www.gnu.org/licenses/>.
 .
 
 On Debian systems, the complete text of the GNU General Public License
 can be found in /usr/share/common-licenses/GPL-3 file.


