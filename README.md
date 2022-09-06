# Arch install
## A little guide to assist installing arch on hardware

```
wipefs -a /dev/nvme0n1
```

```
cfdisk /dev/nvme0n1
```

```
yes | mkfs.fat /dev/nvme0n1p1 && yes | mkfs.ext4 -L ROOT /dev/nvme0n1p2 && yes| mkfs.ext4 -L HOME /dev/nvme0n1p3 && yes | mkfs.ext4 -L DATA /dev/nvme0n1p4 && fatlabel /dev/nvme0n1p1 BOOT
```

```
mount /dev/nvme0n1p2 /mnt && mkdir /mnt/{boot,home,data,Virtualization} && mount /dev/nvme0n1p1 /mnt/boot && mount /dev/nvme0n1p3 /mnt/home && mount /dev/nvme0n1p4 /mnt/data && mount /dev/nvme1n1 /mnt/Virtualization/
```

```
timedatectl set-ntp true
```

```
reflector --country Germany,Austria --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

```
pacstrap /mnt base base-devel linux linux-firmware linux-headers  grub efibootmgr os-prober networkmanager ntp openssh vim neofetch
```

```
genfstab -U /mnt >> /mnt/etc/fstab
```

```
arch-chroot /mnt
```

```
systemctl enable NetworkManager.service && systemctl enable sshd.service
```

```
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen && echo "KEYMAP=us" >> /etc/vconsole.conf && echo "LANG=en_US.UTF-8" >> /etc/locale.conf && locale-gen && echo "LANG=en_US.UTF-8" >> /etc/locale.conf
```

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub && grub-mkconfig -o /boot/grub/grub.cfg
```

```
passwd
```

```
useradd -m stywen && usermod -G wheel stywen && passwd stywen 
```

```
sed -i "s/#%wheel ALL=(ALL:ALL) ALL/%wheel ALL=(ALL:ALL) ALL/g" /etc/sudoers
```

```
sed -i "s/#[multilib]/[multilib]/g" /etc/pacman.conf
```

```
sed -i "s/#Include = /etc/pacman.d/mirrorlist/Include = /etc/pacman.d/mirrorlist/g" /etc/pacman.conf
```




```
vim 
```

```
pacman -Syyuu
```

```
exit
```

```
umount -R /mnt 
```

```
reboot
```
