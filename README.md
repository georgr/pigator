# pigator

## setup onewire


sudo raspi-config

1. 5 - Interfacing Options
1. P6 - serial
1. "Would you like a login shell to be accessible over serial?" -> No
1. " Would you like the serial port hardware to be enabled?" -> Yes

sudo vim /boot/config.txt

add at the end of the file: 

dtoverlay=disable-bt

dtparam=i2c_arm=on

sudo systemctl disable hciuart

sudo vim /etc/modules

add: i2c_dev

sudo modprobe i2c_dev

sudo apt-get install owfs

sudo vim /etc/owfs.conf

#server: FAKE = DS18S20,DS2405

server: i2c = ALL:ALL

reboot the pi

sudo owfs --i2c=ALL:ALL --allow_other /mnt/

browse to http://ip_or_raspberry:2121


## setup rtc

sudo echo ds1307 0x68 > /sys/class/i2c-adapter/i2c-1/new_device

sleep 2

sudo hwclock

sudo hwclock -w

sudo hwclock -s

sudo apt-get -y remove fake-hwclock

sudo rm /etc/cron.hourly/fake-hwclock

sudo update-rc.d -f fake-hwclock remove

sudo rm /etc/init.d/fake-hwclock

sudo systemctl enable hwclock.sh

## install node-red

apt install build-essential git


## install knxd

https://wiki.fhem.de/wiki/Knxd

sudo apt-get update

sudo apt-get install debhelper cdbs automake libtool libusb-1.0-0-dev git-core build-essential libsystemd-dev dh-systemd libev-dev cmake

git clone https://github.com/knxd/knxd.git

cd knxd

git checkout deb

dpkg-buildpackage -b -uc

cd ..

sudo dpkg -i knxd_*.deb knxd-tools_*.deb

sudo udevadm info -a /dev/ttyAMA0 | grep KERNELS.*serial

sudo udevadm info -a /dev/ttyAMA0 | grep {id}

sudo vim /etc/udev/rules.d/70-knxd.rules

ACTION=="add", SUBSYSTEM=="tty", ATTRS{id}=="00241011", KERNELS=="3f201000.serial", SYMLINK+="ttyKNX1", OWNER="knxd"

sudo reboot

ls -ahl /dev/ttyKNX1 (lrwxrwxrwx 1 root root 7 Feb 14  2019 /dev/ttyKNX1 -> ttyAMA0)

ls -ahl /dev/ttyAMA0 (crw-rw---- 1 knxd dialout 204, 64 Feb 14  2019 /dev/ttyAMA0)

