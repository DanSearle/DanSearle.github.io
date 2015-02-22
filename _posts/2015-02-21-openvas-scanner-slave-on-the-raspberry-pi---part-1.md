---
layout: post
title: "Openvas Scanner Slave on the Raspberry Pi   Part 1"
description: ""
category: security
tags: [openvas RaspberryPi]
---
{% include JB/setup %}

Setup
-----

For my initial setup the Raspberry Pi will simply be a node which will carry out
scanning on a particular network on behaf of another machine.

* 1 Raspberry Pi B+
* 1 Kali Linux virtual machine installed to a virtual drive with VirtualBox
  - note you will need a virtual drive 12GB+ otherwise the install will fail.
* Micro SD card (I would recommend 8GB+ Class 10) with Kali Linux written to it.
    Instructions are available in the [Kali documentation](http://docs.kali.org/armel-armhf/install-kali-linux-arm-raspberry-pi)

Initial Setup
-------------
After booting the Pi with the Micro SD card

* Login via ssh (root:toor)
* Install raspi-config
* raspi-config -> expand to sd card size -> reboot
* apt-get update && apt-get upgrade
* change root password

## TODO: Explain raspi-config setup

Openvas Scanner Setup
---------------------

* apt-get install openvas-scanner openvas-manager
* openvas-nvt-sync
* apt-get install xsltproc # Required dependency not installed by openvas-scanner or openvas-manager
* test -e /var/lib/openvas/CA/cacert.pem  || openvas-mkcert -q
* openvas-mkcert-client -i -n om # -i before -n instals the certificate - it semes the documented `openvas-mkcert-client -n om -i` does not take the i option into account
* /etc/init.d/openvas-manager stop
* /etc/init.d/openvas-scanner stop
* openvassd
* openvasmd --rebuild # Can take a long time !!
* openvas-scap-data-sync
* openvas-certdata-sync
* test -e /var/lib/openvas/users/admin || (openvasmd --create-user=admin --role=Admin && openvasmd --user=admin --new-password=admin)
* openvasmd --list-users
* killall openvassd

Start Servers
-------------

/etc/default/openvas-manager
Set MANAGER_ADDRESS to 0.0.0.0

* /etc/init.d/openvas-scanner
 - If ERROR reported check if openvas is already running - `ps aux | grep openvas` e.g. it may be loading the NVTs `openvassd: Reloaded 16050 of 37996 NVTs (42% / ETA: 04:52)`
 - Or use /etc/init.d/openvas-scanner status
* /etc/init.d/openvas-manager
 - Sometimes returns ERROR yet the manager will start?

* Check it is listening
netstat -apn | grep openvasmd
tcp        0      0 0.0.0.0:9390            0.0.0.0:*               LISTEN      2893/openvasmd  

TODO:
Memory split between GPU
New Firmware (after October 2012)

Edit /boot/config.txt and add or edit the following line:

gpu_mem=16
The value can be 16, 64, 128 or 256 and represents the amount of RAM available to the GPU.


Dial back scanning to 2 concurrent hosts with 2 tests, source:

http://cromwell-intl.com/linux/raspberry-pi/openvas.html

Overclocking:

Ensure cpufeq is installed: apt-get install cpufreq

Useful commands
---------------

* Unfortunately the `openvasmd --rebuild` command does not report any error requiring you to check the 
  $? variable for success. I would recommend using `openvasmd --progress --rebuild -v` command instead
  where `Rebuilding NVT cache... done.' should be reported on success or 'Rebuilding NVT cache... failed.' on failure.

* Refreshing NVT cache from scratch `openvas-nvt-sync --wget`

Helpful tools
-------------
* apt-get install htop iotop lsof strace nethogs
* iotop -o


Wireless IDS
------------
TODO
