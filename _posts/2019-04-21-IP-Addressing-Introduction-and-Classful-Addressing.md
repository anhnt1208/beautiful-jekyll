---
layout: post
title: IP Addressing - Introduction and Classful Addressing
subtitle: 
tags: [infrastructure]
---

A **classful network** is a network addressing architecture used in the Internet from 1981 until the introduction of **Classless Inter-Domain Routing (CIDR)** in 1993. 
The method divides the IP address space for **Internet Protocol version 4** (IPv4) into five address classes based on the leading four address bits.

## IP Addressing  
Each TCP/IP host is identified by a logical IP address.  
The IP address is a network layer address and has no dependence on the data link layer address 
(such as a MAC address of a network interfacecard).  
A unique IP address is required for each host and network component that communicates using TCP/IP.  
Each IP address is divided into two parts : **Network ID** and **Host ID**.	
- The **network ID** (also known as a network address) identifies the systems that are located on thesame physical network bounded by IP routers.  
All systems on the same physical network must havethe same network ID.  
The **network ID** must be unique to the internetwork.	 
- The host ID (also known as a host address) identifies a workstation, server, router, or otherTCP/IP host within a network. 

The address for each host must be unique to the network ID  
An IP address is 32 bits long.  
Rather than working with 32 bits at a time, it is a common practice tosegment the 32 bits of the IP address into four 8-bit fields called octets.  	
Each octet is converted to adecimal number (the Base 10 numbering system) in the range 0-255 and separated by a period (adot).  
This format is called **dotted decimal notation**.  
![](https://www.geeksforgeeks.org/wp-content/uploads/IP_addressing_1.jpg)

## Address Classes
### Class A
Class A addresses are assigned to networks with a very large number of hosts.  
The high-order bit in aclass A address is always set to zero.  
The next seven bits (completing the first octet) complete the network ID.  
The remaining 24 bits (the last three octets) represent the host ID.  
This allows for 126 networks and 16,777,214 hosts per network.  
![](https://miro-icmfxmvdkqz7ku.stackpathdns.com/wp-content/uploads/Intro-to-Networking-Part-2_2.png)

### Class B
Class B addresses are assigned to medium-sized to large-sized networks.  
The two high-order bits in aclass B address are always set to binary **1 0**.  
The next 14 bits (completing the first two octets)complete the network ID.  
The remaining 16 bits (last two octets) represent the host ID.  
This allows for16,384 networks and 65,534 hosts per network.  
![](https://miro-icmfxmvdkqz7ku.stackpathdns.com/wp-content/uploads/Intro-to-Networking-Part-2-3.png)

### Class C
Class C addresses are used for small networks.  
The three high-order bits in a class C address arealways set to binary **1 1 0**.  
The next 21 bits (completing the first three octets) complete the network ID.  
The remaining 8 bits (last octet) represent the host ID.  
This allows for 2,097,152 networks and 254hosts per network.  
![](https://miro-icmfxmvdkqz7ku.stackpathdns.com/wp-content/uploads/Intro-to-Networking-Part-2_4.png)

### Class D
Class D addresses are reserved for IP multicast addresses.  
The four high-order bits in a class D address are always set to binary **1 1 1 0**.  
The remaining bits are for the address that interested hostswill recognize.  
Class D does not posses any sub-net mask.  
Microsoft supports class D addresses for applications to multicast data to multicast-capable hosts on an internetwork.  

### Class E
class E addresses are reserved for experimental and research purposes.   
The high-order bits in a classE address are set to **1 1 1 1**.  
This class doesn’t have any sub-net mask.  

## Network ID Guidelines
The network ID identifies the TCP/IP hosts that are located on the same physical network.  
All hosts on the same physical network must be assigned the same network ID to communicate with each other  
- The network address must be unique to the IP internetwork. If you plan on having a direct routed connection to the public Internet, the network ID must be unique to the Internet.  
If you do not plan onconnecting to the public Internet, the local network ID must be unique to your private internetwork.
- The network ID cannot begin with the number 127. The number 127 in a class A address isreserved for internal loopback functions.  
- All bits within the network ID cannot be set to 1. All 1’s in the network ID are reserved for use as an IP broadcast address.  
- All bits within the network ID cannot be set to 0. All 0’s in the network ID are used to denote aspecific host on the local network and will not be routed.  

## Host ID Guidelines
The host ID identifies a TCP/IP host within a network.  
The combination of IP network ID and IP host IDis an IP address.  
Follow these guidelines when assigning a host ID:  
- The host ID must be unique to the network ID.
- All bits within the host ID cannot be set to 1, because this host ID is reserved as a broadcast address to send a packet to all hosts on a network.
- All bits in the host ID cannot be set to 0, because this host ID is reserved to denote the IP networkID.

| Class  	| Leading bits 	| Size of network  number bit field 	| Number of Networks 	| Hosts per Network    	| ID range                  	| Default subnet mask 	| CIDR notation 	|
|--------	|--------------	|-----------------------------------	|--------------------	|----------------------	|---------------------------	|---------------------	|---------------	|
| A      	| 0            	| 8                                 	| 128 (2^7)          	| 2^24 – 2 = 16,777,24 	| 0.0.0.0 ~ 126.0.0.0       	| 255.0.0.0           	| /8            	|
| B      	| 10           	| 16                                	| 16,384 (2^14)      	| 2^16 – 2 = 65,534    	| 128.0.0.0 ~ 191.255.0.0   	| 255.255.0.0         	| /16           	|
| C      	| 110          	| 24                                	| 2,097,152 (2^21)   	| 2^8 – 2 = 254        	| 192.0.0.0 ~ 223.255.255.0 	| 255.255.255.0       	| /24           	|

The number of addresses usable for addressing specific hosts in each network is always **2N - 2**,  
where *N* is the number of rest field bits, and the subtraction of *2* adjusts for the use of the all-bits-zero host portion for network address and the all-bits-one host portion as a broadcast address.  
Thus, for a Class C address with 8 bits available in the host field, the maximum number of hosts is 254.  

Today, IP addresses are associated with a subnet mask.  
This was not required in a classful network because the mask was implied by the address itself.   
Any network device would inspect the first few bits of the IP address to determine the class of the address and thus its netmask.  

The blocks numerically at the start and end of classes A, B and C were originally reserved for special addressing or future features  
- 0.0.0.0/8 and 127.0.0.0/8 are reserved in former class A.  
While the 127.0.0.0/8 network is a Class A network, it is designated for loopback and cannot be assigned to a network  
- 128.0.0.0/16 and 191.255.0.0/16 were reserved in former class B but are now available for assignment;   
- 192.0.0.0/24 and 223.255.255.0/24 are reserved in former class C.   

