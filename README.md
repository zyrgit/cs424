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


## 4. Setup networking configurations.

Suppose you are interacting with RPi via a screen, mouse, and a keyboard. Open a terminal on RPi. 

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

You also need to use Ethernet on RPi to communicate with your laptop. Because once you get rid of the screen for RPi, you have no way of knowing its IP. You need to SSH into RPi from your laptop via Ethernet (or USB-Ethernet adaptor) to know RPi's IP on wireless interface. So connect your laptop with RPi via Ethernet cable now.

If you use Ubuntu, run `ifconfig eth0 up` to bring up Ethernet interface `eth?`, if you use Mac, plug in cable and `en?` should be up automatically. Find the IP of your laptop Ethernet interface using `ifconfig`. On Ubuntu it may be `192.168.0.1`, on Mac it may be `169.254.109.86`, something like that. 

Your RPi and laptop needs to be on the same subnet. if you run `ifconfig` on your laptop, you see the netmask on your laptop, may be `netmask 0xffff0000 broadcast 169.254.255.255` on Mac or `netmask 0xffffff00 broadcast 192.168.0.255` on Ubuntu. If broadcast is `?.?.255.255` it means the lowest two bytes are masked, if broadcast is `?.?.?.255` it means the lowest one byte is masked. 

If on your laptop, it's `netmask 0xffff0000 broadcast 169.254.255.255` then the netmask on your RPi should be `netmask 255.255.0.0`. If it's `netmask 0xffffff00 broadcast 192.168.0.255` then the netmask on your RPi should be `netmask 255.255.255.0`.

On RPi, run `ifconfig -a` and see the interface name for Ethernet, it may not be `eth0`, it can be something like `enxb827eb04aeaf`. Choose a different IP for your RPi's `eth?`, like `192.168.0.123` or `169.254.109.123`, only the last byte is different so they are on the same local network. 

On RPi, run `sudo nano /etc/network/interfaces` and change to the following:
```
source-­directory /etc/network/interfaces.d

auto lo
iface lo inet loopback

auto enxb827eb04aeaf
iface enxb827eb04aeaf inet static
    address 169.254.109.123
    netmask 255.255.0.0

allow-hotplug wlan0
iface wlan0 inet dhcp
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```
Note that the address for enxb827eb04aeaf is what you've chosen for RPi, and the netmask is either `255.255.0.0` or `255.255.255.0`. Save and exit nano.

Restart by running `sudo shutdown -r now`. If everything goes well, on RPi, run `sudo apt-get update`, `sudo apt-get upgrade -y`, restart if necessary. 

After restart, RPi's Ethernet should be automatically up. On laptop, SSH into RPi using `ssh pi@169.254.109.123`. On RPi you can check `ifconfig` and see the IPs:

```
pi@robotpi1:~ $ ifconfig
enxb827eb04aeaf: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 169.254.109.123  netmask 255.255.255.0  broadcast 169.254.109.255
        inet6 fe80::ba27:ebff:fe04:aeaf  prefixlen 64  scopeid 0x20<link>
        ether b8:27:eb:04:ae:af  txqueuelen 1000  (Ethernet)
        RX packets 42  bytes 13151 (12.8 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 34  bytes 4782 (4.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1  (Local Loopback)
        RX packets 2  bytes 78 (78.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2  bytes 78 (78.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.194.102.108  netmask 255.252.0.0  broadcast 10.195.255.255
        inet6 fe80::ba27:ebff:fe51:fbfa  prefixlen 64  scopeid 0x20<link>
        ether b8:27:eb:51:fb:fa  txqueuelen 1000  (Ethernet)
        RX packets 117  bytes 21303 (20.8 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 97  bytes 16527 (16.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```


## 5. Modify RPi.
Suppose you're group 1, the name of your group should be `robotpi1`, change accordingly.

Change hostname by running `sudo nano /etc/hosts` on RPi and change `1​27.0.1.1 raspberrypi` to `1​27.0.1.1 robotpi1`

On RPi, run `sudo nano /etc/hostname` and change `​raspberrypi​` to ​`robotpi​1`. Restart. 

On RPi, run 
```
sudo touch /etc/network/if-up.d/robotpi
sudo chmod 755 /etc/network/if-up.d/robotpi
sudo nano /etc/network/if-up.d/robotpi
```
Add the following in `/etc/network/if-up.d/robotpi`:
```
#! /bin/sh
curl --data "hostname=`/bin/hostname`&data=`/sbin/ifconfig`" http://apollo3.cs.illinois.edu/robotpi/controller.py/send_heartbeat
```


