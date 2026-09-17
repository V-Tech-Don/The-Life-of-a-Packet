# The Life of a Packet
This lab demonstrates how Cisco routers can be configured to resolve hostnames to IP addresses using a DNS server, and then use those hostnames to IP addresses using a **DNS server**, and then use those hostnames directly in commands such as **ping**. We will also explore how IP addresses are stored in the ARP cache of each router through network communication. 

<img width="1912" height="876" alt="(1) Topology" src="https://github.com/user-attachments/assets/a31668cf-e27b-4760-b556-fe4b2c92a6ee" />
| **Device** | **Interface** | **IP Address** |
|------------|---------------|----------------|
| DNS-Server | F0            | 10.10.10.10    |
| R1         | F0/0          | 10.10.10.1     |
| R2         | F0/2          | 10.10.10.2     |
| R2         | F1/0          | 10.10.20.2     |
| R3         | F0/0          | 10.10.20.1     |

---

**Connections**
  - **DNS-Server - SW1 (F0/3)**
  - **SW1 (F0/1) - R1 (F0/0)**
  - **SW1 (F0/2) - R2 (F0/2)**
  - **R2 (F1/0) - SW2 (F0/2)**
  - **SW2 (F0/1) - R3 (F0/0)**

---

**Objective**
Configure **R1, R2,** and **R3** to use the DNS server at **10.10.10.10.** for name resolution, then verify that each router can **ping** the others **by hostname (R1, R2, R3)** instead of by IP address. 

---

**Configuration**
The same two commands are applied on **R1, R2,** and **R3** to enable DNS lookups and point them to the DNS server. 
<img width="1267" height="933" alt="(4) R3 ip domain lookup" src="https://github.com/user-attachments/assets/695d14ab-a519-49f5-be31-cd96b1a6d3a2" />
<img width="1263" height="931" alt="(3) R2 ip domain lookup" src="https://github.com/user-attachments/assets/c6e082aa-899f-4175-8101-cb236a821773" />
<img width="1265" height="930" alt="(2) R1 ip domain lookup" src="https://github.com/user-attachments/assets/0f17b404-c6c4-4703-9c2c-aec03694710b" />
**Note: ping <hostname>** cannot be entered while still in **(config)** mode - it must be run from privileged EXEC mode **(Router#)**, otherwise you'll see:
R1(config) #ping R2
% Invalid input detected at '^' marker.
---

**Verification - Ping by Hostname**
**From R1**
<img width="1262" height="928" alt="(5) Ping from R1" src="https://github.com/user-attachments/assets/4dc0b7dc-0892-4480-a57b-278f7ae08fc0" />
**From R3**
<img width="1263" height="933" alt="(6) Ping from R3" src="https://github.com/user-attachments/assets/b7f4e78d-24f7-473b-b465-8eefcd50c90c" />
Both Routers successfully translate the hostnames to an IP address via the DNS server (10.10.10.10) before sending the ICMP echo requests, confirming DNS resolution is working correctly. ARP requests use broadcast traffic so they are not forwarded by a router. R1 will have entries in its ARP cache for all hosts it has seen **on its directly connected networks** (10.10.10.0/24). 

R1 is not directly connected to the 10.10.20.0/24 network so it will not have an entry in the ARP cache for R3 at 10.10.20.1.

R1 can reach R3 via R2's IP address 10.10.10.2-this IP address is included in the ARP cache. 
---

**ARP Tables**
After the pings, each router's ARP cache shows the MAC-to-IP mappings learned across the network.

**R1-show arp**
<img width="1050" height="117" alt="(7) R1 Show ARP " src="https://github.com/user-attachments/assets/e72162c9-1891-42d0-92ef-6d850144b97f" />
**R2-show arp**
<img width="1110" height="112" alt="(8) R2 Show ARP" src="https://github.com/user-attachments/assets/a921d39b-b93f-42d8-9c87-1ad6bf08e96b" />
**R3-show arp**
<img width="1458" height="65" alt="(9) R3 Show ARP" src="https://github.com/user-attachments/assets/4171c4b8-4867-49e0-82de-a5bd9e189f64" />

This confirms that configuring a router with **ip domain-lookup** and **ip name-server <DNS-IP>** allows it to resolve hostnames dynamically, simplifying network testing and management. 
