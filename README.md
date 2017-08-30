# CS424 Real Time System Lab

# Setup Raspberry Pi (RPi):

1. Download SD Formatter 4.0 for either Windows or Mac https://www.sdcard.org/downloads/formatter_4/index.html
Install it on your laptop and format the SD card using default option. 

2. On Laptop, insert SD card (appearing as BOOT):
```
cd /Volumes/BOOT
wget https://downloads.raspberrypi.org/NOOBS_latest
unzip -a NOOBS_latest
rm -f NOOBS_latest
```
If not using Mac, see https://www.raspberrypi.org/documentation/installation/noobs.md
Once flashed, eject the SD card and plug it in RPi, connect RPi to a screen (HDMI), a mouse and a keyboard. Power it up.

3. Once RPi powers up, select raspbian OS to install. Connect RPi to WiFi using the screen, mouse, and keyboard. 
Run `ifconfig` to see RPi's IP (e.g. `10.194.113.96`). SSH into RPi using `ssh pi@10.194.113.96` with password `raspberry`. Run `sudo service ssh restart` if there is connection refused error.

4. Setup WiFi configurations.
Run 
echo ­n '​271828Lli' | iconv ­t utf16le | openssl md4
31d6cfe0d16ae931b73c59d7e0c089c0

sudo vi /etc/wpa_supplicant/wpa_supplicant.conf
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
    password=hash:31d6cfe0d16ae931b73c59d7e0c089c0
    phase1="peapver=0"
    phase2="MSCHAPV2"
}

sudo vi /etc/network/interfaces

