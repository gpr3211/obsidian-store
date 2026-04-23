 

## Layered Approach
A reference model is a conceptual blueprint of how comms take place. It addresses all the processes required for effective communication and divides these processes into logical groupigns called layers.
**Layered architecture** 

*PROS*
1. separate network comm process into smaller more manageable chunks.
2. Allows mutliple-vendor dev through standardization of network components.
3. Encourages industry standardization.
4. Separation of concerns.
![[Pasted image 20260418222559.png]]
## OSI Model
 - Open Systems Interconnection Reference Model
 ![[Pasted image 20260418222658.png]]
![[Pasted image 20260418222647.png]]
## L1 Physical
- Signaling , cabling, connectors

### Troubleshooting
- Cable issues, 

## L2 Data Link

1. Physical transmission of the data, handles error notification, network topology and flow control.









## L3 Network
Manages device addressing, tracks location of devices on the network and determines the best way to move data.
- Transports data between devices that are not local.

### Flow
1. packet is received on a router interface, destination IP is checked. If location is not current router, looks up destination address on routing table.
*Two types of packets are used on the network layer*
### 1)Data packets
Used to transport user data through the internetwork. Protocols used to support data traffic are *routed protocols*
IP, IPv6.
### 2) Route update packets
- Used to update neighbouring routers about the networks connected to all routers around the network.
Uses *routing protocols*  EX.
1. ROuting Information Protocol  - *RIP, RIPv2*
2. Enchanced Interior Gateway Routing Protocol *EIGRP*.
3. Open Shortest Path First *OSPF*
![[Pasted image 20260420183938.png]]
### Network addresses
	 protocol specific network addresses. A router must maintain a routing table for individual R          protocols.

### Important router facts
	1. Routers by def wont forward broadcast/multicast packets.
	2. Routers use the logical address in a Network-layer header to determine the next hop router.
	3. Routers can use access lists created by admins to control security on the type of packets allowed in the network.
	4. Can provide L2 bridging functions if needed.
	5. L3 devices(routers in this case) provide connections between VLANs
	6. Can be referred as L3 Switch.

## L4 Transport
### TCP ( connection oriented communication)
1. *3-way handshake* “SYN,
SYN-ACK, SYN”, or synchronize, synchronize-acknowledgment, syncronize.
![[Pasted image 20260419190439.png]]

### Flow control
Data integrity is ensured at L4 by maintaing *flow control*.
1. Segments are delivered and acknowledged back to the sender upon receipt.
2. Any non-acknowledged segment is re-sent.
3. Segments are sequenced into proper order upon arrival.
4. managable data flow is maintaned in order to avoid congestion, overloading and data loss.

![[Pasted image 20260419194758.png]]
### Windowing
The time gap after sending data and before receiving acknowledgement is used to transmit more data.
The quantity of data segments in bytes that are allowed to be sent without an acknowledgement is called a *window*
Some protocols use segments as measurement for a window, TCP uses bytes.
![[Pasted image 20260419195726.png]]
- **NOTE** if a receiving host fails to get all segments that should be acknowldge, host can decrease window size.
### Acknowledgements
- *Positive acknowledgement with re-transmission*

![[Pasted image 20260419200010.png]]

## L5 Session
Responsible for session management between L6 layers.
- coordinates communication between systems and organizes their comms by three modes
1. SImplex
2. Half Duplex
3. Full duplex
## L6 Presentation
- *presents data to the app layer*
-  responsible for data translation and code formatting.
-  Makes sure L7 of one machine can read the data sent from another machine's L7
- OSI has protocol standards for data format.
- Includes *compression, decompression. encryption and decryption*
## L7 Application
Communication between user and computer.
Not to be mistaken with the actual program/application, but the interface that communicates between it and the machine.
-  responsible for identifying and establishing the availability of the intended communication partner and determining whether sufficient resources exist.
- 

### *Devices used in an intranetwork
Devices operating at all 7 Layers
1. Network management stations.
2. Web/App servers
3. Gateways ( not default)
4. Network hosts
*Devices used at the Physical Layer
1. Network interface cards (NIC).
2. Transceivers
3. Repeaters
4. Hubs 
