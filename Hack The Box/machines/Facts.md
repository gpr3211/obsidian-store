## Step 1 
```bash
   nmap -sC -sV -vv -oA nmap/facts <ip>
```
-vv for very verbose
-oA returns output in 3 mjor formats
-sV probe open ports
-sC default script
1
# Facts (S10)— HTB Writeup

[

![wahyudnWis](https://miro.medium.com/v2/da:true/resize:fill:38:38/0*hbiDvCphbZ6JQezW)





](https://medium.com/@mwahyudinwisesar?source=post_page---byline--3f6369768e57---------------------------------------)

[wahyudnWis](https://medium.com/@mwahyudinwisesar?source=post_page---byline--3f6369768e57---------------------------------------)

4 min read

·

9 hours ago

[](https://medium.com/m/signin?actionUrl=https%3A%2F%2Fmedium.com%2F_%2Fbookmark%2Fp%2F3f6369768e57&operation=register&redirect=https%3A%2F%2Fmedium.com%2F%40mwahyudinwisesar%2Ffacts-s10-htb-writeup-3f6369768e57&source=---header_actions--3f6369768e57---------------------bookmark_footer------------------)

[

](https://medium.com/m/signin?actionUrl=https%3A%2F%2Fmedium.com%2Fplans%3Fdimension%3Dpost_audio_button%26postId%3D3f6369768e57&operation=register&redirect=https%3A%2F%2Fmedium.com%2F%40mwahyudinwisesar%2Ffacts-s10-htb-writeup-3f6369768e57&source=---header_actions--3f6369768e57---------------------post_audio_button------------------)

![](https://miro.medium.com/v2/resize:fit:823/1*zD9rL4P9OFrEBROZEN7RdQ.png)

In this section, i’ll share my experience about pwned “Facts” HTB Machine

First, this machine is an easy machine with Linux OS, and skill required :

- Web and cloud enumeration
- S3/object storage misconfiguration analysis
- SSH key handling and Linux fundamentals
- Post-exploitation enumeration (`sudo -l`)
- Binary abuse and privilege escalation (fecter custom code loading)

**Overall level:** Medium  
Focused on misconfiguration chaining rather than complex exploit development.

Always do reconn, let's use Nmap

![](https://miro.medium.com/v2/resize:fit:806/1*MtHW5K_7o5NErKCm_aRrDg.png)

Nothing special here, now do an enumeration

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:762/1*-UvBx132MNXTefPlx4jTRQ.png)

there are so many 200, but there is an interesting thing here

![](https://miro.medium.com/v2/resize:fit:685/1*UnX9blFcJPRdrDmwlj1bGQ.png)

Let’s create an account on the admin panel and log in with the account

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:913/1*EE9M0ts7mbACDoGnc37dzw.png)

We can login into the admin panel, but the role is only clients, not administrators.

but it’s ok, now we’re gonna looking for the Vulnerabilities on Cameleon CMS — Version 2.9.0

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:913/1*5GXXcMhdCmD-4s6PEufBaQ.png)

We found it, it’s [CVE-2025–2304](https://github.com/Alien0ne/CVE-2025-2304)

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:913/1*LTCdv4PlEQ44OHrPWqkxgQ.png)

let’s try it on

We’re doing privEsc and AWS S3 Configuration Leak

![](https://miro.medium.com/v2/resize:fit:844/1*cVGdinxJY4tMNh9pPrsQJw.png)

After obtaining the exposed AWS access key and secret key, I configured the AWS CLI to validate whether the credentials were still valid. Once configured, I specified the custom endpoint pointing to the internal service and enumerated the available S3 buckets. This confirmed that the credentials were active and allowed access to internal storage resources

![](https://miro.medium.com/v2/resize:fit:817/1*8jU2l_Xf2vsY1lAuoWCXKA.png)

Let’s dig deeper into the bucket’s contents. Interestingly, an SSH private key was found inside. This could potentially allow direct access to the target machine, so I copied the key to my attack machine for further use

![](https://miro.medium.com/v2/resize:fit:852/1*vTOSOVAFjYIDoHe-hH3Gtw.png)

After obtaining the private key, I converted it into a format compatible with John the Ripper using `ssh2john.py`. Then, I launched a dictionary attack with the rockyou wordlist. Within a few minutes, John successfully recovered the passphrase: `dragonballz`

## Get wahyudnWis’s stories in your inbox

Join Medium for free to get updates from this writer.

With the passphrase in hand, I was able to authenticate via SSH using the private key. dont forget to give permission to the key “chmod 600” and get the user flag~

![](https://miro.medium.com/v2/resize:fit:750/1*R8cnFaZupYpGVpw423Tdqw.png)

After obtaining the user flag, the next step is gaining root flag, of course, wkwk

This means that the user `trivia` is allowed to execute `/usr/bin/facter` as any user (including root) without being prompted for a password. In other words, `facter` can be executed with root privileges. If the binary can be abused or leveraged for command execution, this misconfiguration could lead to full privilege escalation.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:913/1*nDeYZxtoHzIxbYKQtCaf4w.png)

After discovering that `facter` could be executed as root without a password, I leveraged its Ruby-based functionality to achieve privilege escalation.

`Facter` is written in **Ruby** and allows the creation of custom facts using Ruby syntax. These custom facts can execute arbitrary Ruby code when `facter` runs.

I created a malicious custom fact:

Facter.add(:pwn) do  
setcode do  
exec(“/bin/bash -p”)  
end  
end

and run this to spawn root:

sudo facter — custom-dir=/tmp pwn

And boommm, we got root!!

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:913/1*oKLzTCDsObqaiGA8BXQicQ.png)

Ruby was necessary because:

- `facter` loads custom facts as **Ruby scripts**
- The `Facter.add` function is part of the Ruby API
- The `exec()` call is a Ruby method that executes system commands

In short:

> _The exploitation worked because_ `_facter_` _executes Ruby code, and we were allowed to run it as root._

This allowed arbitrary command execution with elevated privileges, resulting in a root shell.

Key takeaways:

- Sensitive data exposure is often the first domino. A single leaked SSH private key can lead to initial access.
- Encryption alone is not enough, weak passphrases can be cracked, especially with common wordlists.
- Post-exploitation enumeration is critical. Running `sudo -l` should always be one of the first checks after gaining a shell.
- Any binary allowed via `NOPASSWD` must be treated as a potential root escalation vector.
- Scripting-based tools (Ruby, Python, etc.) are particularly dangerous when misconfigured, as they can execute arbitrary code.
- Privilege escalation is rarely about exploiting memory corruption, it’s often about abusing trust and misconfigurations.
- Chaining small weaknesses together is what turns “low severity findings” into full root compromise.