Multi-Site Enterprise Network Architecture: Scalability & Zero-Trust Integration of My Town Media Solutions.

Executive Summary:

Designed and deployed a dual-site enterprise network (HQ and Branch) using a 3-tier hierarchical model for 99.9% availability.
Implemented secure site-to-site connectivity via GRE Tunnels and centralized internet access using PAT (Port Address Translation).
Enforced network-wide security through SSH management, Port Security, and Layer 3 Access Control Lists (ACLs)

I'm excited to share my first Packet Tracer network design with you. This project is a culmination of many hours dedicated to research, troubleshooting, and mentor collaboration. This project has been an incredibly enriching learning experience. Although the assignment is now complete, my desire for continuous improvement in network efficiency and security remains strong. I would appreciate any insights or suggestions you have. Thank you again for viewing my work.

The Problem:

Problem: Inconsistent IP management and potential IP exhaustion.
Solution: Engineered a precise addressing scheme using Variable Length Subnet Masking (VLSM), maximizing the efficiency of the 192.168.99.0/24 space.

Technical Highlights:

Routing: Optimized path selection using Single-Area OSPF to reduce CPU overhead while maintaining rapid convergence.
Switching: Implemented EtherChannel (PAgP) and Rapid-PVST+ to achieve sub-second failover and eliminate loops.
Automation/Scripting: If you used any Python for these configurations, highlight it as Infrastructure as Code (IaC).

IP Addressing and Scheme

Media Solutions is a small company with specific requirements for IP addressing, as listed below.

VLAN Sizing & Addressing
• Use 192.168.99.0/24 for VLSM addressing of all VLANs
• GRE tunnel will use 192.168.98.0/30
• Assign the first usable IP address as the gateway for each VLAN subnet

IP addressing and Scheme link: IP Addressing and Scheme for Media Solutions 

The subnetting for this network was meticulously implemented using Variable Length Subnet Masking (VLSM), driven by the diverse host requirements of each VLAN and the necessity for precise address assignment. In the absence of a pre-allocated ISP address, I engineered a suitable IP address for the WAN connection and GRE tunnels within the simulated wide area network. The server's IP address and its dedicated subnet were designed to accommodate its role in providing DHCP, TFTP, and HTTP services. Subnetting this way enabled full use of the 192.168.99.0/24 address space


Routing Protocol

Since both the Branch and HQ office used a GRE tunnel to connect to two LAN networks, as well as the overall size of the network, it was more efficient to use a single area instead of a multi-area ospf. Single area reduces the complexity and increases the efficiency of the network by easing configuration.

Switching and STP

HQ-SW1 was chosen to be the root, so the first step was creating an EtherChannel between the two switches. At first, I had a few complications configuring spanning-tree over the po1 link, I then linked a trunk port and moved it to native VLAN 1. I then used the spanning-tree vlan 1-100 root primary command in global configuration to make HQ-SW1 the root. I then enabled root guard to ensure that HQ-SW1 will remain the root. I then enabled port-fast Fast Ethernet 3 and 4 as they are connected to endpoints. On HQ-SW2, I also configured the etherchannel on Fast Ethernet 1 and 2 to match SW1 configuration, made po1 a trunk port and assigned it to native vlan 1, as well as enabled portfast on Fast Ethernet 3-6 that were connected endpoints. The EtherChannel was configured to use PAgP for its configuration. I also changed spanning-tree to rapid-pvst+ to reduce downtime and increase load balancing 

Nat and Internet Access

In the My Town Media Solutions network, I used Port Address Translation (PAT) over NAT to centralize internet access for both Headquarters and Branch office clients. The inside interfaces were all the sub-interfaces, route to server, as well as the GRE tunnel on both Routers. The ISP link on both Routers was set to be outside. This step up, combined with an ACL, permitted traffic from both the Headquarters and Branch networks, allowing all internal devices to share a single public address of HQ-RTR ISP interface for internet connectivity. The clients in the Headquarters will have their address translated directly from the HQ-RTR, while the Branch network clients will first have traffic travel over the GRE and then will be translated by the HQ-RTR for internet connectivity. 

Security 

SSH, or secure shell, is preferred for remote login because it is secure. For this reason, all Routers and switches were configured to be configured remotely using this method. Employing port security was also important as it prevents unauthorized access to the network, as well as restricts access to only known MAC addresses if a violation occurs via switches. On the routers, ACLs were used to control inter-vlan routing. I did not implement ACL to control which interface can SSH into the router or switches, but created users with certain privilege levels who can log into them via SSH.

Service and Access Control

Addresses were dynamically given out to different clients on each VLAN based on the number of hosts and using a DHCP server. At first, I had lots of issues setting up the server because of improper subnetting of the 192.168.99.0 network.
