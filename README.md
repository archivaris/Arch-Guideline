# Arch-Guideline
guide to arch instalation

# fdisk /dev/sda
 * g (to create an empty GPT partition table)
 * n
 * 1
 * enter
 * +300M
 * t
 * 1 (For EFI)
 * w


Create boot partition:
# fdisk /dev/sda
* n
* 2
* enter
* +400M
* w


Create LVM partition:
# fdisk /dev/sda
 * n
 * 3
 * enter
 * enter
 * t
 * 3
 * 31
 * w


# mkfs.fat -F32 /dev/sda1


# mkfs.ext2 /dev/sda2


Set up encryption

# cryptsetup luksFormat /dev/sda3
# cryptsetup open --type luks /dev/sda3 lvm



Set up lvm:

# pvcreate --dataalignment 1m /dev/mapper/lvm
# vgcreate volgroup0 /dev/mapper/lvm
# lvcreate -L 30GB volgroup0 -n lv_root
# lvcreate -L 250GB volgroup0 -n lv_home
# modprobe dm_mod
# vgscan
# vgchange -ay



# mkfs.ext4 /dev/volgroup0/lv_root


# mkfs.xfs /dev/volgroup0/lv_home


# mount /dev/volgroup0/lv_root /mnt


# mkdir /mnt/boot


# mkdir /mnt/home


# mount /dev/sda2 /mnt/boot


# mount /dev/volgroup0/lv_home /mnt/home


# pacstrap -i /mnt base


# genfstab -U -p /mnt >> /mnt/etc/fstab


# arch-chroot /mnt

17.# pacman -S base-devel openssh grub efibootmgr dosfstools openssh os-prober mtools linux-headers linux-lts linux-lts-headers

18.If wireless: # pacman -S dialog network-manager-applet networkmanager networkmanager-openvpn wireless_tools wpa_supplicant wpa_actiond

Edit /etc/mkinitcpio.conf and add encrypt lvm2 in between block and filesystems


# mkinitcpio -p linux


# mkinitcpio -p linux-lts


# nano /etc/locale.gen (uncomment en_US.UTF-8)


# locale-gen


Enable root logon via ssh


# systemctl enable sshd.service


# passwd (for setting root password)


Edit /etc/default/grub:
add cryptdevice=<PARTUUID>:volgroup0 to the GRUB_CMDLINE_LINUX_DEFAULT line
If using standard device naming, the option will look like this: cryptdevice=/dev/sda3:volgroup0


# mkdir /boot/EFI


# mount /dev/sda1 /boot/EFI


# grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck


# cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo


# grub-mkconfig -o /boot/grub/grub.cfg


Create swap file:

# fallocate -l 2G /swapfile
# chmod 600 /swapfile
# mkswap /swapfile
# echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab



$ exit


# umount -a


# reboot

Post installation steps:

Fix GNOME app issues: # localectl set-locale LANG="en_US.UTF-8"

Add to fstab:
tmpfs /tmp tmpfs nodev,nosuid,size=2G 0 0

If ssd, add discard to fstab. Example:
UUID=<UUID> / ext4 defaults,noatime,discard 0 2

# useradd -m -y users -s /bin/bash vulgaris
#passwd vulgaris
#visudo
##User privilege specification 
under root ALL=(ALL) ALL type:
vulgaris ALL=(ALL) ALL
press ESC then :wq

#sudo pacman -S xorg-server xorg-server-utils xorg-xinit xorg xorg-apps
#sudo pacman -S lib32-mesa xf86-video-intel vulkan-intel
#sudo pacman -S sddm plasma/ plasma-meta / plasma-desktop  || kde-applications
#systemctl enable sddm
#reboot

Must-have applications:


