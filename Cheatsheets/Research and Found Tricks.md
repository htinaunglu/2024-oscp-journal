```bash
# Take the ip addresses from nmap -oG output!
cat smb.txt | grep open | grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' | awk '!a[$0]++' | tee targets.txt
```

```bash
# bash one liner for enum4linux on many ips.
for ip in $(cat target_ips.txt); do enum4linux -a $ip; done
```
