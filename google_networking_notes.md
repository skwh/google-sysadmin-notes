Should address:

* TCP/IP, UDP, ICMP, MAC addresses, IP packets, DNS, OSI layers, **load balancing**
* deeper dive into TCP, three way handshake, **blocking, wait times** - TCP/UDP, **load balancing algorthims, DNS**
* description of internet protocols

# Networking notes

## OSI Model

A theoretical model for how data should be organized in a network system.

>"[OSI] is a conceptual model that characterizes and standardizes the communication functions of a telecommunication or computing system without regard to their underlying internal structure and technology." - Wikipedia

Each layer has a PDU, or a "protocol data unit." A PDU is the smallest form data takes in each layer. PDUs are often passed between layers.

### Layers

The layers can be remembered with the mnemonic "Please Do Not Throw Sausage Pizza Away."

#### 1. Physical

Literally the wires which the bits are transmitted on. Also refers to the electrical and physical specifications of the wires and their connections.

>"Transmission and reception of raw bit streams over a physical medium." - Wikipedia

PDU = Bit

#### 2. Data

Provides "node to node transfer" - a link between two directly connected nodes.

Specifies the protocols for establishing and terminating connections between nodes, as well as flow control (the rate of data transmission between nodes).

Technically, divided into two sublayers:

The Media Access Control layer, or MAC: determines how devices gain access to a network and permission to transmit on it.

The Logical Link Control layer, or LLC: identifies Network layer (3) protocols and encapsulates them.

PDU = frame

#### 3. Network

>"The network layer provides the functional and procedural means of transferring variable length data sequences (called datagrams) from one node to another connected to the same "network". A network is a medium to which many nodes can be connected, on which every node has an address and which permits nodes connected to it to transfer messages to other nodes connected to it by merely providing the content of a message and the address of the destination node and letting the network find the way to deliver the message to the destination node, possibly routing it through intermediate nodes. If the message is too large to be transmitted from one node to another on the data link layer between those nodes, the network may implement message delivery by splitting the message into several fragments at one node, sending the fragments independently, and reassembling the fragments at another node. It may, but need not, report delivery errors." - Wikipedia

PDU = packet

#### 4. Transport

>"The transport layer provides the functional and procedural means of transferring variable-length data sequences from a source to a destination host via one or more networks, while maintaining the quality of service functions." - Wikipedia

TCP is an example of a Transport Layer protocol.

PDU = Segment (For TCP) / Datagram (For UDP)

#### 5. Session

>"The session layer controls the dialogues (connections) between computers. It establishes, manages and terminates the connections between the local and remote application. It provides for full-duplex, half-duplex, or simplex operation, and establishes checkpointing, adjournment, termination, and restart procedures. The OSI model made this layer responsible for graceful close of sessions, which is a property of the Transmission Control Protocol, and also for session checkpointing and recovery, which is not usually used in the Internet Protocol Suite. The session layer is commonly implemented explicitly in application environments that use remote procedure calls." - Wikipedia

PDU = data

#### 6. Presentation

Takes data from the other layers and formats it for the application layer.

Provides independence from data representation, for example in the context of encryption.

PDU = data

#### 7. Application

The layer which interfaces with a program that implements a "communicating component"- something that must communicate over a network.

For example, a web browser is mostly a communicating component- it relies heavily on its ability to send and recieve data over the network. A text editor, by contrast, has no need for a network connection, and so has no communicating component.


------

## IPS or TCP/IP

IPS stands for "internet protocol suite." Also called TCP/IP for its most popular protocols. IPS addresses how data should be packetized, addressed, transmitted, routed, and recieved. Unlike the OSI model, IPS has actually been implemented and is widely used.

### Layers & OSI Relation

The OSI's 7 layers roughly correspond to the IPS' four:

1. Link Layer - OSI 1 & 2

2. Internet layer - OSI 3

3. Transport layer - OSI 4

4. Application layer - OSI 5, 6, & 7

### Example packet journey

Take, for example, the journey of a packet between two hosts, `H1` and `H2`.

The packet starts at Layer 4 (Application) as some HTML data.

`Data`

It is then moved to Layer 3 (Transport) and prefixed with a Transport Header, which contains the source port of the original host (`H1`) and the destination port for the recieving host (`H2`). The data now looks like this:

`1777 | 80` - `Data`

This new form of the data is called a segment.

The segment is sent to Layer 2 (Network), and there another header is added, which contains the source IP of `H1` and the destination IP of `H2`. The data now looks like this:

`145.23.43.1 | 175.22.45.53` - `1777 | 80` - `Data`

This new form of the data is called a datagram, or a packet.

The datagram is sent to Layer 1 (Link) and affixed with another header, which contains the Hardware Source Address, or MAC address, of `H1`, and the MAC address of the first router along the path (`R1`). The data now looks like this:

`a4:5e:60:f3:c9:81 | b4:11:97:e1:d2:12` - `145.23.43.1 | 175.22.45.53` - `1777 | 80` - `Data`

This new form of the data is called a frame.

The frame is finally converted to bits, and sent over the wire (Layer 0, for instance) to the first router.

At the first router, the router removes the frame header, and then examines the datagram header and sees which router is the next hop on the journey to `H2`. So it adds a new frame header to the data, with the destination MAC address of the next router, and sends the data over the wire to the next router.

This repeats as the data takes its journey to `H2`.

When the data arrives at `H2`, `H2` first checks to see that the frame's destination MAC address is its own. It then does the same for the datagram destination IP at the Network layer. It then passes the data to the segment destination port in the Transport layer, and the correct application recieves the data.

The application, the HTTP process, then shows the data: an HTML page.

## IP

IP stands for "Internet Protocol". Its task is to address and deliver data soley based on its IP address.

IP uses packets or dataframes as its PDU. IP packets consist of two parts: a header and a payload. There are two versions of IP, IPv4, which uses 32 bit addresses consisting of four binary octets, and IPv6, which uses 128 bits consisting of eight groups of four hexadecimal digits.

IPv4 addresses are typically written in "dot decimal" format, as follows:

`172.16.254.1`

But this address can also be expressed in other forms, such as its octal:

`10101100 00010000 11111110 00000001`

### IPv4 Header Structure

The IPv4 packet header is 32 octets (bit-words) long and consists of 14 fields, 13 of which are required. (The 14th is aptly named `options`.)

1. The first four bits of the first octet is the version, for IPv4 this is always "4".

2. The four bits of the first octet is called the Internet Header Length, which specifies how long the header will be.

3. The first six bits in the second octet is called the Differentiated Services Code Point, and is mainly used by DiffServ applications, such as Voice over Ip (VoIP).

4. The last two bits of the second octet is for Explicit Congestion Notification (ECN), which "allows end to end notification of network congestion without dropping packets." Only used if both parties and the underlying network support it.

5. The third and fourth octets, making up 16 bits, are home to the Total Length field, which defines the entire packet size. The maximum packet size is therefore 65,535 bytes, but all hosts are only required to reassemble 576 byte datagrams.

6. The fifth and sixth octets are for the Identification field, which is used to identify and group fragments of a datagram.

7. The next three bits of the seventh octet are fragmentation flags. Bit 0 is always 0. Bit 1 = don't fragment (DF), and Bit 2 = more fragments (MF).

8. The next 13 bits of the seventh and eighth octets are for the Fragment Offset field. This specifies how offset this fragment is from the original IP datagram.

9. The ninth octet is the packet's Time to Live, which prevents packets from going in circles. Times less than 1 second are generally rounded up. In practice, TTL is used as a hop count, with routers decrementing a packet's TTL each time it reaches a router. When the TTL is zero, the router sends an ICMP Time Exceeded message to the origin.

10. The tenth octet is the packet's protocol flag. It defines the protocol used in the data portion of the packet.

11. The eleventh and twelveth octets are for a checksum which does error checking.

12. Octets 12-15 are for the source IP address, and

13. octets 16-19 are for the destination IP address.

14. Octets 20-32 are for the options, which are generally not used.

## ARP

ARP stands for "Address Resolution Protocol".

## IGMP

IGMP stands for "Internet Group Management Protocol".

## ICMP

ICMP stands for "Internet Control Message Protocol".

ICMP isn't used to communicate data, but rather the status of different devices on the network, such as error messages or other operational information.

Few end-user network applications interact with ICMP, with the exception of diagnostic tools like `ping` and `traceroute`.

### Structure

ICMP packets are encapsulated in IPv4 packets. They consist of two sections: header and data.

The first four octets of the header have special meanings:

1. Octet "0": Type. General type of message being transmitted.
2. Octet 1: Code. The specific type of message being transmitted.
3. Octets 2 & 3: Checksum. Error checking data.

For example, the first two octets of an ICMP header might look like this:

`00000011 00000111` = 3 / 7

Where Type 3 refers to a "Destination Unreachable" type of message and Code 7 means specifically "Destination host unknown."

The rest of the ICMP packet consists of data or the body. This is usually a copy of the IPv4 header and first eight bytes of data from the IPv4 packet that caused the error.

So, for example, if `H1` sends a packet to an unreachable destination, the ICMP packet which responds to it would consist of the appropriate ICMP header and some of the IPv4 data for the original packet.

## TCP

TCP stands for "Transmission Control Protocol". It sacrifices speed for reliability.

TCP has several features that set it apart from UDP:

1. Ordered data transfer: packets have sequence numbers, and are put in order when they are delivered.
2. Retransmission of lost packets: if a packet is lost on its way to the destination, the recieving computer may ask for a replacement.
3. Flow control: limits the rate a sender transfers data to guarantee reliable delivery. With each acknoledgement, the reciever tells the sender how much data can be sent.

### TCP Three-way handshake

TCP uses a three-way handshake to initiate a connection.

`SYN` -> `SYN-ACK` -> `ACK`

### TCP Headers

TCP headers consist of 10 mandatory fields, and an 11th "options" field.

1. Source port: 16 bits
2. Destination port: 16 bits
3. Sequence Number: 32 bits, either the intitial sequence number (when starting a connection) or otherwise the accumulated sequence number. Depends on the SYN flag being set.
4. Acknoledgement Number: 32 bits, if ACK is set then the value of this field is the next sequence number that the sender is expecting
5. Data offset: 4 bits, Specifies the size of the TCP header
6. Reserved: 3 bits, set to zero
7. Flags: 9 bits, contains nine 1 bit flags:
  * NS: ECN-nonce concealment protection (experimental)
  * ECE: ECN-Echo, meaning depends on other flags:
    * If SYN is set, the TCP peer is ECN capable
    * If SYN is not, a packet with Congestion Experienced flag (IPv4 header) was recieved
  * CWS: Congestion Window Reduced, shows that an ECE flagged-packet was received and responded with a congestion control mechanism
  * URG: indicates that the Urgent pointer field is significant
  * ACK: indicates that the Acknoledgement field is significant, all packets after the initial SYN packet should have this field set
  * PSH: Push function. Asks to push buffered data to the recieving application.
  * RST: reset the connection
  * SYN: synchronize sequence numbers
  * FIN: last package from sender
8. Window size: 16 bits, specifies the size of the recieve window that the sender of this segment is willing to recieve
9. Checksum: 16 bits, checksum for error-checking
10. URG Pointer: 16 bits, If URG flag is set, this is an offset from the sequence number indicating the last urgent byte.
11. Options

The header always ends with some padding to ensure that it rests on a 32-bit boundary.

## UDP

UDP stands for "User Datagram Protocol".
Versus TCP, it sacrifices reliablility for speed.

## HTTP

HTTP stands for "HyperText Transfer Protocol".

## DNS

DNS stands for "Domain Name System".

>"[DNS] is a hierarchical decentralized naming system for computers, services, or any resource connected to the Internet or a private network. It associates various information with domain names assigned to each of the participating entities. Most prominently, it translates more readily memorized domain names to the numerical IP addresses needed for the purpose of locating and identifying computer services and devices with the underlying network protocols." -Wikipedia

Uses different types of "records" to hold the associations between IP addresses and domain names.

DNS uses UDP to execute its queries against various domain name servers.

### Record types

`A`: Used to associate IPv4 addresses with domain names.

`AAAA`: Used to associate IPv6 addresses with domain names.

`MX`: Used for email records.

`NS`: Used for other DNS name servers.

`PTR`: reverse DNS lookups

`CNAME`: domain name aliases

## DHCP

DHCP stands for Dynamic Host Configuration Protocol.
