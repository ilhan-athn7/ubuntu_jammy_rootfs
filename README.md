# ubuntu_jammy_rootfs
My own ubuntu setup with kde-plasma desktop and build essentials for building android roms.

# Setting up Ubuntu Jammy using debootstrap and arch-install-scripts
```
mkfs.ext4 /dev/[devnode]
mount /dev/[devnode] /mnt

debootstrap --variant=minbase \
--exclude="snapd,cloud-init,landscape-common,popularity-contest,ubuntu-advantage-tools,unattended-upgrades,netplan,networkd" \
jammy /mnt http://archive.ubuntu.com/ubuntu

echo ubuntu >/mnt/etc/localhost
echo 127.0.0.1 >>/mnt/etc/hosts
echo <<EOF >/mnt/etc/apt/sources.list
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

echo << EOF >/mnt/etc/apt/preferences.d/ignored-packages
Package: snapd cloud-init landscape-common popularity-contest ubuntu-advantage-tools grub* unattended-upgrades apport netplan networkd lilo refind systemd-boot-efi systemd-boot
Pin: Release * 
Pin-Priority: -10
EOF

mkdir -p /mnt/etc/NetworkManager/conf.d/
cat << EOF > /mnt/etc/NetworkManager/conf.d/10-globally-managed-devices.conf
[keyfile]
unmanaged-devices=none
EOF

echo <<EOF >/mnt/root/setup
apt update
apt dist-upgrade
apt install --no-install-recommends \
nano linux-generic-hwe-22.04 linux-firmware initramfs-tools
apt install nano git adb fastboot python-is-python3 sddm sddm-theme-breeze fonts-noto-color-emoji konsole dolphin ark kde-spectactle kwrite plasma-nm pulseaudio-module-bluetooth gstreamer1.0-plugins-bad

export PATH=\$PATH:/usr/sbin

dpkg-reconfigure tzdata
dpkg-reconfigure locales
dpkg-reconfigure keyboard-configuration

systemctl set-default graphical.target
systemctl enable sddm
systemctl enable networkmanager


useradd -mG sudo ilhan
passwd
passwd ilhan
EOF

echo "Now run 'bash /root/setup' to finalize setup."
arch-chroot /mnt
```

# RootFS tarball
- [mega.nz](https://mega.nz/file/oU0xHTgA#qEfBcKBus-j--gxNLH4u89iYVOdD1l4td-KG7L2KJ94)
