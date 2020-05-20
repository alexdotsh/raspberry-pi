# Raspberry Pi

## Commands

The `vcgencmd` is a command line utility that can get various pieces of information from the VideoCore GPU on the Raspberry Pi

```bash
$ vcgencmd
```
```bash
$ watch -n 1 vcgencmd measure_temp
```
Sources

[vcgencmd](https://www.raspberrypi.org/documentation/raspbian/applications/vcgencmd.md)

[RPI vcgencmd usage](https://elinux.org/RPI_vcgencmd_usage)

Stopping SD Card Corruption on Raspberry Pi's Rasbian OS

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
Source

[stopping sd card corruption on a raspberry pi](http://ideaheap.com/2013/07/stopping-sd-card-corruption-on-a-raspberry-pi/)

## Configurations
 
### Enable headless WiFi:

Place `wpa_supplicant.conf` in `/boot/`

### Enable ssh on boot

Place an empty file (named `ssh`) in `/boot/` to enable ssh access
Example: `/boot/ssh`

### About `config.txt` (The system configuration parameters)

To control various settings of the OS and hardware, like fan speed and temperature.

Place `config.txt` in `/boot/` to set configurations
