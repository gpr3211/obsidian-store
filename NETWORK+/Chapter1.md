-  Hubs and switches connect devices together while routers connect networks
 *  Separate workgroups in order to reduce latency and increase security
 

 
## Common Network Components
### Workstations
Intermediary between servers and client machines. Client machines being printers, pcs. etc

### Hosts
 * Network devices including workstations and servers having an IP address
  Generally over TCP/IP


### VLAN ( virtual LANs)
![[Pasted image 20260416143731.png]]

Separates workgroups by ports/switches. Allows you to be anywhere on the physical network and still be local to the specific nework resources you need. 
Separation is done by VLAN membership determined by the specific port on the switch you are connected to.    

### WAN ( wide area network)
-  usually need router port or ports
-  span a wider geo area than a lan
 - connection is not permanent. We can choose time and type of conn. 
 -  Can utilieze private orpublic data transport media like phone lines.

1. I LAN hosts use hardware addresses to communicate while in WAN they use IP addresses( logical address)
![[Pasted image 20260416143710.png]]

### VPN
VPN makes your local host part of the remote network using the WAN link that conencts you to the remote LAN
Similar to VLAN but for connection over WAN not LAN.
![[Pasted image 20260416153643.png]]
A VPN in other words allows you to connect to a VLAN over WAN instead of LAN.
Much more secure than  opening the server to the internet in order to conn over large distances.

## Network Architecture P2P &  Client/Server
### p2p
![[Pasted image 20260416153941.png]]
- A p2p network doesnt have a central authority. Meaning its up to the computer that has the requested resources to do the security checks. Ok for small networks, but insecure for larger ones since each client has to own the required credentials.

### Client/server
![[Pasted image 20260416154419.png]]

## Physical Network Topologies
- BUS
- Star
- Ring
- Mesh
- point to point
- point to multipoint
- Hybrid
### Bus
![[Pasted image 20260416182353.png]]
 - bad fault tolerance
 - cheap/easy to install

### Star Topology



![[Pasted image 20260416184627.png]]
- Easy to add new stations
- single cable failure doesntbrinddowntheentirenetwork.
- easytotroubleshoot
-  Single point of failure
### Ring
![[Pasted image 20260416190118.png]]
Outdated due to high cost and hard reconfiguration
Bad fault tolerance
Still used by some ISP (SONET) 
### Mesh
- Has a path from each machine to every other machine
![[Pasted image 20260416191748.png]]
- every node is connected to every other node, n(n-1)/2 number of connections
- only suited for small networks
### Point To Point 
- T1 means WAN point to point connection
- rings with in-out connection indicate routers
- A common version of this setup consists of a direct wireless link between
two wireless bridges that’s used to connect computers in two different buildings together.

![[Pasted image 20260416191920.png]]
Lighting bold arrow indicates connection over WAN( industry standard notation)

### Point-to-Multipoint 

![[Pasted image 20260416192237.png]]