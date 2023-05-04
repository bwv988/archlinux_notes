# Arch Linux installation notes

## Basic setup steps

### Partitioning

It's just horribly painful. 

### UEFI

Boot directly into the kernel, no GRUB etc.


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

## CPU Frequency scaling

### Monitor CPU frequency in real time

`watch cat /sys/devices/system/cpu/cpu[0-9]*/cpufreq/scaling_cur_freq`

### Intel performance and energy bias hint

#### Watch
`cat /sys/devices/system/cpu/cpu*/power/energy_perf_bias`

#### Set
`echo epb | tee /sys/devices/system/cpu/cpu*/power/energy_perf_bias`


