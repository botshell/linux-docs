```bash
dd if=/dev/urandom of=/root/luks.key bs=4096 count=1
chmod 0400 /root/luks.key
chattr +i /root/luks.key  # chattr -i /root/luks.key
# if `rm /root/luks.key` then `update-initramfs -u`, grub will display an error
# that non-existing keyfile /cryptroot/keyfiles/sdX3_crypt.key

target="/dev/sda3"
UUID=$(blkid -s UUID -o value "$target")
cryptsetup luksAddKey "$target" /root/luks.key  # Enter any existing passphrase
sed -i "s|\(UUID=$UUID[[:space:]]\+\)none|\1/root/luks.key|" /etc/crypttab
# cat /etc/crypttab  # debug

grep -qxF 'KEYFILE_PATTERN=/root/luks.key' /etc/cryptsetup-initramfs/conf-hook || \
echo 'KEYFILE_PATTERN=/root/luks.key' >> /etc/cryptsetup-initramfs/conf-hook

grep -q '^UMASK=' /etc/initramfs-tools/initramfs.conf && \
sed -i 's/^UMASK=.*/UMASK=0077/' /etc/initramfs-tools/initramfs.conf || \
echo 'UMASK=0077' >> /etc/initramfs-tools/initramfs.conf

update-initramfs -u
# reboot
```
