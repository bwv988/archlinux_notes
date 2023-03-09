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

## Frequent Commands

### Suspend to disk

```
sudo systemctl hibernate
```

### Suspend to RAM

```
sudo systemctl suspend
```

### Pacman cleanup - Remove orphaned packages

```
sudo pacman -Qtdq | pacman -Rns -
```


