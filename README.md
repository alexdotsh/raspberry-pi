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

#### Enable WiFi (Working)

Edit the network-config file in (`system-boot` root drive) and add your Wi-Fi credentials.
An example is already included in the file, it can simply be adapt it.

Use the networkd in renderer as `renderer: networkd`

```bash
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

Add the following to the end of the `user-data`

```yaml
power_state:
  mode: reboot
```

##### Using `netplan` to setup the network interfaces and change `existing wifi`

Edit the file `/etc/netplan/your-config-file.yaml` and add or change the following

Example code

```bash
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

Once done, test, generate and apply the config that way:

- Testing: `sudo netplan --debug try` (continue even if successful)
- Generate: `sudo netplan --debug generate` (will give you more details in case of issues with the previous command)
- Apply: `sudo netplan --debug apply`
- Reload: `systemctl daemon-reload`
- Reboot: `reboot`

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

#### Fan temprature

```bash
cat /sys/class/thermal/thermal_zone0/temp
```

#### Fan control and cooling

The fan config is in `/sys/class/thermal/cooling_device0/`, if you cat `/sys/class/thermal/cooling_device0/type`, it should be "rpi-poe-fan".

Once you've confirmed that, use this udev rule as an example:

```bash
SUBSYSTEM=="thermal"
KERNEL=="thermal_zone0"

# If the temp hits 75c, turn on the fan. Turn it off again when it goes back down to 70.
ATTR{trip_point_0_temp}="75000"
ATTR{trip_point_0_hyst}="5000"
#
# If the temp hits 78c, higher RPM.
ATTR{trip_point_1_temp}="78000"
ATTR{trip_point_1_hyst}="2000"
#
# If the temp hits 80c, higher RPM.
ATTR{trip_point_2_temp}="80000"
ATTR{trip_point_2_hyst}="2000"
#
# If the temp hits 81c, highest RPM.
ATTR{trip_point_3_temp}="81000"
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
