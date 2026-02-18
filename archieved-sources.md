Sometimes the rescue-mode-os is too outdated to run `apt update` or `apt install packages` due to outdated signature, so change the apt source to the archieved edition (not the newest, as its architecture may not be applicable ).  
However, many components are missing which can cause scripts to fail. So, just for temporary rescue-mode-use.
```sh
cat /etc/debian_version
```
For debian 9, replace to the archieved source, then allow unsecure:
```
cat > /etc/apt/sources.list << 'EOF'
deb http://archive.debian.org/debian/ stretch main contrib non-free
deb http://archive.debian.org/debian-security/ stretch/updates main contrib non-free
EOF

echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid
echo 'APT::Get::AllowUnauthenticated "true";' > /etc/apt/apt.conf.d/99allow-unauth

apt update -o Acquire::AllowInsecureRepositories=true -o APT::Get::AllowUnauthenticated=true
apt install wget -y

```
