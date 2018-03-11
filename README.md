# Linux-kernel
This repo contain the content of Linux kernel

uname -n kernal name
uname -s computer name
uname -r kernel release
uname -v kernel version
uname -m Machine name
uname -p Processor type
uname -i hardware platform
uname -o operating system

The information that we harvest with the uname command is coming from the file in the procfs /proc/version

/proc/version -- This command uname displays information from the file /proc/version

Trobleshooting enviorment is looking at /proc/cmdline

/proc/cmdline -- To view which options were supplied to the kernel at boot time, we can view the file /proc/cmdline

if you want to open configuration: cat /boot/config-$(uname -r), usefull if we have multiple kernels.

or 

ls /boot

yum install kernel-doc
ls /usr/share/doc/kernel-doc-3.10.0/Documentation/
less filesystem/proc.txt

file /boot/vmlinuz

type of initail ram disk: initrd and initramfs

mount -t sysfs initrd /mnt
umount /mnt

ls /usr/src/kernels/
tar -Jxvf linux-4.15.7.tar.xz -C /usr/src/kernels/

ln -s /usr/src/kernels/linux-4.15.7/ /usr/src/linux

compling a kernel in /usr/src/linux:

make mrproper
make bzimage
make modules_install
make menuconfig
make modules
make install

make clean
make menuconfig
make bzImage
make modules
make install

mkinitrd:

generated the initrd ram disk

kernel is compiled the ram disk may also need to change

dracut:

mkinitrd relies on udev and script to lessen the amount of hard-coded activity in the initramfs

cat /proc/modules

modinfo sr_mod

systemd, provides a standard process for controlling which program start when a linux system boots.
Runlevels are now target units
SysVinit scripts are now service units
PID 1 is now /usr/lib/systemd/systemd

Targets in systemd    Runlevels with init

poweroff.target         0
rescue.target           1
multiuser.target        2
multiuser.target        3
multiuser.target        4
graphical.target        5
reboot.target           6


cat /etc/system-release

systemctl get-default

systemctl set-default multi-user.target

systemctl isolate multi-user.target


systemctl status sshd

/lib/systemd/system

/etc/systemd/system              need to change anything on running script


systemctl show kdump.service

systemctl list-units
systemctl list-unit-files
