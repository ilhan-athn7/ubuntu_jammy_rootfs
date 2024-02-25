# ubuntu_jammy_rootfs
My own minimal ubuntu setup with kde-plasma desktop.

# Setting up Ubuntu Jammy using debootstrap and arch-install-scripts
```
#!/bin/bash
#mkfs.ext4 /dev/nvme0n1p4
#mount /dev/nvme0n1p4 /mnt

debootstrap --variant=minbase \
--exclude="snapd,cloud-init,landscape-common,popularity-contest,ubuntu-advantage-tools,unattended-upgrades,netplan,networkd" \
jammy /mnt http://archive.ubuntu.com/ubuntu

echo ubuntu >/mnt/etc/hostname
echo 127.0.0.1 >>/mnt/etc/hosts
cat <<EOF >"/mnt/etc/apt/sources.list"
deb http://archive.ubuntu.com/ubuntu jammy main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu jammy main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu jammy-updates main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu jammy-updates main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu jammy-security main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu jammy-security main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu jammy-backports main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu jammy-backports main restricted universe multiverse

deb http://archive.canonical.com/ubuntu jammy partner
deb-src http://archive.canonical.com/ubuntu jammy partner
EOF

cat <<EOF >"/mnt/etc/apt/preferences.d/ignored-packages"
Package: snapd cloud-init landscape-common popularity-contest ubuntu-advantage-tools grub* unattended-upgrades apport netplan networkd lilo refind systemd-boot-efi systemd-boot plasma-discover* khelpcenter kinfocenter partitionmanager kdeconnect kwalletmanager plasma-systemmonitor
Pin: Release * 
Pin-Priority: -10
EOF

mkdir -p /mnt/etc/NetworkManager/conf.d/
cat <<EOF >"/mnt/etc/NetworkManager/conf.d/10-globally-managed-devices.conf"
[keyfile]
unmanaged-devices=none
EOF

cat <<EOF >"/mnt/root/setup"
apt update
apt dist-upgrade
apt install --no-install-recommends nano dialog linux-generic-hwe-22.04 linux-firmware initramfs-tools
apt install nano git adb fastboot python-is-python3 sddm sddm-theme-breeze fonts-noto-color-emoji konsole dolphin ark kde-spectacle kwrite plasma-nm pulseaudio-module-bluetooth gstreamer1.0-plugins-bad tzdata locales keyboard-configuration

export PATH=\$PATH:/usr/sbin

dpkg-reconfigure tzdata
dpkg-reconfigure locales
dpkg-reconfigure keyboard-configuration

systemctl set-default graphical.target
systemctl enable sddm
systemctl enable NetworkManager


useradd -mG sudo -s /bin/bash ilhan
passwd
passwd ilhan
EOF

echo "Now run 'bash /root/setup' to finalize setup."
arch-chroot /mnt
```
