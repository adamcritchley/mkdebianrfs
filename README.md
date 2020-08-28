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

QemuARMVexpress
========================================

Instructions on how to successfully setup qemu to emulate an arm vexpress a9/a15 board (and possibly any other arm hw that qemu emulates):

1) setup the rootfs

sudo apt-get install qemu-user-static qemu-system-arm

mkdir vexpress
cd vexpress
mkdir qemu-img

# 4GBs should be enough
dd if=/dev/zero of=./vexpress-4G.img bs=4M count=1024
sudo losetup -f ./vexpress-4G.img
sudo mkfs.ext4 /dev/loop0
sudo mount /dev/loop0 qemu-img

# let's bootstrap a saucy armhf rootfs in the qemu-img directory
sudo qemu-debootstrap --arch=armhf saucy qemu-img
sudo cp `which qemu-arm-static` qemu-img/usr/bin/

# setup serial console, apt repositories and network
sudo chroot qemu-img
sed 's/tty1/ttyAMA0/g' /etc/init/tty1.conf > /etc/init/ttyAMA0.conf
echo "deb  http://ports.ubuntu.com saucy main restricted multiverse universe" > /etc/apt/sources.list
apt-get update
echo -e "\nauto eth0\niface eth0 inet dhcp" >> /etc/network/interfaces

# root password
passwd
2) pick and install a kernel
open a web browser and navigate to this page:

https://launchpad.net/ubuntu/+source/linux

pick a kernel that suites your need (e.g. 3.10.0-6.17), download and install it:


apt-get install wget ca-certificates
wget https://launchpad.net/ubuntu/+archive/primary/+files/linux-image-3.10.0-6-generic-lpae_3.10.0-6.17_armhf.deb
dpkg -i linux-image-3.10.0-6-generic-lpae_3.10.0-6.17_armhf.deb

# press CTRL+D to exit the chroot
^D
3) boot it up

# copy kernel, initrd and dtb files
cp qemu-img/boot/vmlinuz-3.10.0-6-generic-lpae .
cp qemu-img/boot/initrd.img-3.10.0-6-generic-lpae .
cp qemu-img/lib/firmware/3.10.0-6-generic-lpae/device-tree/vexpress-v2p-ca15-tc1.dtb .
# umount the rootfs img
sudo umount qemu-img
# and finally boot it up!
qemu-system-arm -kernel vmlinuz-3.10.0-6-generic-lpae -initrd initrd.img-3.10.0-6-generic-lpae -M vexpress-a15 -serial stdio -m 1024 -append 'root=/dev/mmcblk0 rw mem=1024M raid=noautodetect rootwait console=ttyAMA0,38400n8 devtmpfs.mount=0' -sd vexpress-4G.img -dtb ./vexpress-v2p-ca15-tc1.dtb
