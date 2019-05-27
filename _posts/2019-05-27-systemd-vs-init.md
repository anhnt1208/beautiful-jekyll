---
layout: post
title: Systemd vs /etc/init.d
subtitle: replace Systemd with init
tags: [unix, ubuntu]
---

The init daemon is going to be replaced with daemon systemd on some of the Linux Distributions, while a lot of them have already implemented it. This is/will be creating a huge gap between traditional Unix/Linux Guard and New Linux Guard – programmers and System Admins.

## What is Init ?
The init is a daemon process which starts as soon as the computer starts and continue running till, it is shutdown. Init is the first process that starts when a computer boots, making it the parent of all other running processes directly or indirectly and hence typically it is assigned “pid=1“.

If somehow init daemon could not start, no process will be started and the system will reach a stage called “Kernel Panic“. init is most commonly referred to as System V init. System V is the first commercial UNIX Operating System designed and usages of init on most of the Linux Distribution of today is identical with System V OS with a few exception like Slackware using BSD-style and Gentoo using custom init.

init stores startup scripts at `/etc/init.d/`

The need to replace init with something more perfect was felt from a long time and several alternatives were developed from time-to-time, some of which became distribution’s native init replacement, some of which are:
- **Upstart** : A init replacement daemon implemented in Ubuntu GNU/Linux and designed to start process asynchronously.
- **Epoch** : A init replacement daemon built around simplicity and service management, designed to start process single-threaded.
- **Mudar** : A init replacement daemon written in Python, implemented on Pardus GNU/Linux and designed to start process asynchronously.
- **systemd** : A init replacement daemon designed to start process in parallel, implemented in a number of standard distribution – Fedora, OpenSuSE, Arch, RHEL, CentOS, etc.

## What is systemd?
A systemd is a System Management Daemon named with UNIX convention to add ‘d‘ at the end of daemon. So, that they can be easily recognized.
Initially it was released under GNU General Public License, but now the releases are made under GNU Lesser General Public License.
Similar to init, systemd is the parent of all other processes directly or indirectly and is the first process that starts at boot hence typically assigned a “pid=1“.

A systemd, may refer to all the packages, utilities and libraries around daemon.
It was designed to overcome the shortcomings of init.
It itself is a background processes which is designed to start processes in parallel, thus reducing the boot time and computational overhead.
It has a lot other features as compared to init.

Systemd keeps startup scripts at `/lib/systemd`, and symbolic links of them are contained at `/etc/systemd`

## Why there was a need to replace init?
A init process starts serially i.e., one task starts only after the last task startup was successful and it was loaded in the memory. This often resulted into delayed and long booting time. However, systemd was not designed for speed but for getting the things done neatly which in turns avoid all the UN-necessary delay.

systemd-analyze
systemd-analyze blame
journalctl -u apt-daily-upgrade -b

|Features|init|systemd|
|--------|----|-------|
|DBus Dependency – Mandatory|No|Yes|
|Device based Activation|No|Yes|
|Device dependency configuration with udev|No|Yes|
|Timer based Activation|Cron/at|Proprietary|
|Quota Management|No|Yes|
|Automatic Service Dependency Handling|No|Yes|
|Kills users Process at logout|No|Yes|
|Swap Management|No|Yes|
|SELinux integration|No|Yes|
|Support for Encrypted HDD|No|Yes|
|Static kernle module loading|No|Yes|
|GUI|No|Yes|
|List all the child processes|No|Yes|
|Sysv compatible|Yes|Yes|
|Interactive booting|No|Yes|
|Portable to non x86|Yes|No|
|Adopted on|Several Distro|Several Distro|
|Parallel service startup|No|Yes|
|Resource limit per service|No|Yes
|Easy extensible startup script|Yes|No|
|Separate Code and Configuration File|Yes|No|
|Automatic dependency calculation|No|Yes|
|Verbose debug|Yes|No|
|Version|N/A|V44+|
|Size|560 KB|N/A|
|Number of Files|75 files|900 files + glib + DBus|
|Lines of code – LOC|15000 (Approx)|224000 (Approx) (inc Codes, comments and white space) 125000 (Approx) (acctual code)|


## SysVinit to Systemd Cheatsheet
|Sysvinit|Command|Systemd Command Notes|
|--------|----|-------|
|service <service-name> start|systemctl start <service-name>|Used to start a service (not reboot persistent)|
|service <service-name> stop|systemctl stop <service-name>|Used to stop a service (not reboot persistent)|
|service <service-name> restart|systemctl restart <service-name>|Used to stop and then start a service|
|service <service-name> reload|systemctl reload <service-name>|When supported, reloads the config file without interrupting pending operations.|
|service <service-name> condrestart|systemctl condrestart <service-name>|Restarts if the service is already running.|
|service <service-name> status|systemctl status <service-name>|Tells whether a service is currently running.|
|ls /etc/rc.d/init.d/|systemctl (or) systemctl list-unit-files --type=service (or) ls /lib/systemd/system/*.service /etc/systemd/system/*.service|Used to list the services that can be started or stopped. Used to list all the services and other units|
|chkconfig <service-name> on|systemctl enable <service-name>|Turn the service on, for start at next boot, or other trigger.|
|chkconfig <service-name> off|systemctl disable <service-name>|Turn the service off for the next reboot, or any other trigger.|
|chkconfig <service-name>|systemctl is-enabled <service-name>|Used to check whether a service is configured to start or not in the current environment.|
|chkconfig --list|systemctl list-unit-files --type=service (or) ls /etc/systemd/system/*.wants/|Print a table of services that lists which runlevels each is configured on or off|
|chkconfig --list \| grep 5:on|systemctl list-dependencies graphical.target|Print a table of services that will be started when booting into graphical mode|
|chkconfig <service-name> --list|ls /etc/systemd/system/*.wants/<service-name>.service|Used to list what levels this service is configured on or off|
|chkconfig <service-name> --add|systemctl daemon-reload|Used when you create a new service file or modify any configuration|

## Analyze the boot process with the blame command
Run `systemd-analyze` without any extra commands will display the last boot statistics
```bash
$ systemd-analyze
Startup finished in 4.038s (kernel) + 25.119s (userspace) = 29.157s
graphical.target reached after 23.117s in userspace
```
Run `systemd-analyze blame` to get a verbose output
```bash
$ systemd-analyze blame
  4.392s apt-daily-upgrade.service
  2.777s cloud-init-local.service
  2.533s apparmor.service
  2.309s pollinate.service
  2.187s dev-xvda1.device
  1.374s cloud-init.service
  1.248s cloud-config.service
```
To view full log service status
```bash
journalctl -u <service-name>
```
To dump our boot info into a plot data
```bash
systemd-analyze plot > /tmp/plot.svg
eog /tmp/plot.svg
```

### References
[systemd replaces init in linux](https://www.tecmint.com/systemd-replaces-init-in-linux)   
[blame systemd](http://0pointer.de/blog/projects/blame-game.html)   
[SysVinit to Systemd Cheatsheet](https://fedoraproject.org/wiki/SysVinit_to_Systemd_Cheatsheet)   
