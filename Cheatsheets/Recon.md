## Passive Information Gathering
### Whois Enumeration
```bash
# Basic whois
whois megacorpone.com -h 192.168.50.251
# Whois reverse lookup
whois 38.100.193.70 -h 192.168.50.251
```
## Active Information Gathering
[[NOTES#Active Information Gathering]]
### DNS Enumeration
TAGS: #ActiveRecon #DNS #Enumeration 
```bash
## linux

# Find A host record
host www.megacorpone.com
# Find MX host records
host -t mx megacorpone.com
# Find TXT host records
host -t txt megacorpone.com
# Search for invvalid host
host idontexist.megacorpone.com

# list.txt
cat list.txt
www
ftp
mail
owa
proxy
router

# Oneliner DNS lookup
for ip in $(cat list.txt); do host $ip.megacorpone.com; done
# Reverse DNS bruteforce
for ip in $(seq 200 254); do host 51.222.169.$ip; done | grep -v "not found"

# DNSRecon
dnsrecon -d megacorpone.com -t std
# DNSRecon Bruteforce
dnsrecon -d megacorpone.com -D ~/list.txt -t brt

# DNSenum
dnsenum megacorpone.com
```

```powershell
## windows
# nslookup
nslookup mail.megacorptwo.com
# more specific
nslookup -type=TXT info.megacorptwo.com 192.168.50.151
```
