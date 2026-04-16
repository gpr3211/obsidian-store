

---

The rule-based attack is the most advanced and complex password cracking mode. Rules help perform various operations on the input wordlist, such as prefixing, suffixing, toggling case, cutting, reversing, and much more. Rules take mask-based attacks to another level and provide increased cracking rates. Additionally, the usage of rules saves disk space and processing time incurred as a result of larger wordlists.

A rule can be created using functions, which take a word as input and output it's modified version. The following table describes some functions which are compatible with JtR as well as Hashcat.

| **Function**    | **Description**                                                    | **Input**                             | **Output**                                                                                                        |
| --------------- | ------------------------------------------------------------------ | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| l               | Convert all letters to lowercase                                   | InlaneFreight2020                     | inlanefreight2020                                                                                                 |
| u               | Convert all letters to uppercase                                   | InlaneFreight2020                     | INLANEFREIGHT2020                                                                                                 |
| c / C           | capitalize / lowercase first letter and invert the rest            | inlaneFreight2020 / Inlanefreight2020 | Inlanefreight2020 / iNLANEFREIGHT2020                                                                             |
| t / TN          | Toggle case : whole word / at position N                           | InlaneFreight2020                     | iNLANEfREIGHT2020                                                                                                 |
| d / q / zN / ZN | Duplicate word / all characters / first character / last character | InlaneFreight2020                     | InlaneFreight2020InlaneFreight2020 / IInnllaanneeFFrreeiigghhtt22002200 / IInlaneFreight2020 / InlaneFreight20200 |
| { / }           | Rotate word left / right                                           | InlaneFreight2020                     | nlaneFreight2020I / 0InlaneFreight202                                                                             |
| ^X / $X         | Prepend / Append character X                                       | InlaneFreight2020 (^! / $! )          | !InlaneFreight2020 / InlaneFreight2020!                                                                           |
| r               | Reverse                                                            | InlaneFreight2020                     | 0202thgierFenalnI                                                                                                 |

---

A complete list of functions can be found [here](https://hashcat.net/wiki/doku.php?id=rule_based_attack#implemented_compatible_functions). Sometimes, the input wordlists contain words that don't match our target specifications. For example, a company's password policy might not allow users to set passwords less than 7 characters in length. In such cases, rejection rules can be used to prevent the processing of such words.

Words of length less than N can be rejected with `>N`, while words greater than N can be rejected with `<N`. A list of rejection rules can be found [here](https://hashcat.net/wiki/doku.php?id=rule_based_attack#rules_used_to_reject_plains).

_Note: Reject rules only work either with `hashcat-legacy`, or when using `-j` or `-k` with `Hashcat`. They will not work as regular rules (in a rule file) with `Hashcat`._

---

## Example Rule Creation

Let's look at how we can create a rule based on common passwords. Usual user behavior suggests that they tend to replace letters with similar numbers, like "`o`" can be replaced with "`0`" or "`i`" can be replaced with "`1`". This is commonly known as `L33tspeak` and is very efficient. Corporate passwords are often prepended or appended by a year. Let's create a rule to generate such words.

*Note: Reject rules only work either with `hashcat-legacy`, or when using `-j` or `-k` with `Hashcat`. They will not work as regular rules (in a rule file) with `Hashcat`. *

#### Rules

Working with Rules

```shell-session
c so0 si1 se3 ss5 sa@ $2 $0 $1 $9
```

The first letter word is capitalized with the `c` function. Then rule uses the substitute function `s` to replace `o` with `0`, `i` with `1`, `e` with `3` and a with `@`. At the end, the year `2019` is appended to it. Copy the rule to a file so that we can debug it.

#### Create a Rule File

Working with Rules

```shell-session
underhill341@htb[/htb]$ echo 'c so0 si1 se3 ss5 sa@ $2 $0 $1 $9' > rule.txt
```

#### Store the Password in a File

Working with Rules

```shell-session
underhill341@htb[/htb]$ echo 'password_ilfreight' > test.txt
```

---

Rules can be debugged using the "`-r`" flag to specify the rule, followed by the wordlist.

#### Hashcat - Debugging Rules

Working with Rules

```shell-session
underhill341@htb[/htb]$ hashcat -r rule.txt test.txt --stdout

P@55w0rd_1lfr31ght2019
```

As expected, the first letter was capitalized, and the letters were replaced with numbers. Let's consider the password "`St@r5h1p2019`". We can create the `SHA1` hash of this password via the command line:

#### Generate SHA1 Hash

Working with Rules

```shell-session
underhill341@htb[/htb]$ echo -n 'St@r5h1p2019' | sha1sum | awk '{print $1}' | tee hash
```

We can then use the custom rule created above and the `rockyou.txt` dictionary file to crack the hash using `Hashcat`.

#### Hashcat - Cracking Passwords Using Wordlists and Rules

Working with Rules

```shell-session
underhill341@htb[/htb]$ hashcat -a 0 -m 100 hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt -r rule.txt

hashcat (v6.1.1) starting...
<SNIP>

08004e35561328e357e34d07c53c7e4f41944e28:St@r5h1p2019
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: SHA1
Hash.Target......: 08004e35561328e357e34d07c53c7e4f41944e28
Time.Started.....: Fri Aug 28 22:17:13 2020, (3 secs)
Time.Estimated...: Fri Aug 28 22:17:16 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Mod........: Rules (rule.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  3519.2 kH/s (0.39ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 10592256/14344385 (73.84%)
Rejected.........: 0/10592256 (0.00%)
Restore.Point....: 10586112/14344385 (73.80%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: St0p69692019 -> S0r051x53nt2019
```

We were able to crack the hash with our custom rule and rockyou.txt. Hashcat supports the usage of multi-rules with repeated use of the `-r` flag. `Hashcat` installs with a variety of rules by default. They can be found in the rules folder.

#### Hashcat - Default Rules

Working with Rules

```shell-session
underhill341@htb[/htb]$ ls -l /usr/share/hashcat/rules/

total 2576
-rw-r--r-- 1 root root    933 Jun 19 06:20 best64.rule
-rw-r--r-- 1 root root    633 Jun 19 06:20 combinator.rule
-rw-r--r-- 1 root root 200188 Jun 19 06:20 d3ad0ne.rule
-rw-r--r-- 1 root root 788063 Jun 19 06:20 dive.rule
-rw-r--r-- 1 root root 483425 Jun 19 06:20 generated2.rule
-rw-r--r-- 1 root root  78068 Jun 19 06:20 generated.rule
drwxr-xr-x 1 root root   2804 Jul  9 21:01 hybrid
-rw-r--r-- 1 root root 309439 Jun 19 06:20 Incisive-leetspeak.rule
-rw-r--r-- 1 root root  35280 Jun 19 06:20 InsidePro-HashManager.rule
-rw-r--r-- 1 root root  19478 Jun 19 06:20 InsidePro-PasswordsPro.rule
-rw-r--r-- 1 root root    298 Jun 19 06:20 leetspeak.rule
-rw-r--r-- 1 root root   1280 Jun 19 06:20 oscommerce.rule
-rw-r--r-- 1 root root 301161 Jun 19 06:20 rockyou-30000.rule
-rw-r--r-- 1 root root   1563 Jun 19 06:20 specific.rule
-rw-r--r-- 1 root root  64068 Jun 19 06:20 T0XlC-insert_00-99_1950-2050_toprules_0_F.rule
-rw-r--r-- 1 root root   2027 Jun 19 06:20 T0XlC-insert_space_and_special_0_F.rule
-rw-r--r-- 1 root root  34437 Jun 19 06:20 T0XlC-insert_top_100_passwords_1_G.rule
-rw-r--r-- 1 root root  34813 Jun 19 06:20 T0XlC.rule
-rw-r--r-- 1 root root 104203 Jun 19 06:20 T0XlCv1.rule
-rw-r--r-- 1 root root     45 Jun 19 06:20 toggles1.rule
-rw-r--r-- 1 root root    570 Jun 19 06:20 toggles2.rule
-rw-r--r-- 1 root root   3755 Jun 19 06:20 toggles3.rule
-rw-r--r-- 1 root root  16040 Jun 19 06:20 toggles4.rule
-rw-r--r-- 1 root root  49073 Jun 19 06:20 toggles5.rule
-rw-r--r-- 1 root root  55346 Jun 19 06:20 unix-ninja-leetspeak.rule
```

It is always better to try using these rules before going ahead and creating custom rules.

`Hashcat` provides an option to generate random rules on the fly and apply them to the input wordlist. The following command will generate 1000 random rules and apply them to each word from `rockyou.txt` by specifying the "`-g`" flag. There is no certainty to the success rate of this attack as the generated rules are not constant.

#### Hashcat - Generate Random Rules

Working with Rules

```shell-session
underhill341@htb[/htb]$ hashcat -a 0 -m 100 -g 1000 hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```

There are a variety of publicly available rules as well, such as the [nsa-rules](https://github.com/NSAKEY/nsa-rules), [Hob0Rules](https://github.com/praetorian-code/Hob0Rules), and the [corporate.rule](https://github.com/sparcflow/HackLikeALegend/blob/master/old/chap3/corporate.rule) which is featured in the book [How to Hack Like a Legend](https://www.sparcflow.com/new-release-hack-like-legend/). These are curated rulesets generally targeted at common corporate Windows password policies or based on statistics and probably industry password patterns.

On an engagement or password cracking exercise, it is generally best to start with small targeted wordlists and rule sets, especially if the password policy is known or we have a general idea of the policy. Extremely large dictionary files combined with large rule sets can be effective as well. Still, we are limited by our computing power (i.e., a laptop with a single CPU will not be able to run `Hashcat` jobs with as large a word list and rule set as a password cracking rig with 8x 2080ti GPUs). Understanding the power of rules will help us greatly refine our password cracking abilities, saving both time and resources in the process.