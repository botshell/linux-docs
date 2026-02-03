```bash
mkdir -p ~/.ssh
echo id_rsa.pub_content >> ~/.ssh/authorized_keys
```

Generate id_rsa.pub from id_rsa in powershell
```powershell
ssh-keygen -y -f "\path\to\your\id_rsa" > "\path\to\your\id_rsa.pub"
# cat "\path\to\your\id_rsa.pub"  # check if rsa.pub generated
```
Check
```bash
ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub  # ED25519 key fingerprint, should be same as infromed when first login
# same as run `ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key`

# they should be same as %userprofile%/.ssh/known_hosts
cat /etc/ssh/ssh_host_ed25519_key.pub
cat /etc/ssh/ssh_host_rsa_key.pub
cat /etc/ssh/ssh_host_ecdsa_key.pub
```

the Public key's Fingerprint: SHA256 calculate as
```bash
# apt install xxd -y
awk '{print $2}' /etc/ssh/ssh_host_ed25519_key.pub \
  | base64 -d \
  | sha256sum \
  | awk '{print $1}' \
  | xxd -r -p \
  | base64

```

login
```powershell
ssh -i "\path\to\your\id_rsa" -p port username@ip
# ssh-keygen -R ip  # delete old records when changed
# ssh -vvv -i "\path\to\your\id_rsa" -p port username@ip  # debug mode
```
