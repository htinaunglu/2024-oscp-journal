## Fingerprinting web servers with nmap
```bash
# nmap scan port 80 to see the version
sudo nmap -p80  -sV 192.168.50.20
# use NSE script for found service
sudo nmap -p80 --script=http-enum 192.168.50.20
```
- Use **Wappalyzer** to identify the tech stack.
### Gobuster
```bash
Running Gobuster
gobuster dir -u 192.168.50.20 -w /usr/share/wordlists/dirb/common.txt -t 5
```
### [[BurpSuite]]
```bash
# running burp
burpsuite
```
- **Debug** Page Contents
### Inspecting HTTP Response Headers and Sitemaps
```bash
# robots.txt
curl https://www.google.com/robots.txt

```