Linux Cheat Sheet
===============
### Index
* [Linux kernel](#linux-kernel)
* [Daemon-Monitoring Daemon](#daemon-monitoring-daemon)
* [Installing and Configuring monit](#installing-and-configuring-monit)

<hr>

# Linux kernel
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

<hr>

 ## Daemon-Monitoring Daemon (DMD)

A daemon-monitoring daemon (DMD) is a utility that watches your services for you and automatically attempts to restart them when they fail.

<hr>

 ## Installing and Configuring monit
 
To install monit, you can either use your Linux system package manager or down- load the tarball from http://www.tildeslash.com/monit. If you’re using the Debian setup, simply enter:
```shell
# apt-get install monit
```
After you’ve installed monit, edit /etc/monit/monitrc. The file created during installa- tion contains lots of examples, and you can find more configuration examples at http://www.tildeslash.com/monit/doc/examples.php. In our case, we want to:

• Enable the monit web interface on port 2812.
• Monitor the proftpd, sshd, mysql, apache, and postfix services.
• Create a Secure Sockets Layer (https) web interface where we can log in with the username admin.
• Tell monit to send email alerts to root@localhost.

Our /etc/monit/monitrc configuration file looks like this:
```shell
     set daemon  60
     set log file syslog facility log_daemon
     set mailserver localhost
     set mail-format { from: monit@server1.centralsoft.org }
     set alert root@localhost
     set httpd port 2812 and
          SSL ENABLE
          PEMFILE  /var/certs/monit.pem
          allow admin: test
     check process proftpd with pidfile /var/run/proftpd.pid
        start program = "/etc/init.d/proftpd start"
        stop program  = "/etc/init.d/proftpd stop"
        if failed port 21 protocol ftp then restart
        if 5 restarts within 5 cycles then timeout
     check process sshd with pidfile /var/run/sshd.pid
        start program  "/etc/init.d/ssh start"
        stop program  "/etc/init.d/ssh stop"
        if failed port 22 protocol ssh then restart
        if 5 restarts within 5 cycles then timeout
     check process mysql with pidfile /var/run/mysqld/mysqld.pid
        group database
        start program = "/etc/init.d/mysql start"
        stop program = "/etc/init.d/mysql stop"
        if failed host 127.0.0.1 port 3306 then restart
        if 5 restarts within 5 cycles then timeout
     check process apache with pidfile /var/run/apache2.pid
        group www
        start program = "/etc/init.d/apache2 start"
        stop program  = "/etc/init.d/apache2 stop"
        if failed host www.centralsoft.org port 80 protocol http
           and request "/monit/token" then restart
        if cpu is greater than 60% for 2 cycles then alert
        if cpu > 80% for 5 cycles then restart
        if totalmem > 500 MB for 5 cycles then restart
        if children > 250 then restart
        if loadavg(5min) greater than 10 for 8 cycles then stop
        if 3 restarts within 5 cycles then timeout
     check process postfix with pidfile /var/spool/postfix/pid/master.pid
        group mail
        start program = "/etc/init.d/postfix start"
        stop  program = "/etc/init.d/postfix stop"
        if failed port 25 protocol smtp then restart
        if 5 restarts within 5 cycles then timeout
```
Statements and options are described in the monit documentation at http://www. tildeslash.com/monit/doc/manual.php.

In the apache section of the monit configuration, you’ll see this statement:
```shell
if failed host www.xxx.org port 80 protocol http and request "/monit/token" then restart
```
This means that monit tries to connect to www.xxx.org on port 80 and tries to access the file /monit/token. Because our web site’s document root is /var/www/www.xxx.org/web, the filename expands to /var/www/www.xxx.org/web/ monit/token. If monit doesn’t succeed, this means Apache isn’t running, so monit tries to restart it.

Now we must create the file /var/www/www.xxx.org/web/monit/token and write some arbitrary string into it:
```shell
# mkdir /var/www/www.centralsoft.org/web/monit
# echo "hello" > /var/www/www.centralsoft.org/web/monit/token
```
You can follow a similar procedure on your own system. 

Next, create a directory to hold the pem cert file (/var/certs/monit.pem) required for
the SSL-encrypted monit web interface: 
```shell
# mkdir /var/certs
# cd /var/certs
```
You’ll need an OpenSSL configuration file to create the certificate. The resulting /var/
certs/monit.pem file should look like this:
```shell
# create RSA certs - Server
```
Now create the certificate:
```shell
# openssl req -new -x509 -days 365 -nodes -config ./monit.cnf -out \ /var/certs/monit.pem -keyout /var/certs/monit.pem
# openssl gendh 512 >> /var/certs/monit.pem
# openssl x509 -subject -dates -fingerprint -noout -in /var/certs/monit.pem # chmod 700 /var/certs/monit.pem
```
Then edit /etc/default/monit to enable the monit daemon. Change startup to 1 and set CHECK_INTERVALS to the interval in seconds at which you would like to check your sys- tem. We chose 60. The file should now look like this:
```shell
     # Defaults for monit initscript
     # sourced by /etc/init.d/monit
     # installed at /etc/default/monit by maintainer scripts
     # Fredrik Steen <stone@debian.org>
     # You must set this variable to for monit to start
     startup=1
     # To change the intervals which monit should run uncomment
     # and change this variable.
     CHECK_INTERVALS=60
```
Finally, start monit:
```shell
# /etc/init.d/monit start
```
Now point your browser to https://your_domain:2812/ (make sure port 2812 isn’t blocked by your firewall) and log in with the username admin and password test. You should see the monit web interface.

<hr>

## Email

Mail User Agent (MUA)
Mail Transfer Agent (MTA)
Post Office Protocol (POP)
Interactive Mail Access Protocol (IMAP)
SimpleAuthenticationandSecurityLayer(SASL)
Unsolicited bulk emailers (UBEs), also knowns as spammers.UBEs use a variety of techniques to hide their real identities, including spoofing IP addresses, forging mail envelopes, and relaying through open SMTP servers
unsolicited commercial emailers (UCEs)
