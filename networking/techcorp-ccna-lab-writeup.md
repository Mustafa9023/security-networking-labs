# TechCorp LAN/WAN Design Lab

**Date:** July 12, 2026

This lab is based on a fictional company called TechCorp, which has three departments. The objective was to design, create, and configure the Local Area Network (LAN) for the internal departments, along with the Wide Area Network (WAN) connection to the internet. Since each department has a different number of users and end devices, I used Variable Length Subnet Masking (VLSM) as a best practice. This approach allows each department to receive only the number of IP addresses it requires, plus a small number of additional unused addresses to accommodate future growth.

I built this lab in Cisco Packet Tracer, applying the techniques I learned while studying for the CCNA certification.

## IP Addressing Scheme

I used the private IP address range **192.168.10.0/24** for this network, which provides 254 usable host addresses — more than sufficient for this network. I calculated the VLSM addressing scheme in a text file before configuring anything, then assigned IP addresses to the end devices based on those calculations, configuring each device's default gateway as the last usable IP address in its VLAN.

I also included two access switches, since the company occupies two floors. This follows best practice by improving network scalability and adding redundancy.

## Network Topology

I connected the end devices to the switches and documented all network connections, as shown in the diagram below.

<img width="1842" height="841" alt="image" src="https://github.com/user-attachments/assets/3990c7b1-6530-47d0-bb47-38d98ffe54a5" />

## VLAN Configuration on the Core Switch

I used a Layer 3 (core) switch to handle inter-VLAN routing. On this switch, I first created three VLANs using the `vlan` command. I also created a native VLAN (VLAN 99) and assigned it to all trunk ports so that any untagged traffic is directed there.

From a security perspective, this is best practice: untagged traffic on trunk links goes to the native VLAN by default, which is VLAN 1. Using VLAN 1 as the native VLAN is poor security practice since it's the default VLAN on every Cisco switch and is well known to attackers, who can exploit it through VLAN hopping attacks. By changing the native VLAN to VLAN 99 — an unused VLAN with no devices or ports assigned to it — any untagged traffic goes nowhere, effectively neutralizing this attack vector.

<img width="1090" height="177" alt="image" src="https://github.com/user-attachments/assets/f69cb97e-fd0b-4654-ada4-7eab03aecfd3" />

I used the same commands to create the VLANs on the other two access switches.

## SVI Configuration on CORE-SW3

Next, I configured the SVIs (Switch Virtual Interfaces) on CORE-SW3. Since this switch handles inter-VLAN routing, I assigned the last usable IP address of each subnet as the gateway on its corresponding virtual interface. I also enabled routing on the switch using the `ip routing` command — without it, the switch cannot route traffic between VLANs even if the SVIs are configured. Finally, I used `no shutdown` on each SVI to bring it up, since SVIs are administratively down by default.

<img width="1257" height="522" alt="image" src="https://github.com/user-attachments/assets/ede12761-5a86-4913-bd5b-c4a37be38781" />
<img width="1212" height="127" alt="image" src="https://github.com/user-attachments/assets/7bb75359-6a1d-4212-8495-21e85de496c8" />

## Access and Trunk Port Configuration

I configured the access ports on both SW1 and SW2 by assigning each port to its corresponding VLAN. This ensures that each port only carries traffic for one specific VLAN, preventing traffic from leaking between departments.

After configuring the access ports, I configured the uplink connections between the access switches (SW1 and SW2) and the core switch (CORE-SW3) as trunk ports. Unlike access ports, trunk ports can carry traffic for multiple VLANs simultaneously over a single physical cable. This is necessary because both SW1 and SW2 have devices from multiple VLANs connected to them, and all of that traffic needs to reach CORE-SW3 for inter-VLAN routing. I also configured VLAN 99 as the native VLAN on all trunk ports.

### Trunk Encapsulation Issue on CORE-SW3

While configuring the trunk ports on CORE-SW3, the `switchport mode trunk` command was initially rejected with the error: *"An interface whose trunk encapsulation is Auto cannot be configured to trunk mode."* This happens because the 3650 multilayer switch supports multiple trunk encapsulation protocols — specifically ISL and 802.1Q (dot1q). Unlike regular access switches, which default to dot1q automatically, the multilayer switch requires the encapsulation type to be specified explicitly before setting the trunk mode.

I resolved this by adding `switchport trunk encapsulation dot1q` before `switchport mode trunk`. This tells the switch to use 802.1Q encapsulation — the industry standard for VLAN tagging on trunk links, and the encapsulation used by every other device in the network.

<img width="1871" height="875" alt="SW3" src="https://github.com/user-attachments/assets/e48bfe8e-9454-4034-bf0b-bdb13291a5ea" />
<img width="1871" height="875" alt="SW3" src="https://github.com/user-attachments/assets/9df4fd1f-183a-4e1e-86c4-1a90fd8f15fa" />
<img width="1871" height="875" alt="SW3" src="https://github.com/user-attachments/assets/281cc550-0075-4d8e-957c-23f3f067f48c" />

## Connectivity Testing

After completing these configurations, I tested connectivity using a PC in VLAN 30, pinging PCs in VLAN 20 and VLAN 10. I also verified that PCs could successfully ping other devices in the same VLAN located on a different floor.

<img width="1300" height="637" alt="image" src="https://github.com/user-attachments/assets/78e6c1fb-1379-4a1d-a992-6861e7247904" />

## Internet Connectivity

The next step was to configure internet connectivity. To do this, I first created a point-to-point network using the `203.0.113.0/30` subnet, assigning one IP address to the router and one to the CORE-SW3 port connecting to it.

Since CORE-SW3 is a multilayer switch, its ports are Layer 2 by default. To let the port connecting to the router hold an IP address, I converted it to a Layer 3 routed port using the `no switchport` command.

After configuring the point-to-point link, I configured a default route on CORE-SW3 pointing to the router:

```
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

This tells CORE-SW3 to forward all unknown traffic — including internet-bound traffic — to the router.

Finally, I configured a static route on the router pointing back to the internal network:

```
ip route 192.168.10.0 255.255.255.0 203.0.113.2
```

Since this is a lab environment with no real internet connection available, I simulated internet connectivity by creating a loopback interface on the router and assigning it the IP address `1.1.1.1`.

<img width="589" height="201" alt="image" src="https://github.com/user-attachments/assets/e62540c8-04e4-4450-a881-4952c95b4246" />
<img width="1196" height="672" alt="image" src="https://github.com/user-attachments/assets/c8cc1ef4-1547-4445-b46a-481cf49c81f6" />

The screenshot below shows a successful ping test to the simulated internet address:

<img width="490" height="357" alt="image" src="https://github.com/user-attachments/assets/3a1af6aa-baee-44a2-a8b5-cffa9abf5dcd" />

## Securing Unused Ports

One other thing I remembered from CCNA: not all ports on the switches were in use, and as a security best practice, unused ports should be administratively shut down. Router ports are administratively down by default, whereas switch ports are not — so on a switch, this has to be done explicitly for every unused port.

## Conclusion

This lab covered the full process of designing and building a segmented LAN with WAN connectivity for a multi-department company. Using VLSM let each department's subnet be sized to its actual number of hosts rather than wasting address space on a flat allocation. Configuring VLANs with a locked-down native VLAN, setting up SVIs and inter-VLAN routing on the core switch, and correctly provisioning access versus trunk ports enforced traffic separation between departments while still allowing centralized routing. Working through the trunk encapsulation error on the multilayer switch reinforced why explicit configuration matters on Cisco's more advanced platforms. Finally, simulating internet connectivity with static routing and a loopback interface, and shutting down unused ports, tied the lab back to real-world practices for both connectivity and baseline port security.
