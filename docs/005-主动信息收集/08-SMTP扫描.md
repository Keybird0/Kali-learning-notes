# SMTP

## 0x01 探测

### nc
```
nc -nc 1.1.1.1 25 
```

### nmap
```
nmap smtp.163.cn -p25 --script=smtp-enum-users.nse --script-args=smtp-enum-user.methods={VRFY}

nmap smtp.163.com -p25 --script=smtp-open-relay.nse
```

### smtp-user-enum

```
smtp-user-enum -M VRFY -U users.txt -t 10.0.0.1
```
