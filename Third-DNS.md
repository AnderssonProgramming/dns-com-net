# Third - DNS

[Slackware net commands](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/Slackware%20net%20commands%20264f56fc503e8085bd88d9584832e069.md) 

# SLACKWARE - PRYMARY DNS ✅

Perfect! Let's complete Point 1 step by step. Since you have 2 students, we'll configure 2 domains.

## Step 1: Plan Your Configuration

First, let's define your domains and servers:

**Replace these with your actual names:**

- Domain 1: `cristian.com.it`
- Domain 2:`andersson.org.uk`

**Server Distribution:**

- **Domain 1 (cristian.com.it)**: Primary on Solaris, Secondary on Slackware + Windows
- **Domain 2 (andersson.org.uk)**: Primary on Slackware, Secondary on Solaris + Windows

## Step 2: Start with Slackware (Primary DNS for Domain 2)

### 2.0. Check if BIND is already installed

```bash

*which named*
*ls /usr/sbin/named*
```

If you see `/usr/sbin/named`, BIND is already installed! Skip to step 4.

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image.png)

### ~~2.1 Install BIND (if needed)~~

```bash
# Mount the CD/ISO and install BIND
mount /dev/cdrom /mnt/cdrom
cd /mnt/cdrom/slackware*
installpkg bind*.txz
umount /mnt/cdrom

```

### 2.2 Create the main configuration file

```bash
nano /etc/named.conf
```

**This is the configuration**

```bash
options {
        directory "/var/named";
        allow-query { any; };
        recursion yes;
};

// Root zone
zone "." IN {
        type hint;
        file "caching-example/named.root";
};

// PRIMARY zone for andersson.org.uk
zone "andersson.org.uk" IN {
        type master;
        file "andersson.org.uk.zone";
};

// Localhost zones
zone "localhost" IN {
        type master;
        file "caching-example/localhost.zone";
        allow-update { none; };
};

zone "0.0.127.in-addr.arpa" IN {
        type master;
        file "caching-example/named.local";
        allow-update { none; };
        
       
};
```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%201.png)

### 2.4 Create the SOA file

```bash
nano /var/named/andersson.soa
```

**Copy this content (**

```bash
;
; SOA record for andersson.org.uk
;
@ IN SOA dns.andersson.org.uk. admin.andersson.org.uk. (
    2024120301  ; Serial (YYYYMMDDXX)
    3600        ; Refresh (1 hour)
    1800        ; Retry (30 minutes)
    604800      ; Expire (1 week)
    86400       ; Minimum TTL (1 day)
)
```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%202.png)

### 2.4 Create the domain hosts file

```bash
nano /var/named/andersson.org.uk.zone

```

**Copy this content (replace IPs with your assigned range):**

```bash
;
; Zone file for andersson.org.uk
;
$INCLUDE andersson.soa

; Name Server
andersson.org.uk. IN NS dns.andersson.org.uk.

; IPv4 addresses (A records) - Usando tu rango real
dns.andersson.org.uk.     IN A 10.2.77.176
server1.andersson.org.uk. IN A 10.2.77.177
server2.andersson.org.uk. IN A 10.2.77.178
server3.andersson.org.uk. IN A 10.2.77.179

; IPv6 addresses (AAAA records)
server1.andersson.org.uk. IN AAAA 2001:db8::1
server2.andersson.org.uk. IN AAAA 2001:db8::2

; Aliases (CNAME records)
www.andersson.org.uk.     IN CNAME server1.andersson.org.uk.
mail.andersson.org.uk.    IN CNAME server2.andersson.org.uk.
web.andersson.org.uk.     IN CNAME server1.andersson.org.uk.
```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%203.png)

### 2.7 Start the DNS service🏗️

```bash
/usr/sbin/named

```

### 2.8 Check if it's running

```bash

ps aux | grep named
netstat -ln | grep :53

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%204.png)

## Step 3: Test Your Slackware DNS Server

### 3.1 Configure the server to use itself as DNS

```bash
nano /etc/resolv.conf

```

Add:

```bash
nameserver 127.0.0.1
nameserver 192.168.1.10  # Your DNS server IP

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%205.png)

### 3.2 Test with nslookup

```bash
nslookup
> server1.andersson.org.uk
> www.andersson.org.uk
> dns.andersson.org.uk
> www.google.com
> exit
```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%206.png)

## Before We Continue to Solaris...

**Tell me:**

1. What names did you choose for your domains? (Replace [yourname1] and [yourname2])
2. What IP address range were you assigned?
3. Did the Slackware DNS server start successfully?

Once you confirm this works, we'll configure Solaris as the primary for the first domain and then set up the secondary servers.

**Are you ready to test this first part?**

---

## Paso 3.3: Reiniciar el servicio

bash

`*killall named*
*/usr/sbin/named*`

## ***Make BIND Start Automatically on Boot***

## Make BIND Start Automatically on Boot

First, let's check if there's already a startup script:

```bash
ls -la /etc/rc.d/rc.bind*
```

If it exists, make it executable and enable it:

```bash
chmod +x /etc/rc.d/rc.bind
```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%207.png)

O añadir a archivo de arranque

# SOLARIS - PRYMARY DNS ✅

¡Perfecto! Ahora configuremos Solaris como servidor DNS primario para `cristian.com.it`.

## Configuración Solaris DNS Server

**IP Solaris**: 10.2.77.178

**Gateway**: 10.2.65.1

**Dominio**: cristian.com.it (Primario)

## Paso 1: Verificar si BIND está instalado

```bash
solaris# which named
solaris# ls /usr/sbin/named

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%208.png)

Si no está instalado, instálalo desde los paquetes de Solaris.

## Paso 2: Crear la configuración principal

```bash
solaris# vi /etc/named.conf

```

**Contenido completo:**

```bash
options {
        directory "/var/named";
        allow-query { any; };
        recursion yes;
};

// Root zone
zone "." IN {
        type hint;
        file "named.ca";
};

// PRIMARY zone for cristian.com.it
zone "cristian.com.it" IN {
        type master;
        file "cristian.com.it.zone";
};

// SECONDARY zone for andersson.org.uk (from Slackware)
zone "andersson.org.uk" IN {
        type slave;
        file "andersson.org.uk.slave";
        masters { 10.2.77.176; };
};

// Localhost zones
zone "localhost" IN {
        type master;
        file "localhost.zone";
        allow-update { none; };
};

zone "0.0.127.in-addr.arpa" IN {
        type master;
        file "named.local";
        allow-update { none; };
};

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%209.png)

## Paso 3: Crear directorio y archivos básicos

```bash
solaris# mkdir -p /var/named

```

### Crear archivo de root servers

```bash
solaris# nano /var/named/named.ca

```

**Contenido:**

```bash
;
; Root name servers
;
. 3600000 IN NS A.ROOT-SERVERS.NET.
. 3600000 IN NS B.ROOT-SERVERS.NET.
. 3600000 IN NS C.ROOT-SERVERS.NET.

; Root name servers by address
A.ROOT-SERVERS.NET. 3600000 IN A 198.41.0.4
B.ROOT-SERVERS.NET. 3600000 IN A 199.9.14.201
C.ROOT-SERVERS.NET. 3600000 IN A 192.33.4.12

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2010.png)

## Paso 4: Crear archivo SOA

```bash
solaris# vi /var/named/cristian.soa

```

**Contenido:**

```bash
;
; SOA record for cristian.com.it
;
@ IN SOA dns.cristian.com.it. admin.cristian.com.it. (
    2024120401  ; Serial (YYYYMMDDXX)
    3600        ; Refresh (1 hour)
    1800        ; Retry (30 minutes)
    604800      ; Expire (1 week)
    86400       ; Minimum TTL (1 day)
)

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2011.png)

## Paso 5: Crear archivo de zona principal

```bash
solaris# nano /var/named/cristian.com.it.zone
```

**Contenido:**

```bash
;
; Zone file for cristian.com.it
;
$INCLUDE /var/named/cristian.soa

; Name Server
cristian.com.it. 86400 IN NS dns.cristian.com.it.

; IPv4 addresses (A records) - Con TTL
dns.cristian.com.it.     86400 IN A 10.2.77.178
server1.cristian.com.it. 86400 IN A 10.2.77.180
server2.cristian.com.it. 86400 IN A 10.2.77.181
server3.cristian.com.it. 86400 IN A 10.2.77.182

; IPv6 addresses (AAAA records) - Con TTL
server1.cristian.com.it. 86400 IN AAAA 2001:db8:1::1
server2.cristian.com.it. 86400 IN AAAA 2001:db8:1::2

; Aliases (CNAME records) - Con TTL
www.cristian.com.it.     86400 IN CNAME server1.cristian.com.it.
mail.cristian.com.it.    86400 IN CNAME server2.cristian.com.it.
web.cristian.com.it.     86400 IN CNAME server1.cristian.com.it.
```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2012.png)

## Paso 6: Crear archivos localhost

```bash
solaris# nano /var/named/localhost.zone

```

**Contenido:**

```bash
@ IN SOA dns.cristian.com.it. admin.cristian.com.it. (
    2024120401
    3600
    1800
    604800
    86400
)

@ IN NS dns.cristian.com.it.
localhost IN A 127.0.0.1

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2013.png)

```bash
solaris# nano /var/named/named.local

```

**Contenido:**

```bash
@ IN SOA dns.cristian.com.it. admin.cristian.com.it. (
    2024120401
    3600
    1800
    604800
    86400
)

@ IN NS dns.cristian.com.it.
1 IN PTR localhost.cristian.com.it.

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2014.png)

## Paso 7: Iniciar el servicio

```bash
solaris# /usr/sbin/named

```

## Paso 8: Verificar funcionamiento

```bash
	solaris# ps -ef | grep named
	solaris# netstat -an | grep :53
```

---

# SOLARIS - PRIMARY DNS SERVER CONFIGURATION - SOLARIS AS SECOND DNS SERVER FOR SLACKWARE ✅

## Server Information

- **Solaris IP**: 10.2.77.178
- **Primary Domain**: cristian.com.it
- **Secondary Domain**: andersson.org.uk (from Slackware)

## Step 1: Check if BIND is Installed

```bash
solaris# which named
solaris# ls /usr/sbin/named

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2015.png)

~~If BIND is not installed:~~

```bash
# Install BIND package
solaris# pkgadd -d /cdrom/sol_*/Product SUNWbind
# or
solaris# pkg install bind

```

## Step 2: Create Directory Structure

```bash
solaris# mkdir -p /var/named
solaris# cd /var/named

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2016.png)

## Step 3: Create Main Configuration File

```bash
solaris# nano /etc/named.conf

```

**Content for /etc/named.conf:**

```bash
options {
    directory "/var/named";
    allow-query { any; };
    recursion yes;
    listen-on { any; };
    listen-on-v6 { none; };
};

// Root zone
zone "." IN {
    type hint;
    file "named.ca";
};

// PRIMARY zone for cristian.com.it
zone "cristian.com.it" IN {
    type master;
    file "cristian.com.it.zone";
    allow-transfer { 10.2.77.176; };  // Allow Slackware to transfer
};

// SECONDARY zone for andersson.org.uk (from Slackware)
zone "andersson.org.uk" IN {
    type slave;
    file "andersson.org.uk.slave";
    masters { 10.2.77.176; };
};

// Localhost zones
zone "localhost" IN {
    type master;
    file "localhost.zone";
    allow-update { none; };
};

zone "0.0.127.in-addr.arpa" IN {
    type master;
    file "named.local";
    allow-update { none; };
};

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2017.png)

## Step 4: Create Root Servers File

```bash
solaris# nano /var/named/named.ca

```

**Content for named.ca:**

```bash
;
; Root name servers (simplified)
;
.                        3600000      NS    A.ROOT-SERVERS.NET.
.                        3600000      NS    B.ROOT-SERVERS.NET.
.                        3600000      NS    C.ROOT-SERVERS.NET.

A.ROOT-SERVERS.NET.      3600000      A     198.41.0.4
B.ROOT-SERVERS.NET.      3600000      A     199.9.14.201
C.ROOT-SERVERS.NET.      3600000      A     192.33.4.12

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2018.png)

## Step 5: Create Zone File for cristian.com.it

```bash
solaris# nano /var/named/cristian.com.it.zone

```

**Content for cristian.com.it.zone:**

```bash
;
; Zone file for cristian.com.it
;
$TTL 86400
@       IN      SOA     dns.cristian.com.it. admin.cristian.com.it. (
                        2024120401      ; Serial (YYYYMMDDXX)
                        3600            ; Refresh (1 hour)
                        1800            ; Retry (30 minutes)
                        604800          ; Expire (1 week)
                        86400           ; Minimum TTL (1 day)
)

; Name Server
@               IN      NS      dns.cristian.com.it.

; IPv4 addresses (A records)
dns             IN      A       10.2.77.178
server1         IN      A       10.2.77.180
server2         IN      A       10.2.77.181
server3         IN      A       10.2.77.182

; IPv6 addresses (AAAA records)
server1         IN      AAAA    2001:db8:1::1
server2         IN      AAAA    2001:db8:1::2

; Aliases (CNAME records)
www             IN      CNAME   server1.cristian.com.it.
mail            IN      CNAME   server2.cristian.com.it.
web             IN      CNAME   server1.cristian.com.it.
ftp             IN      CNAME   server3.cristian.com.it.

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2019.png)

## Step 6: Create Localhost Zone Files

```bash
solaris# nano /var/named/localhost.zone

```

**Content for localhost.zone:**

```bash
$TTL 86400
@       IN      SOA     dns.cristian.com.it. admin.cristian.com.it. (
                        2024120401      ; Serial
                        3600            ; Refresh
                        1800            ; Retry
                        604800          ; Expire
                        86400           ; Minimum
)

@       IN      NS      dns.cristian.com.it.
@       IN      A       127.0.0.1

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2020.png)

```bash
solaris# nano /var/named/named.local

```

**Content for named.local:**

```bash
$TTL 86400
@       IN      SOA     dns.cristian.com.it. admin.cristian.com.it. (
                        2024120401      ; Serial
                        3600            ; Refresh
                        1800            ; Retry
                        604800          ; Expire
                        86400           ; Minimum
)

@       IN      NS      dns.cristian.com.it.
1       IN      PTR     localhost.

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2021.png)

## Step 7: Set Correct Permissions

```bash
chown -R named:named /var/named
chmod 755 /var/named
chmod 644 /var/named/*
chown named:named /etc/named.conf
chmod 644 /etc/named.conf

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2022.png)

## Step 8: Check Configuration Syntax

```bash
# Check main configuration
solaris# named-checkconf /etc/named.conf

# Check zone file
solaris# named-checkzone cristian.com.it /var/named/cristian.com.it.zone

```

**Expected output:**

```
zone cristian.com.it/IN: loaded serial 2024120401
OK

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2023.png)

[Step8 didnt work](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/Step8%20didnt%20work%20265f56fc503e807a80cbcc525efe949d.md) 

## Step 9: Start the DNS Service

```bash
# Kill any existing named processes
solaris# pkill named

# Start named service
solaris# /usr/sbin/named

# Or start with logging (for debugging)
solaris# /usr/sbin/named -f -g &

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2024.png)

## Step 10: Verify Service is Running

```bash
# Check if process is running
solaris# ps -ef | grep named

# Check if listening on port 53
solaris# netstat -an | grep :53

# Expected output:
# *.53                    *.*                    0      0 49152      0 LISTEN

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2025.png)

## Step 11: Configure DNS Resolution

```bash
solaris# nano /etc/resolv.conf

```

**Add these lines:**

```bash
nameserver 127.0.0.1
nameserver 10.2.77.178
nameserver 10.2.77.176

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2026.png)

## Step 12: Test Your DNS Server

```bash
# Test local domain resolution
nslookup dns.cristian.com.it
nslookup www.cristian.com.it
nslookup server1.cristian.com.it

# Test secondary domain (should get from Slackware)
nslookup server1.andersson.org.uk

# Test internet resolution
nslookup www.google.com
ping www.google.com

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2027.png)

## Step 13: Enable Automatic Startup

### Method 1: Using SMF (Service Management Facility) - Solaris 10+

```bash
# Enable DNS service
solaris# svcadm enable svc:/network/dns/server:default

# Check status
solaris# svcs -l dns/server

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2028.png)

### ~~ethod 2: Traditional init script~~

```bash
# Create startup script
solaris# vi /etc/init.d/named
```

**~~Content for /etc/init.d/named:~~**

```bash
#!/bin/sh

case "$1" in
start)
    echo "Starting BIND DNS server..."
    /usr/sbin/named
    ;;
stop)
    echo "Stopping BIND DNS server..."
    pkill named
    ;;
restart)
    $0 stop
    sleep 2
    $0 start
    ;;
*)
    echo "Usage: $0 {start|stop|restart}"
    exit 1
    ;;
esac

```

```bash
# Make executable and create links
solaris# chmod 755 /etc/init.d/named
solaris# ln -s /etc/init.d/named /etc/rc2.d/S85named
solaris# ln -s /etc/init.d/named /etc/rc1.d/K15named

```

## Step 14: Final Verification

After reboot, test everything:

```bash
# Check service status
solaris# ps -ef | grep named
solaris# netstat -an | grep :53

# Test all domains
solaris# nslookup dns.cristian.com.it
solaris# nslookup www.cristian.com.it
solaris# nslookup mail.cristian.com.it
solaris# nslookup server1.andersson.org.uk
solaris# ping www.google.com

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2029.png)

## Summary

Your Solaris server is now configured as:

- ✅ **Primary DNS** for `cristian.com.it`
- ✅ **Secondary DNS** for `andersson.org.uk`
- ✅ **Auto-start** on boot
- ✅ **Zone transfers** enabled for replication

**Next Steps:** Configure Windows Server as secondary for both domains!

---

# 3. SLACKWARE AS SECONDARY DNS FOR SOLARIS ✅

### **Slackware (10.2.77.176)**

- ✅ **Primary DNS** for `andersson.org.uk`
- ✅ Auto-start configured

### **Solaris (10.2.77.178)**

- ✅ **Primary DNS** for `cristian.com.it`
- ✅ **Secondary DNS** for `andersson.org.uk` (gets zone from Slackware)
- ✅ SMF auto-start configured

## What We Need to Do Now ⚡

**Update Slackware** to also be **Secondary DNS** for `cristian.com.it` (getting zone from Solaris)

So Slackware will be:

- **Primary** for `andersson.org.uk`
- **Secondary** for `cristian.com.it`

## Update Slackware Configuration

Let's add the secondary zone configuration to your existing Slackware setup:

### Step 1: Edit Slackware's named.conf

```bash
# On Slackware machine
root@darkstar:~# nano /etc/named.conf

```

**Add this secondary zone section to your existing config:**

```bash
options {
        directory "/var/named";
        allow-query { any; };
        recursion yes;
};

// Root zone
zone "." IN {
        type hint;
        file "caching-example/named.root";
};

// PRIMARY zone for andersson.org.uk
zone "andersson.org.uk" IN {
        type master;
        file "andersson.org.uk.zone";
        allow-transfer { 10.2.77.178; };  // Allow Solaris to transfer
};

// NEW: SECONDARY zone for cristian.com.it (from Solaris)
zone "cristian.com.it" IN {
        type slave;
        file "cristian.com.it.slave";
        masters { 10.2.77.178; };
};

// Localhost zones
zone "localhost" IN {
        type master;
        file "caching-example/localhost.zone";
        allow-update { none; };
};

zone "0.0.127.in-addr.arpa" IN {
        type master;
        file "caching-example/named.local";
        allow-update { none; };
};

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2030.png)

### Step 2: Restart Slackware DNS Service

```bash
root@darkstar:~# killall named
root@darkstar:~# /usr/sbin/named

```

### Step 3: Test Zone Transfer and Resolution

```bash
# Test if it can resolve cristian.com.it domains (should get from Solaris)
root@darkstar:~# nslookup dns.cristian.com.it
root@darkstar:~# nslookup www.cristian.com.it
root@darkstar:~# nslookup server1.cristian.com.it

# Test original andersson.org.uk still works
	root@darkstar:~# nslookup server1.andersson.org.uk

# Check if the slave zone file was created
root@darkstar:~# ls -la /var/named/cristian.com.it.slave

```

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2031.png)

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2032.png)

**Try this configuration and tell me:**

1. Does the named service restart without errors?
2. Can you resolve `cristian.com.it` domains from Slackware?
3. Was the `cristian.com.it.slave` file created?

This will complete the bidirectional secondary DNS setup! 🎯

[Lab dns explained till this point](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/Lab%20dns%20explained%20till%20this%20point%20268f56fc503e808ea4b3f3d4d343e0a4.md) 

[Lab state explained](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/Lab%20state%20explained%20268f56fc503e80ceb678fa071f071f39.md) 

---

[Action plan](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/Action%20plan%20268f56fc503e80e9aacffa65654c7332.md) 

# WINDOWS AS SLAVE FOR BOTH DNS SERVERS 🏗️

# Windows Server 2025 - Secondary DNS Server Configuration

## Overview

Configure Windows Server 2025 as **secondary DNS** for both domains:

- **Secondary for**: `andersson.org.uk` (Primary on Slackware 10.2.77.176)
- **Secondary for**: `cristian.com.it` (Primary on Solaris 10.2.77.178)

## Prerequisites

- Windows Server 2025 with GUI
- Network connectivity to Slackware and Solaris servers
- Administrative privileges

---

## Step 1: Install DNS Server Role

### Method 1: Using Server Manager (GUI)

1. **Open Server Manager**
    - Click **Start** → **Server Manager**
2. **Add Roles and Features**
    - Click **"Add roles and features"**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2033.png)
    
    - Click **Next** through the wizard
    - Select **"Role-based or feature-based installation"**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2034.png)
    
    - Click **Next**
3. **Select Server Roles**
    - Check **"DNS Server"**
    - When prompted, click **"Add Features"** to include management tools
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2035.png)
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2036.png)
    
    - Click **Next** through remaining screens
    - Click **"Install"**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2037.png)
    

![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2038.png)

### Method 2: Using PowerShell (Alternative)

```powershell
# Open PowerShell as Administrator
Install-WindowsFeature DNS -IncludeManagementTools

```

---

## Step 2: Open DNS Manager

1. **Open DNS Manager**
    - Start Menu → **Administrative Tools** → **DNS**
    - Or: **Server Manager** → **Tools** → **DNS**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2039.png)
    
2. **Connect to Local Server**
    - The local server should appear in the left panel
    - If not, right-click **"DNS"** → **"Connect to DNS Server"** → **"This computer"**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2040.png)
    

---

## Step 3: Configure Secondary Zone for andersson.org.uk

### 3.1 Create Secondary Zone

1. **Right-click "Forward Lookup Zones"**
    - Select **"New Zone..."**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2041.png)
    
2. **Zone Type Selection**
    - Select **"Secondary zone"**
    - Click **Next**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2042.png)
    
3. **Zone Name**
    - Enter: `andersson.org.uk`
    - Click **Next**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2043.png)
    
4. **Master DNS Servers**
    - Click **"Add..."**
    - Enter IP: `10.2.77.176` (Slackware server)
    - Click **OK**
    - Click **Next**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2044.png)
    
5. **Complete the Wizard**
    - Click **"Finish"**
    
    ![image.png](Third%20-%20DNS%20263f56fc503e80ddb361c216e75fd3bf/image%2045.png)
    

### 3.2 Verify Zone Transfer

1. **Check Zone Status**
    - Expand **"Forward Lookup Zones"**
    - Click on **"andersson.org.uk"**
    - You should see records transferred from Slackware
2. **Force Zone Transfer (if needed)**
    - Right-click **"andersson.org.uk"**
    - Select **"Transfer from Master"**

---

## Step 4: Configure Secondary Zone for cristian.com.it

### 4.1 Create Secondary Zone

1. **Right-click "Forward Lookup Zones"**
    - Select **"New Zone..."**
2. **Zone Type Selection**
    - Select **"Secondary zone"**
    - Click **Next**
3. **Zone Name**
    - Enter: `cristian.com.it`
    - Click **Next**
4. **Master DNS Servers**
    - Click **"Add..."**
    - Enter IP: `10.2.77.178` (Solaris server)
    - Click **OK**
    - Click **Next**
5. **Complete the Wizard**
    - Click **"Finish"**

### 4.2 Verify Zone Transfer

1. **Check Zone Status**
    - Click on **"cristian.com.it"**
    - You should see records transferred from Solaris
2. **Force Zone Transfer (if needed)**
    - Right-click **"cristian.com.it"**
    - Select **"Transfer from Master"**

---

## Step 5: Configure DNS Server Settings

### 5.1 Configure Forwarders (for internet resolution)

1. **Right-click your server name** in DNS Manager
    - Select **"Properties"**
2. **Go to "Forwarders" tab**
    - Check **"Enable forwarders"**
    - Click **"Add..."**
    - Add: `8.8.8.8` (Google DNS)
    - Add: `1.1.1.1` (Cloudflare DNS)
    - Click **OK**

### 5.2 Configure Recursion

1. **Go to "Advanced" tab**
    - Ensure **"Enable recursion"** is checked
    - Click **OK**

---

## Step 6: Configure Network Settings

### 6.1 Set DNS Server IP

1. **Open Network Settings**
    - Control Panel → Network and Internet → Network Connections
    - Right-click your network adapter → **Properties**
2. **Configure IPv4**
    - Select **"Internet Protocol Version 4 (TCP/IPv4)"**
    - Click **"Properties"**
    - Select **"Use the following DNS server addresses"**
    - **Preferred DNS server**: `127.0.0.1` (itself)
    - **Alternate DNS server**: `10.2.77.176` (Slackware)
    - Click **OK**

---

## Step 7: Test DNS Resolution

### 7.1 Test Using Command Prompt

```bash
# Open Command Prompt as Administrator

# Test andersson.org.uk domain (from Slackware)
nslookup server1.andersson.org.uk
nslookup www.andersson.org.uk
nslookup dns.andersson.org.uk

# Test cristian.com.it domain (from Solaris)
nslookup server1.cristian.com.it
nslookup www.cristian.com.it
nslookup dns.cristian.com.it

# Test internet resolution
nslookup www.google.com
ping www.google.com

```

### 7.2 Test Using PowerShell

```powershell
# Test DNS resolution with PowerShell
Resolve-DnsName server1.andersson.org.uk
Resolve-DnsName www.cristian.com.it
Resolve-DnsName www.google.com

# Test specific DNS server
Resolve-DnsName server1.andersson.org.uk -Server 127.0.0.1

```

---

## Step 8: Monitor Zone Transfers

### 8.1 Check Event Logs

1. **Open Event Viewer**
    - Start → Administrative Tools → Event Viewer
    - Navigate to: **Applications and Services Logs** → **DNS Server**
2. **Look for Zone Transfer Events**
    - Look for successful zone transfer messages
    - Check for any errors

### 8.2 Monitor DNS Manager

1. **Refresh Zones Regularly**
    - Right-click zones → **Reload** or **Transfer from Master**
2. **Check Zone Serial Numbers**
    - Compare with primary servers to ensure updates

---

## Step 9: Configure Automatic Startup

### 9.1 Verify DNS Service

1. **Open Services**
    - Start → Run → `services.msc`
    - Find **"DNS Server"** service
    - Ensure **Startup Type** is **"Automatic"**
    - Ensure **Status** is **"Running"**
2. **Configure Service Recovery**
    - Right-click **"DNS Server"** → **Properties**
    - Go to **"Recovery"** tab
    - Set failure actions to restart the service

---

## Step 10: Advanced Configuration (Optional)

### 10.1 Configure Zone Transfer Security

1. **Right-click each secondary zone**
    - Select **"Properties"**
    - Go to **"Zone Transfers"** tab
    - Select **"Only to the following servers"**
    - Add trusted server IPs

### 10.2 Configure Notifications

1. **On primary servers** (Slackware/Solaris)
    - Configure notify lists to include Windows Server IP
    - This ensures immediate updates when zones change

---

## Troubleshooting Common Issues

### Issue 1: Zone Transfer Fails

**Symptoms**: Empty zones, no records transferred
**Solutions**:

- Check network connectivity: `ping 10.2.77.176` and `ping 10.2.77.178`
- Verify firewall settings on primary servers
- Ensure zone transfer is allowed in primary server configs
- Check DNS service is running on primary servers

### Issue 2: DNS Resolution Not Working

**Symptoms**: NXDOMAIN errors, timeouts
**Solutions**:

- Verify DNS server IP configuration
- Check forwarders configuration
- Restart DNS Server service
- Clear DNS cache: `ipconfig /flushdns`

### Issue 3: Slow DNS Responses

**Symptoms**: Long resolution times
**Solutions**:

- Add more forwarders
- Check network latency to primary servers
- Verify DNS cache settings

---

## Expected Final Results

After completing all steps, your Windows Server should:

✅ **Resolve andersson.org.uk domains**:

- `server1.andersson.org.uk` → `10.2.77.177`
- `www.andersson.org.uk` → points to server1
- `dns.andersson.org.uk` → `10.2.77.176`

✅ **Resolve cristian.com.it domains**:

- `server1.cristian.com.it` → `10.2.77.180`
- `www.cristian.com.it` → points to server1
- `dns.cristian.com.it` → `10.2.77.178`

✅ **Resolve internet domains**:

- `www.google.com` → external IP
- `www.microsoft.com` → external IP

✅ **Provide redundancy**:

- If Slackware fails, Windows can still resolve `andersson.org.uk`
- If Solaris fails, Windows can still resolve `cristian.com.it`

---

## Final Lab Architecture

```
DNS Lab - Complete Architecture

andersson.org.uk:
├── Primary:   Slackware (10.2.77.176)
├── Secondary: Solaris   (10.2.77.178)
└── Secondary: Windows   (your-windows-ip)

cristian.com.it:
├── Primary:   Solaris   (10.2.77.178)
├── Secondary: Slackware (10.2.77.176)
└── Secondary: Windows   (your-windows-ip)

```

**Your DNS lab will have complete high availability with 3 servers for each domain!** 🚀