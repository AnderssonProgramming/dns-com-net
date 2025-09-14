# Step8 didnt work

Perfect! Your configuration files are syntactically correct! The issue is that the `named` user/group doesn't exist on your Solaris system. Let's fix this and continue.

## Fix the User/Group Issue

### Step 1: Check what users exist for BIND

```bash
# Check if bind user exists instead of named
root@grupo7:~# id bind
root@grupo7:~# id named
root@grupo7:~# grep bind /etc/passwd
root@grupo7:~# grep named /etc/passwd

```

### Step 2: Create the named user and group (if they don't exist)

```bash
# Create named group
root@grupo7:~# groupadd named

# Create named user
root@grupo7:~# useradd -g named -d /var/named -s /bin/false named

```

### Step 3: Now set the correct permissions

```bash
chown -R named:named /var/named
chmod 755 /var/named
chmod 644 /var/named/*
chown named:named /etc/named.conf
chmod 644 /etc/named.conf

```

![image.png](Step8%20didnt%20work%20265f56fc503e807a80cbcc525efe949d/image.png)

### Step 4: Alternative - Use root permissions (simpler for testing) ❌

If creating the user is problematic, you can run BIND as root for now:

```bash
root@grupo7:~# chown -R root:root /var/named
root@grupo7:~# chmod 755 /var/named
root@grupo7:~# chmod 644 /var/named/*
root@grupo7:~# chown root:root /etc/named.conf
root@grupo7:~# chmod 644 /etc/named.conf

```

## Step 5: Start the DNS Service

Since your configuration is valid, let's start the service:

```bash
# Kill any existing processes
root@grupo7:~# pkill named

# Start named service
root@grupo7:~# /usr/sbin/named

```

## Step 6: Check if it's running

```bash
# Check if process is running
root@grupo7:~# ps -ef | grep named

# Check if listening on port 53
root@grupo7:~# netstat -an | grep :53

```

## Step 7: Test DNS Resolution

```bash
# Configure resolv.conf first
root@grupo7:~# vi /etc/resolv.conf

```

Add:

```bash
nameserver 127.0.0.1
nameserver 10.2.77.178

```

Then test:

```bash
# Test your domain
root@grupo7:~# nslookup dns.cristian.com.it
root@grupo7:~# nslookup www.cristian.com.it

# Test internet resolution
root@grupo7:~# nslookup www.google.com

```

**Try these steps and tell me:**

1. Did the `named` user creation work, or did you use root permissions?
2. Did the DNS service start successfully?
3. What do you see from the `ps -ef | grep named` command?
4. Can you resolve `dns.cristian.com.it`?

Once this is working, we'll proceed with the automatic startup configuration!