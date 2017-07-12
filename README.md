mkdebianrfs
===========

script that uses qemu and debbootstrap to make a core debian rootfs

Go to http://www.elinux.org/How_to_make_a_debian_rootfs_for_MIPS_CI20 for instructions

Examples:
sudo ./mkdebianrfs.sh mipsel jessie jessie_mipsel

lxc-minimal
===========

LXC 2.0 container build script compatible with Debian Jessie for the MIPSEL architecture
Some LXC features may not work (like capabilities)
Place script and make executable in /usr/share/lxc/templates/lxc-minimal

Examples:
sudo lxc-create -n jessie_mipsel -t minimal -- -a mipsel -r jessie --mirror http://ftp.debian.org/debian
