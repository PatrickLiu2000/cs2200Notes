**Ch13: Fundamentals of Networking and Network Protocols**
- how do computers communicate with one another?

# Preliminaries
- goal is to focus on networking from perspective of hardware and OS points of view
- **socket** library provides API for developing network apps
  - implementing this API is first step
- **protocol stack** - abstraction that addresses networking issues
- **network device driver** - interacts with network interace card to connect to network

# Basic Terms
- **host** - computer that hooks onto network
- **NIC(network interface card**- peripheral controller
- "the network" is simply a composite of several networks
- what is the internet?
  - analogy: sending letter to grandma in india
    - write letter and put into mailbox, mailperson picks up letter
    - letter is sent to regional post office, which are sorted and sent to their respective regional post office destinations, which is finally sent to grandma in India
  - writing an email to mom in California
    - computer plugs into modem, and the modem connects to an ISP (internet service provider), which is a local cable company
      - since this is the access point to the internet, it is an access network
    - access network is like mailwoman - doesn't know how to get email to Cali, but knows how to send it to the regional ISPs
    - regional ISP knows how to talk to other regional ISPs across country/world
    - Mom's access point would be the final destination of email
- ISP systems are not directly connected to one another 
  - **routers** - sit in core on network and route messages accordingly
    - how does it know where to send?
      - like zipcode for mailing, networks use IP address
  - internet is network of networks
- **packets** - unit of data that is routed from origin to destination
  - traverses seres of queues at routers' input from source to destination
  - if heavy network traffic, then paacket can experience queueing delays, and if router is full, packet can be dropped, leading to **packet loss**
- **edge of network** - laptops, computers, servers
- **core of network** - machines that route from source to destination
- **LAN(local area network)** - connecting computer to school/office network
# Networking Software
- in OS, this is the protocol stack
- **network protocol** - language that defines syntax and semantics of messages for computer to interact w/ each other
- there is a maximum packet size limit for different technologies, as well as maximum transmission rate
  - if data is too big, it needs to be split into several packets
  - also, no guarantee that packets will arrive in same order they are sent (out of order delivery)
  - queueing delays also another problem
  - packet loss
  - bit errors in data 
1. Arbitrary message size and physical limitations of network packets
2. Out of order delivery of packets
3. Packet loss in the network
4. Bit errors in transmission
5. Queuing delays en route to the destination
- protocol stack addresses these problems
# Protocol Stack
- protocol layering - abstracts and separates certain concerns to different layers of stack
  - outgoing messages are pushed down stack
  - incoming messages are popped up

![](./images/ch13/0.PNG)
- **application layer**
  - supports network-based applications
  - examples: HTTP, SMTP, FTP
- **Transport layer**
  - responsible for taking application layer message nad moving it between ends of communication
    - breaking message into packets
  - TCP/UDP are dominant transport protocols
    - TCP (Transmission Control Protocol)- reliable, and in-order delivery of data using a byte stream
      - connection oriented - establishes connection between 2 endpoints, and tears it down once the connection is closed
    - UDP(user datagram protocol) - like sending call through US mail
      - messages that have strict boundaries (no relationship at protocol level between successive messages)
      - no connection is established/no teardown before/after sending message, and messages can arrive out of order
- **Network Layer**
  - responsibility to route the packets from transport layer
  - when sending packet, network layer finds a way to get packet to destination address
  - when receiving packet, passes packet up to transport layer
  - use IP to format information in the packet 
- **Link layer**
  - sending mail analogy - airplane, mailcar, are examples of different vehicles used to transport mail
  - Link layer is similar - provides protocol on how to move packet between nodes on internet
  - network layer gives link layer IP packet
  - link layer delivers fragments of IP to then next node, where the data is passed up to the network layer and repeated until it reaches the destination
  - many different link layer protocols
- **Physical layer**
  - physically moves the bits from one node to next
- example message sending with protocol stacks
![](./images/ch13/1.PNG)
- layering abstraction has many benefits
  - separation of responsibilities
  - makes it easy to add/change modules in a layer without changing other layers

## OSI Model
![](./images/ch13/2.PNG)
- adds presentation and session layers
  - session - manager of a specific communication session between 2 endpts
  - presentation - formats text input and does character conversion
## Practical Issues with Layering
- real implementations do not stick with OSI model
- TCP/IP protocol is already standard for transport/network layers, thus many existing protocols combine many layers of the OSI model
# Application Layer
- network app has 2 parts
  - client - sits on end devices like laptops and phones
  - server - part that provides the functionality of a network service (search engine)
  - examples of network apps: WWW, email
- different app layer protocols for different types of apps
  - HTTP - web clients and servers
  - SMTP - email
- protocols can transcend hardware architecture, and are independent of the platform
  - example: HTTP, SMTP
- OS can also provide own network services
  - example: UNIX has socket library for building network apps
# Transport Layer
- assume transport has API:
  - `send (destination-address, data)`
  - `receive (source-address, data)`
- functionality of transport layer
  - support arbitrary data size at app level
  - support in-order delivery of data
  - shield app from loss of data
  - shield app from bit errors in transmission
- 2 views of data:
  - byte stream
    - TCP
  - message
    - UDP
- must separate data into packets to comply with network, as well as assemble the packets to deliver in order
  - called **scatter/gather**
- need a **sequence number** to determine order of message
  - each packet gets a sequence number, so solves the out of order and arbitrary message size problem
- source needs confirmation that destination received packets, and that none are lost
  - positive acknowledgement
![](./images/ch13/3.PNG)
- **round trip time** - time taken for small message to be sent from sender to receiver then back to sender
  - estimates delay experienced to send and receive acknowledgement, set timeout values accordingly
## Stop and Wait Protocols
- simple approach
  - sender sends packet and wait for positive ACK
  - once packet is received, recipient generates and sends an ACK for the packet
    - contains info for the sender to show that the packet is acknowledged (Seq #)
  - sender wait for period of time called **timeout**. If within this period it doesn't get an ACK, then it retransmits the packet
- need sequence numbers still because ACK packets can be lost as well, so may need to resend
  - seq # lets sender know if incoming ACK is current or duplicate ACK
- since at a given time there is only 1 packet in transit, we don't need an infinitely increasing # to be our sequence number
  - simply have an alternating bit from 0 to 1 (alternating bit protocol)
- timeout value must bee greater than round trip time
## Pipelined Protocols
- stop-and-wait is very slow
  - lots of **dead time** (no activity on network)
- if network is reliable, we can pipeline packets without waiting for ACKs
- bandwith - amount of time it takes host to place packet on wire
- propogation time - end-to-end delay for packet to reach destination
- need for ACKs depends on if protocol requires reliable transport
## Reliable Pipelined Protocol
- another approach is to pipeline sending of packets with receiving ACKs
  - source sends set of packets (window) before expecting ACK
  - destination acknowledges each packet individually
  - sender doesn't have to wait for ACKs before sending again
![](./images/ch13/4.png)
- size of window changes based on network congestion

### Network Congestion
- analogy: highway in rush hour
  - several feeder streets that are putting traffic on highway, and highways can also merge
    - streets are putting more cars on the highway than can be sustained
- ex) four 1 gbps network flows going into one 10 gbps network flow
  - but if there are 20 flows, there will be congestion
- congestion causes buildup of packet queues at routers
  - queueing delays and once queues become full, packet loss occurs
- ways of dealing with congestion
  - self-regulate amount of data it hands to network layer depending on congestion
    - problem: no guaranteed upper bound for delay
### Sliding Window
- mechanism for self-regulation in a transport protocol that incorporates congestion control
  - window size restricts sender's rate; prevents buildup at router queues
- for a given window size, we can define an active window of sequence numbers corresponding to set of packets that source can send without waiting for ACKs
- how width of packet is decided
  - ratio of packet size to bandwith, and how many packets we can fit within 1 round trip time
- window size can be chosen to be smaller than upper bound due to congestion
  - represents max number of packets that can be outstanding
- as ACKs for packets are received, window moves to the right by 1 packet
![](./images/ch13/5.png)
- to reduce number of ACK packets, will send a cumulative ACK
  - when receiving n packets with consecutive sequence numbers, will send single ACK - sender knows upon receiving single ACK that all n packets have been acknowledged
- for ACK packet loss, have timeout window when packets are initially sent, and will resend them if no ACK received within timeout window
- additional things to consider
  - choosing timeout values
  - choosing window size
  - out-of-order packets
  - when to ACK packets
  - when to move window
## Dealing With Transmission Errors
- to identify "good" from "bad" packet, source computes a **checksum**, value based on contents of packet and appends it to end of packet
  - destination does same computation to make sure the packets match
  - can potentially repair errors, but most likely will have to retransmit packet
## Transport Protocols on Internet
- **TCP (transmission control protocol)** - connection oreinted protocol
  - sets up connection between 2 endpoints
  - once connection is set up, data flow is a stream of bytes
  - does not deal with messages 
  - once connection is made, client sends requests, then server responds by sending series of objects that form webpage
  - tear down connection once done
  - **full duplex** - both sides can simultaneously send/receive data once connection is set up
  - *connection setup*: both sides negotiate initial sequence number for transmission
    - client sends server connection request message with info on initial sequence number for packets
    - server sends ACK to connection request with info on initial sequence number for packets
    - client allocates resources and sends ACK; once server receives ACK, server allocs resources for connection
    - client and server have officially established connection and can exchange data
  - *reliable data transport*: both endpts can send and receive data, and will have a guarantee that data will be sent without loss/corruption
  - *congestion control*: sender self-regulates network flow by adjusting window size
  - *connection teardown*
    - client sends teardown request, server sends ACK
    - server sends own teardown request, client sends ACK
    - client de-allocos resources for connection, and server does the same
    - client or server can initiate teardown
- **UDP**
  - does not have as much overhead/guarantees as TCP, results in it being faster
  - applications like VOIP prefer speed over packet loss, therefore UDP is a good fit
  - packets can be out of order/lost, and no self-control so can have problems with congestion
![](./images/ch13/6.png)
![](./images/ch13/7.png)
# Network Layer
- send packet handed to it at source from transport later to destination, and pass up incoming packet to transport layer at destination
- combining this functionality with transport layer not a good idea
  - many different types of network connections (wired/wireless)
  - data might need to go through several intermediate nodes to get to destination
    - in addition, these nodes simply need to forward packet to destination
  - allows insertion/deletion of network connections to be independent of transport
- network layer responsible for forwarding packet given destination address
  - maintains a routing table that contains a route from source to destination host
  - upon receiving a packet, either use the table to forward packet to destination, or pass up packet to transport layer if packet is at the destination
- key functionality needed
  - routing algorithms
  - service model: forward incoming packet on incoming link to outgoing link based on routing information
    - also called **switching**
- hardware that performs functionalities of network layer is called **router**
  - like an end host, except knows packet's destination
## Routing Algorithms
- **link state** - cost associated with neightboring links at a given host node
  - like edge lengths within a graph
- **Link state-dijkstra's**
  - least cost path, but requires complete information on edge costs
  - also needs to be synchronous
  - just look at 1332 notes :)
- **Distance vector algorithm**
  - async, and works with partial knowledge
  - node has to send packet to one if its neighbors - simply choose least cost one with regards to destination
  - each node maintains distance vector table
    - table has lowest cost route of sending packet to every destination in network through each of its neighbors
    - name comes from each node having vector of costs for reaching destination through its neighbors
  - psuedocode:
    - each node sends its least cost route to given destination to its neighbors, and each node updates its DV table
      - recomputes DV table entries if:
        - node observes change in link-state for any of its neighbors
        - node receives an update on least cost route from neighbor
- **Hierarchical Routing**
  - due to scale of internet, hierarchy approach is appropriate
  - organize routers on internet into regions called autonomous systems 
    - routers within the autonomous system may run link state or DV algorithms for routing hosts within the system
    - routers that communicate with destinations outside system are called **gateway routers**
      - use border gateway protocol (BGP)
![](./images/ch13/8.png)
![](./images/ch13/9.png)
  - host connected to C.1 communicating with host connected to C.2
    - simply use intra-AS protocol
  - if A.4 needs to send packet to host at B.3
    - needs to send packets to A.G, and routing table will tell packet to be sent to B.G
    - A.G uses BGP to communicate with B.G
    - then use intra-AS protocol to send to node B.3
## Internet Addressing