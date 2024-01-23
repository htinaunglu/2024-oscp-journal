## Passive Information Gathering
### Whois Enumeration
```bash
# Basic whois
whois megacorpone.com -h 192.168.50.251
# Whois reverse lookup
whois 38.100.193.70 -h 192.168.50.251
```
## Active Information Gathering
[[Notes/Information Gathering#Active Information Gathering]]
### DNS Enumeration
TAGS: #ActiveRecon #DNS #Enumeration 
```bash
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
# nslookup
nslookup mail.megacorptwo.com
# more specific
nslookup -type=TXT info.megacorptwo.com 192.168.50.151
```
---
## Port Scanning
TAGS: #portscan #nmap #ncat
```bash
# netcat TCP port scan
nc -nvv -w 1 -z 192.168.50.152 3388-3390
# netcat UDP port scan
nc -nv -u -z -w 1 192.168.50.149 120-123

# configure iptables rules for scan
sudo iptables -I INPUT 1 -s 192.168.50.149 -j ACCEPT
sudo iptables -I OUTPUT 1 -d 192.168.50.149 -j ACCEPT
sudo iptables -Z
# -I insert new rule | INPUT chains, OUTPUT chains
# -s source ip addr
# -d destination ip addr
# -j accept/deny the traffic
# -Z zero the packet and byte counters in al chains

# basic nmap scan | 1000 most popular TCP ports
nmap 192.168.50.149
# iptable stats check for our nmap scan
sudo iptables -vn -L
# all TCP ports
nmap -p 1-65535 192.168.50.149
# SYN/Stealth scan
sudo nmap -sS 192.168.50.149
# TCP connect scan
nmap -sT 192.168.50.149
# UDP scan
sudo nmap -sU 192.168.50.149
# combined SYN & UDP scans
sudo nmap -sU -sS 192.168.50.149
# network/ping sweep
nmap -sn 192.168.50.1-253
# greppable output
nmap -v -sn 192.168.50.1-253 -oG ping-sweep.txt
# grep the results "Up"
grep Up ping-sweep.txt | cut -d " " -f 2
# scan webserver ports
nmap -p 80 192.168.50.1-253 -oG web-sweep.txt
# grep the results "open"
grep open web-sweep.txt | cut -d" " -f2
# Top 20 port scans for all ip addrs
nmap -sT -A --top-ports=20 192.168.50.1-253 -oG top-port-sweep.txt
# nmap-services file
cat /usr/share/nmap/nmap-services
# os fingerprinting
sudo nmap -O 192.168.50.14 --osscan-guess
# banner grabbing service enumeration
nmap -sT -A 192.168.50.14
# using NSE(nmap-script-engine) for os-fingerprinting
nmap --script http-headers 192.168.50.6
# using help on NSE scripts
nmap --script-help http-headers
```

```powershell
# port scanning SMB via powershell
Test-NetConnection -Port 445 192.168.50.151
# automatic powershell port scan
1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("192.168.50.151", $_)) "TCP port $_ is open"} 2>$null
```

## SMB Enumeration
TAGS: #smb 
```bash
# nmap scan for NetBIOS service
nmap -v -p 139,445 -oG smb.txt 192.168.50.1-254

# nbt-scan to collect additional NetBIOS info
sudo nbtscan -r 192.168.50.0/24

# NSE scripts
ls -1 /usr/share/nmap/scripts/smb*

# using one SMB script from NSE
nmap -v -p 139,445 --script smb-os-discovery 192.168.50.152
```

```powershell
# net view to list remote shares
net view \\dc01 /all
```

- *The SMB discovery script works only if SMBv1 is enabled on the target, which is not the default case on modern versions of Windows. However, plenty of legacy systems are still running SMBv1, and we have enabled this specific version on the Windows host to simulate such a scenario.*
---
## SMTP Enumeration
TAGS: #SMTP #script #telnet
```bash
# use VRFY and EXPN to get more info about SMTP
nc -nv 192.168.50.8 25
VRFY root
VRFY idontexist
```

```python
#!/usr/bin/python
## SMTP user enumeration python script

import socket
import sys

if len(sys.argv) != 3:
        print("Usage: vrfy.py <username> <target_ip>")
        sys.exit(0)

# Create a Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect to the Server
ip = sys.argv[2]
connect = s.connect((ip,25))

# Receive the banner
banner = s.recv(1024)

print(banner)

# VRFY a user
user = (sys.argv[1]).encode()
s.send(b'VRFY ' + user + b'\r\n')
result = s.recv(1024)

print(result)

# Close the socket
s.close()
```

```bash
# running the above script to check the user "root"
python3 smtp.py root 192.168.50.8
# running the above script to check the user "johndoe"
python3 smtp.py johndoe 192.168.50.8
```

```powershell
# powershell way of scanning SMTP
Test-NetConnection -Port 25 192.168.50.8
# if want more interavtive use Telnet
# install and enable telnet client
dism /online /Enable-Feature /FeatureName:TelnetClient
# connect with telnet to interact with SMTP
telnet 192.168.50.8 25
```
---
## SNMP Enumeration
TAGS: #SNMP #MIB

| MIB values | Windows SNMP parameters |
| ---- | ---- |
| 1.3.6.1.2.1.25.1.6.0 | System Processes |
| 1.3.6.1.2.1.25.4.2.1.2 | Running Programs |
| 1.3.6.1.2.1.25.4.2.1.4 | Processes Path |
| 1.3.6.1.2.1.25.2.3.1.4 | Storage Units |
| 1.3.6.1.2.1.25.6.3.1.2 | Software Name |
| 1.3.6.1.4.1.77.1.2.25 | User Accounts |
| 1.3.6.1.2.1.6.13.1.3 | TCP Local Ports |
```bash
# nmap SNMP scan
sudo nmap -sU --open -p 161 192.168.50.1-254 -oG open-snmp.txt

# community file
cat community
public
private
manager
# oneliner ip collect
for ip in $(seq 1 254); do echo 192.168.50.$ip; done > ips
# use onesixtyone to bruteforce community strings
onesixtyone -c community -i ips
```
#### MIB enumerate
```bash
# using snmpwalk to anumerate entire MIB tree
snmpwalk -c public -v1 -t 10 192.168.50.151
# OUTPUT
iso.3.6.1.2.1.1.1.0 = STRING: "Hardware: Intel64 Family 6 Model 79 Stepping 1 AT/AT COMPATIBLE - Software: Windows Version 6.3 (Build 17763 Multiprocessor Free)"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.311.1.1.3.1.3
iso.3.6.1.2.1.1.3.0 = Timeticks: (78235) 0:13:02.35
iso.3.6.1.2.1.1.4.0 = STRING: "admin@megacorptwo.com"
iso.3.6.1.2.1.1.5.0 = STRING: "dc01.megacorptwo.com"
iso.3.6.1.2.1.1.6.0 = ""
iso.3.6.1.2.1.1.7.0 = INTEGER: 79
iso.3.6.1.2.1.2.1.0 = INTEGER: 24
```

```bash
# using snmpwalk to enumerate Windows users
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.4.1.77.1.2.25
# OUTPUT
iso.3.6.1.4.1.77.1.2.25.1.1.5.71.117.101.115.116 = STRING: "Guest"
iso.3.6.1.4.1.77.1.2.25.1.1.6.107.114.98.116.103.116 = STRING: "krbtgt"
iso.3.6.1.4.1.77.1.2.25.1.1.7.115.116.117.100.101.110.116 = STRING: "student"
iso.3.6.1.4.1.77.1.2.25.1.1.13.65.100.109.105.110.105.115.116.114.97.116.111.114 = STRING: "Administrator"
```

```bash
# using snmpwalk to see running processes
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.25.4.2.1.2
# OUTPUT
iso.3.6.1.2.1.25.4.2.1.2.1 = STRING: "System Idle Process"
# <--SNIP-->
iso.3.6.1.2.1.25.4.2.1.2.680 = STRING: "svchost.exe"
```

```bash
# using snmpwalk to see installed softwares
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.25.6.3.1.2
# OUTPUT
iso.3.6.1.2.1.25.6.3.1.2.1 = STRING: "Microsoft Visual C++ 2019 X64 Minimum Runtime - 14.27.29016"
# <--SNIP-->
iso.3.6.1.2.1.25.6.3.1.2.7 = STRING: "Microsoft Visual C++ 2019 X86 Minimum Runtime - 14.27.29016"
```

```bash
# using snmpwalk to enumerate open TCP ports
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.6.13.1.3
# OUTPUT
iso.3.6.1.2.1.6.13.1.3.0.0.0.0.88.0.0.0.0.0 = INTEGER: 88
# <--SNIP-->
iso.3.6.1.2.1.6.13.1.3.0.0.0.0.5985.0.0.0.0.0 = INTEGER: 5985
```
---
## Extra
### Vulnerability scanning with Nmap
#nmap #vunlerability-scanning #NSE

```bash
# go to the NSE locatoin
cd /usr/share/nmap/scripts/
# search for "vuln" scripts
cat script.db  | grep "\"vuln\""
# using the script
sudo nmap -sV -p 443 --script "vuln" 192.168.50.124

# use external NSE scripts
# search fo CVE- script and copy it to the nmap NSE folder
sudo cp /home/kali/Downloads/http-vuln-cve-2021-41773.nse /usr/share/nmap/scripts/http-vuln-cve2021-41773.nse
# update the db
sudo nmap --script-updatedb
# utilize the NSE script
sudo nmap -sV -p 443 --script "http-vuln-cve2021-41773" 192.168.50.124
```
