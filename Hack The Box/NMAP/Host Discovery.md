When we need to conduct an internal penetration test for the entire network of a company, for example, then we should, first of all, get an overview of which systems are online that we can work with. To actively discover such systems on the network, we can use various `Nmap` host discovery options. There are many options `Nmap` provides to determine whether our target is alive or not. The most effective host discovery method is to use **ICMP echo requests**, which we will look into.

It is always recommended to store every single scan. This can later be used for comparison, documentation, and reporting. After all, different tools may produce different results. Therefore it can be beneficial to distinguish which tool produces which results.

```shell-session
 sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28

```
|**Scanning Options**|**Description**|
|---|---|
|`10.129.2.0/24`|Target network range.|
|`-sn`|Disables port scanning.|
|`-oA tnet`|Stores the results in all formats starting with the name 'tnet'.|

This scanning method works only if the firewalls of the hosts allow it. Otherwise, we can use other scanning techniques to find out if the hosts are active or not. We will take a closer look at these techniques in "`Firewall and IDS Evasion`".

---

## Scan IP List

During an internal penetration test, it is not uncommon for us to be provided with an IP list with the hosts we need to test. `Nmap` also gives us the option of working with lists and reading the hosts from this list instead of manually defining or typing them in.