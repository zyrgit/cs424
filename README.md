# CS424 Real Time System Lab
You can follow this to setup Raspberry Pi (RPi). Note: `On laptop` means using the terminal logged in to your own laptop; `On RPi` means using the terminal logged in to your Raspberry Pi. 

# Setup RPi:

1. Download SD Formatter 4.0 for either Windows or Mac https://www.sdcard.org/downloads/formatter_4/index.html
Install it on your laptop and format the SD card using default option. 


2. On laptop (Mac), insert SD card (assume appearing as BOOT) and run:
```
cd /Volumes/BOOT
wget https://downloads.raspberrypi.org/NOOBS_latest
unzip -a NOOBS_latest
rm -f NOOBS_latest
```
If not using Mac, see https://www.raspberrypi.org/documentation/installation/noobs.md

Once flashed, eject the SD card and plug it in RPi, connect RPi to a screen (HDMI), a mouse and a keyboard. Power it up.


3. Once RPi powers up, select raspbian OS to install. Connect RPi to WiFi using the screen, mouse, and keyboard. 

On RPi, run `ifconfig` to see RPi's IP (e.g. `10.194.113.96`). 

On laptop, SSH into RPi using `ssh pi@10.194.113.96` with password `raspberry`. Run `sudo service ssh restart` on RPi if there is connection refused error. 

4. Setup WiFi configurations.

Run `echo -n 'Your_UIUC_password' | iconv -t UTF-16LE | openssl md4` and copy the string after `(stdin)= `.

Run `sudo vi /etc/wpa_supplicant/wpa_supplicant.conf`, press `i` entering edit mode, type the following:
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
    proto=WPA2
    eap=PEAP
    ca_cert="/etc/ssl/certs/AddTrust_External_Root.pem"
    identity="zhao97"
    password=hash:4088182b4f4f1e58ad9c497b28a72ac7
    phase1="peapver=0"
    phase2="MSCHAPV2"
}
```

Run `sudo vi /etc/network/interfaces` and type the following:
```
source-­directory /etc/network/interfaces.d

auto lo iface
lo inet loopback

allow-hotplug wlan0
iface wlan0 inet dhcp
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```


