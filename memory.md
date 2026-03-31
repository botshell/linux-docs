Display amount of free and used memory in the system
```sh
free -h
```

Add swap memory when necessary
> Create a swapfile
```sh
dd if=/dev/zero of=/swapfile bs=128M count=8
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile  # temporarily swapon, auto swapoff when reboot
```
> Create a swap partition
```
mkswap /dev/vdX
swapon /dev/vdX
```
Auto enabled when boot.
```
blkid /dev/vdaX  # record uuid
nano /etc/fstab
```
Add the recording in the last line:
```
UUID=xxxx-xxxx-xxxx none swap sw 0 0
```

Check if enabled
```sh
swapon -a
swapon --show
```

Swapon permanently when reboot
```
grep -q '^/swapfile ' /etc/fstab || echo '/swapfile none swap sw 0 0' >> /etc/fstab
swapon -a  # make all devices marked as "swap" in /etc/fstab available to check errors after configured
```
