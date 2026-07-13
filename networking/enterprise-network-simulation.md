### 07/12/2026

### The lab is based on a fictional company called TechCorp, which has three departments. The objective of this lab is to design, create, and configure the Local Area Network (LAN) for the internal departments and the Wide Area Network (WAN) connection to the internet. Since each department has a different number of users and end devices, I decided to use Variable Length Subnet Masking (VLSM) as a best practice. This approach allows each department to receive only the number of IP addresses it requires, along with a small number of additional unused addresses to accommodate potential future growth.


### I built this lab using Cisco Packet Tracer software, applying the techniques I learned while studying for the CCNA certification.

- The IP address range **192.168.10.0/24** was used for this network because it is a private IP address range and provides 254 usable host addresses, which is sufficient for this network. I used a text file to calculate the IP address allocation for each VLAN before applying the configuration. Additionally, I included two access switches because the company occupies two floors. This also follows best practices by improving network scalability and providing redundancy.

- I calculated the VLSM addressing scheme in a text file before configuring the network. Based on these calculations, I assigned IP addresses to the end devices and configured their default gateway as the last usable IP address in each VLAN. I also connected the end devices to the switches and documented all network connections, as shown in the diagram.

<img width="1842" height="841" alt="image" src="https://github.com/user-attachments/assets/3990c7b1-6530-47d0-bb47-38d98ffe54a5" />

- I used a Layer 3 (Core) switch to handle the inter-VLAN routing. On this switch, I first created 3 VLANs using the vlan command. I also created a native VLAN (VLAN99) and assigned it to all trunk ports so that any untagged traffic is directed to it. From a security perspective, this is considered best practice because untagged traffic on trunk links goes to the native VLAN by default, which is VLAN1. VLAN1 is not a good security practice since it is the default VLAN on all Cisco switches and is well known to attackers who can exploit it using VLAN hopping attacks. By changing the native VLAN to VLAN99 — an unused VLAN with no devices or ports assigned to it — any untagged traffic goes nowhere, effectively neutralizing this attack vector.
<img width="1090" height="177" alt="image" src="https://github.com/user-attachments/assets/f69cb97e-fd0b-4654-ada4-7eab03aecfd3" />
- I also used the same commands to create the VLANS on the other two access switches.

..

In the next step, the SVIs (Switch Virtual Interfaces) were configured on CORE-SW3. Since this switch handles inter-VLAN routing, I assigned the last usable IP address of each subnet as the gateway to its corresponding virtual interface. I also enabled the routing functionality on the switch using the ip routing command — without this command the switch cannot route traffic between VLANs even if the SVIs are configured. Finally, I used no shutdown on each SVI to bring them up, as they are administratively down by default.
<img width="1257" height="522" alt="image" src="https://github.com/user-attachments/assets/ede12761-5a86-4913-bd5b-c4a37be38781" />
<img width="1212" height="127" alt="image" src="https://github.com/user-attachments/assets/7bb75359-6a1d-4212-8495-21e85de496c8" />


..
"In the next step, I configured the access ports on both SW1 and SW2 by assigning each port to its corresponding VLAN . This ensures that each port only carries traffic for one specific VLAN, preventing traffic from leaking between departments. After configuring the access ports, I configured the uplink connections between the access switches (SW1 and SW2) and the Core switch (CORE-SW3) as trunk ports. Unlike access ports, trunk ports can carry traffic for multiple VLANs simultaneously over a single physical cable. This is necessary because both SW1 and SW2 have devices from multiple VLANs connected to them, and all this traffic needs to reach CORE-SW3 for inter-VLAN routing. I also configured VLAN99 as the native VLAN on all trunk ports

While configuring the trunk ports on CORE-SW3, the command switchport mode trunk was initially rejected with the error: 'An interface whose trunk encapsulation is Auto cannot be configured to trunk mode.' This is because the 3650 multilayer switch supports multiple trunk encapsulation protocols — specifically ISL and 802.1Q (dot1q). Unlike regular access switches which default to dot1q automatically, the multilayer switch requires you to explicitly specify the encapsulation type before setting the trunk mode. I resolved this by adding the command switchport trunk encapsulation dot1q before switchport mode trunk. This tells the switch to use 802.1Q encapsulation — the industry standard for VLAN tagging on trunk links — which is also the encapsulation used by all other devices in the network.

<img width="1871" height="875" alt="SW3" src="https://github.com/user-attachments/assets/e48bfe8e-9454-4034-bf0b-bdb13291a5ea" />
<img width="1871" height="875" alt="SW3" src="https://github.com/user-attachments/assets/9df4fd1f-183a-4e1e-86c4-1a90fd8f15fa" />
<img width="1871" height="875" alt="SW3" src="https://github.com/user-attachments/assets/281cc550-0075-4d8e-957c-23f3f067f48c" />


After completing these configurations, I performed a connectivity test using a PC in VLAN 30 by pinging PCs in VLAN 20 and VLAN 10. I also verified that PCs could successfully ping other devices within the same VLAN located on a different floor.
<img width="1300" height="637" alt="image" src="https://github.com/user-attachments/assets/78e6c1fb-1379-4a1d-a992-6861e7247904" />


The next step is to configure internet connectivity. To achieve this, I will first create a point-to-point network using the 203.0.113.0/30 subnet — assigning one IP address to the router and one to the CORE-SW3 port that connects to it.
Since CORE-SW3 is a multilayer switch, its ports are Layer 2 by default. To allow the port connecting to the router to have an IP address, I will convert it to a Layer 3 routed port using the no switchport command.
After configuring the point-to-point link, I will configure a default route on CORE-SW3 pointing to the router
ip route 0.0.0.0 0.0.0.0 203.0.113.1
This tells CORE-SW3 to forward all unknown traffic — including internet-bound traffic — to the router.
Finally, I will configure a static route on the router pointing back to the internal network:
ip route 192.168.10.0 255.255.255.0 203.0.113.2

Since this is a lab environment and there is no real internet connection available, I simulated internet connectivity by creating a loopback interface on the router and assigning it the IP address 1.1.1.1
<img width="589" height="201" alt="image" src="https://github.com/user-attachments/assets/e62540c8-04e4-4450-a881-4952c95b4246" />
<img width="1196" height="672" alt="image" src="https://github.com/user-attachments/assets/c8cc1ef4-1547-4445-b46a-481cf49c81f6" />


In this picture we see that the test for pinging the interent working correctly..

<img width="490" height="357" alt="image" src="https://github.com/user-attachments/assets/3a1af6aa-baee-44a2-a8b5-cffa9abf5dcd" />


-On thing i also remembered from CCNA. Not all ports on the switches were used and for a security best practices, these ports must be Administrevly down. The ports on the switches are not down by defaults ( Unlike the siwtch)

