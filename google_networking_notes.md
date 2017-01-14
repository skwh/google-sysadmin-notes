Should address:

* TCP/IP, UDP, ICMP, MAC addresses, IP packets, DNS, OSI layers, load balancing
* deeper dive into TCP, three way handshake, blocking, wait times - TCP/UDP, load balancing algorthims, DNS
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

#### 5. Session

#### 6. Presentation

#### 7. Application
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

Take, for example, the journey of a packet between two hosts, H1 and H2.

The packet starts at Layer 4 (Application) as some HTML data.

`Data`

It is then moved to Layer 3 (Transport) and prefixed with a Transport Header, which contains the source port of the original host (H1) and the destination port for the recieving host (H2). The data now looks like this:

`1777 | 80` - `Data`

This new form of the data is called a segment.

The segment is sent to Layer 2 (Network), and there another header is added, which contains the source IP of H1 and the destination IP of H2. The data now looks like this:

`145.23.43.1 | 175.22.45.53` - `1777 | 80` - `Data`

This new form of the data is called a datagram.

The datagram is sent to Layer 1 (Link) and affixed with another header, which contains the Hardware Source Address, or MAC address, of H1, and the MAC address of the first router along the path (R1). The data now looks like this:

`a4:5e:60:f3:c9:81 | b4:11:97:e1:d2:12` - `145.23.43.1 | 175.22.45.53` - `1777 | 80` - `Data`

This new form of the data is called a frame.

The frame is finally converted to bits, and sent over the wire (Layer 0, for instance) to the first router.

At the first router, the router removes the frame header, and then examines the datagram header and sees which router is the next hop on the journey to H2. So it adds a new frame header to the data, with the destination MAC address of the next router, and sends the data over the wire to the next router.

This repeats as the data takes its journey to H2.

When the data arrives at H2, H2 first checks to see that the frame's destination MAC address is its own. It then does the same for the datagram destination IP at the Network layer. It then passes the data to the segment destination port in the Transport layer, and the correct application recieves the data.

The application, the HTTP process, then shows the data: an HTML page.

### TCP Three-way handshake

`SYN` -> `SYN-ACK` -> `ACK`

## UDP

UDP stands for User Datagram Protocol.
Versus TCP, it sacrifices reliablility for speed.
