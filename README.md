# Raspberry Pi

## Commands

The `vcgencmd` is a command line utility from RaspbianOS, that can get various pieces of information from the VideoCore GPU on the Raspberry Pi.

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

### Enable WiFi on boot (Working)

Edit the `network-config` file in `system-boot` in boot drive, and add your Wi-Fi details.

Example config with IPv4 and IPv6 DHCP enabled:

```bash
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      dhcp4: true
      dhcp6: true
      optional: true
      access-points:
        "SSID":
          password: "PassPhrase"
```

Add the following to the end of the `user-data` file in `system-boot` in boot drive.

```yaml
power_state:
  mode: reboot
```

Power on the PI and let neplan to initialize, after 5mins reboot the PI again and it will connect to the WiFi.

### Update existing WiFi using netplan

Edit the `/etc/netplan/50-cloud-init.yaml` file, and add or change the following config:

```yaml
# Example config with IPv4 and IPv6 DHCP enabled:
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      dhcp4: true
      dhcp6: true
      access-points:
        "SSID":
          password: "PassPhrase"
```

With static IPv4 address:

```yaml
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      dhcp6: true
      addresses: [192.168.0.21/24]
      access-points:
        "SSID":
          password: "PassPhrase"
```

More configuration examples can be found [here](https://netplan.io/examples)

Once done, generate, apply, systemctl daemon-reload, and reboot with the following commands:

- Generate: `sudo netplan --debug generate` (will give you more details in case of issues with the previous command)
- Apply: `sudo netplan --debug apply`
- Reload: `sudo systemctl daemon-reload`
- Reboot: `sudo reboot`

### Default ssh login to Pi (Ubuntu):

- Username: `ubuntu`
- Password: `ubuntu`

```bash
$ ssh ubuntu@<Raspberry Pi’s IP address>
```

#### With ssh key

```bash
$ ssh -i ~/.ssh/id_rsa pi@hostname
```

### Fan temperature

Display Raspberry Pi ARM CPU temperature

```bash
$ cat /sys/class/thermal/thermal_zone0/temp
```

```bash
$ cpu=$(</sys/class/thermal/thermal_zone0/temp)
$ echo "$((cpu/1000)) c"
```

#### Fan control and cooling

The fan config is in `/sys/class/thermal/cooling_device0/`, if you cat `/sys/class/thermal/cooling_device0/type`, it should be "rpi-poe-fan".

Once you've confirmed that, use this udev rule as an example:

```bash
SUBSYSTEM=="thermal"
KERNEL=="thermal_zone0"

# If the temp hits 70c, turn on the fan. Turn it off again when it goes back down to 65.
ATTR{trip_point_0_temp}="70000"
ATTR{trip_point_0_hyst}="2000"
#
# If the temp hits 75c, higher RPM.
ATTR{trip_point_1_temp}="75000"
ATTR{trip_point_1_hyst}="3000"
#
# If the temp hits 80c, higher RPM.
ATTR{trip_point_2_temp}="80000"
ATTR{trip_point_2_hyst}="4000"
#
# If the temp hits 85c, highest RPM.
ATTR{trip_point_3_temp}="85000"
ATTR{trip_point_3_hyst}="5000"
```

Place it in `/etc/udev/rules.d/50-rpi-fan.rules`

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

### OS update

```bash
$ sudo apt update --fix-missing -y && sudo apt dist-upgrade -y && sudo apt autoremove --purge -y
```
