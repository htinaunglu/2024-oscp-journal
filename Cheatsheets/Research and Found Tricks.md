```bash
# Take the ip addresses from nmap -oG output!
cat smb.txt | grep open | grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' | awk '!a[$0]++' | tee targets.txt
```

```bash
# bash one liner for enum4linux on many ips.
for ip in $(cat target_ips.txt); do enum4linux -a $ip; done
```

```bash
# when you can't connect to the RDP, this is due to the MTU size, try reducing it.
sudo ip l s tun0 mtu 1280

## REF
# https://github.com/FreeRDP/FreeRDP/issues/6814
```

