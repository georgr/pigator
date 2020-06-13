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



