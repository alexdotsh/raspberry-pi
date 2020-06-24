# Raspberry Pi

## Commands

The `vcgencmd` is a command line utility that can get various pieces of information from the VideoCore GPU on the Raspberry Pi

```bash
$ vcgencmd
```
```bash
$ watch -n 1 vcgencmd measure_temp
```

Sources:

* [vcgencmd](https://www.raspberrypi.org/documentation/raspbian/applications/vcgencmd.md)
* [RPI vcgencmd usage](https://elinux.org/RPI_vcgencmd_usage)

#### Stopping SD Card Corruption on Raspberry Pi's Rasbian OS

Disable and remove swapfile
```bash
$ dphys-swapfile swapoff
$ dphys-swapfile uninstall
$ update-rc.d dphys-swapfile remove
$ apt purge dphys-swapfile
```

Set up tmpfs mounts
```bash
$ vim /etc/fstab
# Add the following line
none        /var/log        tmpfs   size=1M,noatime         00
```

Source: 
* [stopping sd card corruption on a raspberry pi](http://ideaheap.com/2013/07/stopping-sd-card-corruption-on-a-raspberry-pi/)

## Configurations (RaspbianOS)
 
#### Enable WiFi

Place `wpa_supplicant.conf` in `/boot/`

#### Enable ssh on boot

Place an empty file (named `ssh`) in `/boot/` to enable ssh access.

Example: `/boot/ssh`

#### Default ssh login to Pi (Raspbian):

- Username: `pi`
- Password: `raspberry`

```bash
$ ssh pi@<Raspberry Pi’s IP address>
```

#### With ssh key

```bash
$ ssh -i ~/.ssh/id_rsa pi@hostname
```

#### About `config.txt` (The system configuration parameters)

To control various settings of the OS and hardware, like fan speed and temperature.

Place `config.txt` in `/boot/` to set configurations

## Configurations (Ubuntu)

#### Enable WiFi

Edit the network-config file in (`system-boot` root drive) and add your Wi-Fi credentials.
An example is already included in the file, it can simply be adapt it.

#### Default ssh login to Pi (Ubuntu):

- Username: `ubuntu`
- Password: `ubuntu`

```bash
$ ssh ubuntu@<Raspberry Pi’s IP address>
```

#### With ssh key

```bash
$ ssh -i ~/.ssh/id_rsa pi@hostname
```

## Discover the IP of your PI

ping

```bash
$ ping raspberrypi.local
```

nmap

```bash
$ nmap -sn 192.168.1.0/24

$ nmap -sn 192.168.1.0/24 | grep -B 2 B8:27:EB

$ arp -na | grep -i "b8:27:eb"

# Raspberry Pi 4
$ arp -na | grep -i "dc:a6:32"
```
