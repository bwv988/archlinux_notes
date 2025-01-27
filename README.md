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


## Creating bootable Arch Linux on a USB drive

```bash
# Disk partitioning.

sudo sgdisk -o -n 1:0:+10M -t 1:EF02 -n 2:0:+500M -t 2:EF00 -n 3:0:0 -t 3:8304 -g /dev/sda

sudo mkfs.fat -F32 /dev/sda2
sudo mkfs.ext4 /dev/sda3

# Prepare backup.
sudo mkdir -p /mnt/archflash/rootfs
sudo mkdir -p /mnt/archflash/rootfs/boot
sudo mount /dev/sda3 /mnt/archflash/rootfs/
sudo mount /dev/sda2 /mnt/archflash/rootfs/boot

# Full backup.
sudo rsync -aAXHS --info=progress2  --exclude='/dev/*' --exclude='/proc/*' --exclude='/sys/*' --exclude='/tmp/*' --exclude='/run/*' --exclude='/mnt/*' --exclude='/boot/*' --exclude='/media/*' --exclude='/lost+found/' / /mnt/archflash/rootfs/

sudo rsync -aAXHS --info=progress2  --exclude='/dev/*' --exclude='/proc/*' --exclude='/sys/*' --exclude='/tmp/*' --exclude='/run/*' --exclude='/mnt/*' --exclude='/media/*' --exclude='/lost+found/' /boot /mnt/archflash/rootfs/boot/

sudo sh -c "genfstab /mnt/archflash/rootfs/ | head -n -4 > /mnt/archflash/rootfs/etc/fstab"
sudo sh -c "echo 'cygnus-clone' > /mnt/archflash/rootfs/etc/hostname"

sudo arch-chroot /mnt/archflash/rootfs/

rm /etc/machine-id 

systemd-machine-id-setup

export GRUB_MODULES="all_video
	boot
	btrfs
	cat
	chain
	configfile
	echo
	efifwsetup
	efinet
	ext2
	fat
	font
	gettext
	gfxmenu
	gfxterm
	gfxterm_background
	gzio
	halt
	help
	hfsplus
	iso9660
	jpeg
	keystatus
	loadenv
	loopback
	linux
	ls
	lsefi
	lsefimmap
	lsefisystab
	lssal
	memdisk
	minicmd
	normal
    ntfs	
	part_msdos
	part_gpt
	password_pbkdf2
	peimage
	png
	probe
	reboot
	regexp
	search
	search_fs_uuid
	search_fs_file
	search_label
	serial
	sleep
    smbios 
	squash4
	test
	tpm
	true
	video
	xfs
	zfs
	zfscrypt
	zfsinfo
    cpuid
	play
"

grub-install --target=x86_64-efi --efi-directory /boot --recheck --removable
grub-mkconfig -o /boot/grub/grub.cfg 

```

### Links

* https://mags.zone/help/arch-usb.html
* https://wiki.archlinux.org/title/Install_Arch_Linux_on_a_removable_medium

