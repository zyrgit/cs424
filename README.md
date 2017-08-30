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

On RPi, run `ifconfig` to see RPi's IP (e.g. `10.194.113.96`). Run `sudo service ssh restart` and `ssh-keygen -t rsa` on RPi. If there is connection refused error, run `sudo apt-get purge openssh-server` and `sudo apt-get install -y openssh-server`.

On laptop, SSH into RPi using `ssh pi@10.194.113.96` with password `raspberry`. 

On RPi, run `echo -n 'Your_UIUC_password' | iconv -t UTF-16LE | openssl md4` and copy the string after `(stdin)= `, this is to replace the password field in wpa_supplicant.conf. Run `history -cw` to clear cmd history.

On RPi, run `sudo nano /etc/wpa_supplicant/wpa_supplicant.conf`, change to the following:
```
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="Your_home_wifi"
    psk="Your_password"
    key_mgmt=WPA-PSK
}

network={
    ssid="IllinoisNet"
    key_mgmt=WPA-EAP
    identity="Your_netid"
    password=hash:string_from_previous_cmd
}
```
Use `Ctrl-o`, `Enter`, `Ctrl-x` to save and exit nano.

On RPi, run `sudo nano /etc/network/interfaces` and change to the following:
```
source-­directory /etc/network/interfaces.d

auto lo
iface lo inet loopback

allow-hotplug wlan0
iface wlan0 inet dhcp
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```
Save and exit nano.

Restart by running `sudo shutdown -r now`. If everything goes well, on RPi, run `sudo apt-get update`, `sudo apt-get upgrade -y`, restart if necessary. 


## 5. Modify RPi.
Suppose you're group 1, the name of your group should be `robotpi1`, change accordingly.

Change hostname by running `sudo nano /etc/hosts` on RPi and change `1​27.0.1.1 raspberrypi` to `1​27.0.1.1 robotpi1`

On RPi, run `sudo nano /etc/hostname` and change `​raspberrypi​` to ​`robotpi​1`.





