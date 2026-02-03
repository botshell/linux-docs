# Configure auto decrypt hard drive when booting in local test.
The secret key is exposed in an unencrypted boot partition.
```bash
target="/dev/sda3"

dd if=/dev/urandom of=/root/luks.key bs=4096 count=1
chmod 0400 /root/luks.key
chattr +i /root/luks.key  # chattr -i /root/luks.key
# if `rm /root/luks.key` then `update-initramfs -u`, grub will display an error
# that non-existing keyfile /cryptroot/keyfiles/sdX3_crypt.key

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

```bash
target="/dev/sda3"
cryptsetup luksDump "$target"

cryptsetup luksOpen "$target" test --test-passphrase
cryptsetup luksOpen "$target" test --key-file /root/luks.key --test-passphrase
```

```bash
for i in 0 1 2 3 4 5 6 7; do
  echo "Testing slot $i"
  cryptsetup luksOpen "$target" test \
    --key-file /root/luks.key \
    --key-slot $i \
    --test-passphrase && echo "-> slot $i works"
done

# cryptsetup luksKillSlot "$target" 2  # delete slot 2
```

# Configure auto decrypt hard drive when booting via ssh.
make sure /etc/crypttab config keyfile is `none` rather than `/root/luks.key`
```bash
apt install dropbear-initramfs busybox -y
echo id_rsa.pub_content >> /etc/dropbear/initramfs/authorized_keys
# chmod 700 /etc/dropbear/initramfs
# chmod 600 /etc/dropbear/initramfs/authorized_keys

update-initramfs -u

dropbearkey -y -f /etc/dropbear/initramfs/dropbear_ed25519_host_key  # get the Public key and Public key's Fingerprint: SHA256
# cat /etc/dropbear/initramfs/dropbear_ed25519_host_key.pub  # can also this way to get Public key
# dropbearconvert dropbear openssh /etc/dropbear/initramfs/dropbear_ed25519_host_key dropbear_ed25519_host_key.openssh
# ssh-keygen -lf dropbear_ed25519_host_key.openssh  # can also this way to get Public key's Fingerprint: SHA256
# ssh-keygen -lf /etc/dropbear/initramfs/dropbear_ed25519_host_key.pub  # can also this way to get Public key's Fingerprint: SHA256




# reboot

# disabled this way.
# apt purge dropbear-initramfs
# update-initramfs -u
```

```bash
# the Public key's Fingerprint: SHA256 calculate as
apt install xxd -y
awk '{print $2}' /etc/dropbear/initramfs/dropbear_ed25519_host_key.pub \
  | base64 -d \
  | sha256sum \
  | awk '{print $1}' \
  | xxd -r -p \
  | base64

```

ssh login
```powershell
ssh -i "\path\to\your\id_rsa" -p 22 root@ip
```

```bash
cryptroot-unlock  # Enter any existing passphrase
```
