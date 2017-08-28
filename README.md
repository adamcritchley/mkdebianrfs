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

Example QEMU System command line for ARM
========================================
qemu-system-arm -machine vexpress-a9 -kernel vmlinuz-3.2.0-4-vexpress -initrd initrd.img-3.2.0-4-vexpress -append "root=/dev/mmcblk0p2 rootwait vmalloc=256MB devtmpfs.mount=0 mem=1024M console=tty0 rootfstype=ext4" -serial stdio -sd debian_armhf.qcow2 -m 1024 -net nic -net user,hostfwd=tcp::5555-:22
