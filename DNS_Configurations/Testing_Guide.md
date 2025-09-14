# DNS Configuration Testing Guide
# Lab 03 - Computer Networks

## Testing Commands Summary

### Basic DNS Resolution Tests
```bash
# Test local domain resolution
nslookup dns.cristian.com.it
nslookup www.andersson.org.uk
nslookup server1.cristian.com.it

# Test external domain resolution
nslookup www.google.com
nslookup www.escuelaing.edu.co
```

### Advanced nslookup Testing
```bash
# Interactive nslookup mode
nslookup

# Test different record types
> set type=A
> server1.cristian.com.it

> set type=AAAA
> server1.cristian.com.it

> set type=NS
> cristian.com.it

> set type=CNAME
> www.cristian.com.it

> set debug
> dns.cristian.com.it

> exit
```

### Service Verification Commands
```bash
# Check if DNS service is running
ps aux | grep named
netstat -ln | grep :53

# Check configuration syntax
named-checkconf /etc/named.conf
named-checkzone cristian.com.it /var/named/cristian.com.it.zone

# Test zone transfers
dig @10.2.77.178 cristian.com.it AXFR
dig @10.2.77.176 andersson.org.uk AXFR
```

### Troubleshooting Commands
```bash
# View system logs
tail -f /var/log/messages
tail -f /var/log/named.log

# Restart DNS service
killall named
/usr/sbin/named

# Check file permissions
ls -la /var/named/
ls -la /etc/named.conf
```

## Expected Results

### Successful A Record Query
```
Server:    10.2.77.178
Address:   10.2.77.178#53

Name:      server1.cristian.com.it
Address:   10.2.77.180
```

### Successful CNAME Query
```
Server:    10.2.77.178
Address:   10.2.77.178#53

www.cristian.com.it    canonical name = server1.cristian.com.it.
Name:      server1.cristian.com.it
Address:   10.2.77.180
```

### Zone Transfer Output
```
cristian.com.it.        86400   IN      SOA     dns.cristian.com.it. admin.cristian.com.it. 2024120401 3600 1800 604800 86400
cristian.com.it.        86400   IN      NS      dns.cristian.com.it.
dns.cristian.com.it.    86400   IN      A       10.2.77.178
server1.cristian.com.it. 86400  IN      A       10.2.77.180
server2.cristian.com.it. 86400  IN      A       10.2.77.181
server3.cristian.com.it. 86400  IN      A       10.2.77.182
```
