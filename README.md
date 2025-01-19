# Arch Linux installation notes

Some random notes I am taking while installing / maintaining Arch Linux on a Lenovo T14s.

## Basic setup steps

### Partitioning

It's just horribly painful. 

### UEFI

Boot directly into the kernel, no GRUB etc. Took a while to remove the Windows bootloader and recovery partitions, too.

### Dropbox

So painful.

Need to enable Gnome Shell Extensions and then install the app indicator to get the Dropbox "tray" icon.

```
gsettings set org.gnome.shell disable-user-extensions false
sudo pacman -Su gnome-shell-extension-appindicator
```

### Getting external sound to work

I had to install the `sof-firmware` package to get the external speakers to work on a T14s.

## Frequent Commands

### Suspend to disk

```
sudo systemctl hibernate
```

### Suspend to RAM

```
sudo systemctl suspend
```

### Full system upgrade

```
sudo pacman -Suy
```

### Pacman cleanup - Remove orphaned packages

```
sudo pacman -Qtdq | pacman -Rns -
```

## AUR packages

This is simple. Just `git clone` the AUR package, then `cd` into the directory and run:

```
makepkg
sudo pacman -U <package file>
```

## CPU Frequency scaling

### Monitor CPU frequency in real time

`watch cat /sys/devices/system/cpu/cpu[0-9]*/cpufreq/scaling_cur_freq`

### Intel performance and energy bias hint

#### Watch
`cat /sys/devices/system/cpu/cpu*/power/energy_perf_bias`

#### Set
`echo epb | tee /sys/devices/system/cpu/cpu*/power/energy_perf_bias`


## Control Fan Speed

### Enable kernel driver option:

`echo 'options thinkpad_acpi fan_control=1' | sudo tee -a /etc/modprobe.d/thinkpad_acpi.conf`

### Check success:

`cat /proc/acpi/ibm/fan`

### Set levels


`echo 'level 4' | sudo tee /proc/acpi/ibm/fan`
`echo 'level auto' | sudo tee /proc/acpi/ibm/fan`

## Set battery charging threshold 

Below sets the charging threshold to 80%:

`echo 80 | sudo tee  /sys/class/power_supply/BAT0/charge_control_end_threshold`
