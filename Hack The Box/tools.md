 ofgo install -v github.com/nullt3r/udpx/cmd/udpx@latest
gobuster
gowitness https://github.com/sensepost/gowitness 
hydra
medusa
johntheripper https://github.com/openwall/john/blob/bleeding-jumbo/doc/INSTALL-UBUNTU
ike-scan


## hashing
 1. hashid
 2. hashcat

# john theripper
## install
==== Clone the Git repo

    cd ~/src
    git clone https://github.com/openwall/john -b bleeding-jumbo john

==== Build

    cd ~/src/john/src
    ./configure && make -s clean && make -sj4

## deps
	1. 7z2john 
```
sudo apt install libcompress-raw-lzma-perl
```
2. pdf2john.pl
3. 

# Wireless

1. needs network .cap file
## Aerodump
https://www.aircrack-ng.org/doku.php?id=install_aircrack
```
https://www.aircrack-ng.org/doku.php?id=airodump-ng
```
Convert cap file to something usefed by hashcat
```shell-session
underhill341@htb[/htb]$ git clone https://github.com/hashcat/hashcat-utils.git
underhill341@htb[/htb]$ cd hashcat-utils/src
underhill341@htb[/htb]$ make
```
## hcxtools
```
sudo apt update
sudo apt install libcurl4-openssl-dev

```

```
git clone https://github.com/ZerBea/hcxtools.git
cd hcxtools

```
# sql payloads
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass
