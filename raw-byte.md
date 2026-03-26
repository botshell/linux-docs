```bash
xxd -l 512 debian-13.2.0-amd64-netinst.iso
hexdump -C -n 512 debian-13.2.0-amd64-netinst.iso
od -Ax -tx1 -v -N 512 debian-13.2.0-amd64-netinst.iso
dd if=debian-13.2.0-amd64-netinst.iso bs=1 skip=$((16*2048 - 64)) count=$((2048 + 128)) 2>/dev/null | hexdump -C
dd if=/dev/sdb bs=1 skip=512 count=1538 2>/dev/null
dd if=/dev/sdb bs=512 skip=1 count=3 2>/dev/null
sudo dd if=/dev/sdb bs=4096 count=1 2>/dev/null | xxd -p -c 16 > see.txt
```

diskpartition 

80 00 01 00 00 3f e0 0f 00 00 00 00 00 80 18 00

00 fe ff ff ef fe ff ff 20 10 00 00 20 1c 00 00
