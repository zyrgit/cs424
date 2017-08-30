# CS424 Real Time System Lab
You can follow this to setup Raspberry Pi (RPi). Note: `On laptop` means using the terminal logged in to your own laptop; `On RPi` means using the terminal logged in to your Raspberry Pi. 


# Setup RPi:

## 1. Format SD card.
Download SD Formatter 4.0 for either Windows or Mac https://www.sdcard.org/downloads/formatter_4/index.html

Install it on your laptop and format the SD card using default option. 


## 2. Flash SD card.

On laptop (Mac), insert SD card (assume appearing as BOOT) and run:
```
cd /Volumes/BOOT
wget https://downloads.raspberrypi.org/NOOBS_latest
unzip -a NOOBS_latest
rm -f NOOBS_latest
```
If not using Mac, see https://www.raspberrypi.org/documentation/installation/noobs.md

Once flashed, eject the SD card and plug it in RPi, connect RPi to a screen (HDMI), a mouse and a keyboard. Power it up.


## 3. Install OS.

Once RPi powers up, select raspbian OS to install. You can choose to connect to WiFi before installation via the screen connected to RPi. 


## 4. Setup WiFi configurations.

On RPi, run `ifconfig` to see RPi's IP (e.g. `10.194.113.96`). 

On laptop, SSH into RPi using `ssh pi@10.194.113.96` with password `raspberry`. Run `sudo service ssh restart` on RPi if there is connection refused error. 

On RPi, run `echo -n 'Your_UIUC_password' | iconv -t UTF-16LE | openssl md4` and copy the string after `(stdin)= `, this is to replace the password field in wpa_supplicant.conf. Run `history -cw` to clear cmd history.

On RPi, run `sudo nano /etc/wpa_supplicant/wpa_supplicant.conf`, change to the following:
```
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="RiLeGouLe"
    psk="huskytrail936"
    key_mgmt=WPA-PSK
}

network={
        ssid="IllinoisNet"
        key_mgmt=WPA-EAP
        identity="zhao97"
        password=hash:4088182b4f4f1e58ad9c497b28a72ac7
}
```
Use `Ctrl-o`, `Enter`, `Ctrl-x` to save and exit nano.

On RPi, run `sudo nano /etc/network/interfaces` and change to the following:
```
source-­directory /etc/network/interfaces.d

auto lo iface
lo inet loopback

allow-hotplug wlan0
iface wlan0 inet dhcp
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```



