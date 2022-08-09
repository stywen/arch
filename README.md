# install arch
## setup disk

### wipe disk
```
wipefs -a /dev/nvme0n1
```

### create new partitions
```
cfdisk /dev/nvme0n1
```

- partition 1 boot 128M
- parititon 2 root 15G
- parittion 3 home 25G
- partition 4 data rest of space

### create filesystems
```
mkfs.fat /dev/nvme0n1p1
```
```
mkfs.ext4 -L ROOT /dev/nvme0n1p2
```

```
mkfs.ext4 -L HOME /dev/nvme0n1p3
```

```
mkfs.ext4 -L DATA /dev/nvme0n1p4
```

```
fatlabel /dev/nvme0n1p1 BOOT
```

### mount partitions
```
mount /dev/nvme0n1p2 /mnt
```
```
mkdir /mnt/{boot,home,data,GameLibrary}
```

```
mount /dev/nvme0n1p1 /mnt/boot
```

```
mount /dev/nvme0n1p3 /mnt/home
```

```
mount /dev/nvme0n1p4 /mnt/data
```

```
mount /dev/sda1 /mnt/GameLibrary
```

## system configuration

### update time
```
timedatectl set-ntp true
```

### install base system
```
pacstrap /mnt base linux linux-firmware networkmanager grub efibootmgr os-prober zsh vim base-devel linux-headers mtools tree openssh ntp pulseaudio pavucontrol git
```

```
pacstrap /mnt amd-ucode
```

### create fstab

```
genfstab -U /mnt >> /mnt/etc/fstab
```

### change into systems shell
```
arch-chroot /mnt
```

#### update time
```
timedatectl set-timezone Europe/Vienna && hwclock --systohc
```

### set locale and language
```
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen && echo "KEYMAP=us" >> /etc/vconsole.conf && echo "LANG=en_US.UTF-8" >> /etc/locale.conf && locale-gen && echo "LANG=en_US.UTF-8" >> /etc/locale.conf
```

### install bootloader
```
pacman -S grub os-prober efibootmgr
```

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub   
```

```
grub-mkconfig -o /boot/grub/grub.cfg
```

```
grub-install --recheck /dev/sda  # for BIOS systems (Qemu VMs)
```

### set hostname
```
echo "xxyyzz" >> /etc/hostname
```

### setup user
```
passwd
```

```
useradd -m stywen && usermod -G wheel stywen && passwd stywen 
```

### enable services

```
systemctl enable NetworkManager.service && systemctl enable sshd.service
```

### make new user sudo

```
EDITOR=vim visudo
```

### update system
```
pacman -Syyuu
```

### reboot
```
exit && umount -R /mnt ; reboot
```

### enable [multilib]
```
sudo vim /etc/pacman.conf
```

### install and setup xorg

```
sudo pacman -S xorg xorg-xinit i3 dmenu alacritty
```

```
vim ~/.xinitrc
```

```
# add the following lines to .xinitrc
#!/bin/sh
#
# ~/.xinitrc
#
# Executed by startx (run your window manager from here)

if [ -d /etc/X11/xinit/xinitrc.d ]; then
  for f in /etc/X11/xinit/xinitrc.d/*; do
    [ -x "$f" ] && . "$f"
  done
  unset f
fi
exec i3
```

### install amd drivers and make gpu optimizations
```
sudo pacman -S lib32-mesa vulkan-radeon lib32-vulkan-radeon vulkan-icd-loader lib32-vulkan-icd-loader xf86-video-amdgpu  libva-mesa-driver mesa-vdpau -y
```

```
sudo echo "RADV_PERFTEST=aco" >> /etc/environment
```

#### disable screen tearing
```
sudo vim /etc/X11/xorg.conf.d/20-amd-gpu.conf
```

```
Section "Device"
    Identifier  "AMD Graphics"
    Driver      "amdgpu"
    Option      "TearFree"  "true"
EndSection
```

#### set default resolution
```
sudo -i
```

```
Modes     "5120x1440"
```

```
X -configure; cp xorg.conf.new /etc/X11/xorg.conf.d/xorg.conf && vim /etc/X11/xorg.conf.d/xorg.conf
```

```
sudo vim /etc/security/limits.conf
```

```
stywen hard nofile 524288
```

```
exit
```

```
startx
```

```
reboot
```

```
echo "gaps inner 10" >> ~/.config/i3/config
echo "gaps outer 5" >> ~/.config/i3/config
echo "default_border pixel 3">> ~/.config/i3/config
```














### install yay and brave
```
cd /opt && sudo git clone https://aur.archlinux.org/yay-git.git && sudo chown -R $USER:$USER ./yay-git && cd yay-git && makepkg -si
```

```
yay -S brave-bin
```









