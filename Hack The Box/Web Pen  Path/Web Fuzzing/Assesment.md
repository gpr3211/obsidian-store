
## Part 1
Initial ffuzz with Directories/common.txt finds  `admin`   dir. Scanning that we get admin/panel.php accepting an accessID query param.
```bash
ecard                   [Status: 200, Size: 58, Words: 8, Lines: 1, Duration
```
Since page always returns 200 with "cannot acces" size 58 request we can do second fuzz to filter out those 
```bash
ffuf -u http://83.136.253.5:48922/admin/panel.php?accessID=FUZZ -ic  -w ../seclists/Discovery/Web-Content/common.txt -fs 58
```
	
1. another  ffuz of the panel parameter  we find a param value. "getaccess" . 

	   Head on over to the fuzzing_fun.htb vhost for some more fuzzing fun!

This tells us to add  the vhost to our /etc/hosts and try from there
## Part 2


## NOTES

The first step when encountering a new vhost is to add the IP and the name of the vhost to our /etc/hosts file so that we can work with it ┌──(macc㉿kaliLab)-[~/htb] └─$ sudo echo "94.237.55.38:53269 fuzzing_fun.htb" | sudo tee -a /etc/hosts Lets confirm with a curl to the vhost: ┌──(macc㉿kaliLab)-[~/htb] └─$ curl fuzzing_fun.htb:53269 whats the point

12:05 a.m.

Great question! Let me explain **why** you need to add entries to `/etc/hosts` when working with virtual hosts (vhosts).

## The Problem Without /etc/hosts

When you try to access `http://fuzzing_fun.htb:53269`, your system needs to resolve that hostname to an IP address. Normally it would:

1. Check DNS servers
2. Not find `fuzzing_fun.htb` (it's not a real domain)
3. Fail to connect

## What /etc/hosts Does

By adding this line to `/etc/hosts`:

```
94.237.55.38 fuzzing_fun.htb
```

You're telling your system: "When I ask for `fuzzing_fun.htb`, use IP `94.237.55.38`"

## Why Virtual Hosts Need This

Many web servers (like Apache, Nginx) use **virtual hosting** - multiple websites on the same IP address. The server decides which site to show based on the `Host:` HTTP header sent in your request.

**Without the hosts entry:**

bash

```bash
curl 94.237.55.38:53269
# Server receives: Host: 94.237.55.38
# May show default site or error
```

**With the hosts entry:**

bash

```bash
curl fuzzing_fun.htb:53269
# Server receives: Host: fuzzing_fun.htb
# Shows the correct virtual host!
```

## The Point

This is especially important in CTF/pentesting scenarios where:

- You discover subdomains or vhosts during enumeration
- The target might have multiple sites on one IP
- You need to access the specific vhost to find vulnerabilities
- The domain isn't publicly resolvable via DNS

Without the correct `Host` header, you might miss entire attack surfaces!
