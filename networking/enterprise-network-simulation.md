### I created this lab to apply the networking techniques I learned while studying for the CCNA exam. Packet Tracer was used for this lab.

### 07/10/2026




- The lab is about a fictional comapny called TechCorp. This compay has 3 departments. the task of the lab is to created and configure the LAN for the iternal departments and the WAN to the internet.Since each department has a diffenet amount of users and end devices, for the best practice i decieded to use VLSM to give each department the minimum amount of IP addresses it needs + some additinal unused IP addresses for a possible future groth.

- 192.168.10.0/24 was used for this network because it is priavte address and can give us 254 usable address which is enough for this netwrok. I used a text file to calcualte each vlan's IP addresses before applying anything. Also, i added two access switches becasue the company has two 2 floors and also it is a best practice for redudency.

<img width="1917" height="962" alt="image" src="https://github.com/user-attachments/assets/1bf2856d-7052-444b-bbe3-57d7a29af054" /> 

- In the next step i created VLANS on CORE switch and then i gave each of them a name. 
<img width="1082" height="497" alt="image" src="https://github.com/user-attachments/assets/0e234163-371b-46da-9c1d-f2ebe3c2e78f" />


- Also, on the Core (layer3) Switch , SVI was configured for each vlan, an ip address of the gateway of each vlan was added to the SVI of the vlan as illustreated in the following picture. I also used "ip routing' command to enable routing feature between vlans on this Switch.

<img width="1427" height="512" alt="image" src="https://github.com/user-attachments/assets/ee5f9dc0-2828-4a6a-8267-207f62570aca" />

































- IP address were added to the end hosts based on the calculation of VLSM. Also, one thing i remembered is that Cisco's switches ports are not disabled by default, so it will be a best practice to disalbe them. I will use " inte range" command to select the unsed ports and then i will used "shutdown " command as ilustarted in the following picture.
