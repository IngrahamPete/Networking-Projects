Network Design & Implementation Project
Inter-VLAN Routing with VLSM (Router-on-a-Stick)

Base Network & Planning
Company Base Network
 	10.50.0.0/16

it also includes departments with different host needs

First i have to allocate subnets using vslm or make  the largest network first

Admin (25 hosts)
Staff (60 hosts)
Students (120 hosts)
Servers (10 hosts)

which is the students


This subnetting process is accomplished using CIDR (Classless Inter-Domain Routing) notation.

CIDR is written as an IP address followed by a forward slash and a number (for example, 192.168.0.0/24). The number after the slash represents how many bits are used for the network portion of the address.

For example, a prefix of /24 means the first 24 bits identify the network. Since an IPv4 address is 32 bits total, that leaves 8 bits for host addresses. Eight host bits allow for 
2^8=256 total addresses in the subnet.

Different prefixes determine how many usable host addresses are available:

/32 uses all 32 bits for the network, meaning only one single IP address exists. This is typically used to identify a single device.

/30 corresponds to the subnet mask 255.255.255.252. This provides 4 total addresses. However, because one address is reserved as the network address and one as the broadcast address, only 2 usable host addresses remain. This is commonly used for point-to-point WAN links.

An IPv4 address consists of 32 bits (4 octets). The CIDR prefix determines how many of those bits are used for the network portion of the address.

As the prefix number increases, more bits are allocated to the network portion and fewer bits remain for host addresses. This results in fewer usable IP addresses in the subnet.
As the prefix number decreases, more bits are available for hosts, increasing the number of usable addresses.

Below is a reference for common subnet sizes:

256 /24
128 /25
64  /26
32 /27
16 /28
8 /29
4 /30
2 /31
1 /32

Subnetting the Base Network

The base network provided was:
10.50.0.0/16
To divide this network among departments, I used VLSM (Variable Length Subnet Masking).

Step 1: Start With the Largest Department

Subnetting begins with the department requiring the most hosts. This prevents address space fragmentation and ensures efficient allocation.

For example, if a department requires 120 hosts, I select the smallest prefix that can support at least 120 usable addresses.

A /25 provides:

128 total addresses

126 usable addresses (subtracting network and broadcast)

Since 126 usable addresses is greater than 120, /25 is appropriate.

Example: 10.50.0.0/25

Network Address: 10.50.0.0

Subnet Mask: 255.255.255.128

Usable Range: 10.50.0.1 – 10.50.0.126

Broadcast Address: 10.50.0.127

Default Gateway: 10.50.0.1 (first usable address)

The network address identifies the subnet itself.
The broadcast address allows communication to all devices within the subnet.
The usable IP range is assigned to hosts.
The default gateway is typically the first usable IP address and is assigned to the router interface.


<img width="1275" height="365" alt="image" src="https://github.com/user-attachments/assets/d7967f36-3929-42e1-932c-5392ee0bf166" />

Next I have to Create the VLANs on the switches.

Then as i have multiple broaodcst domains or VLANS the router would not be able to determine which VLAN to send the traffic to. THis is where trunk cables come in.

The trunk cables allow the router the send traffic to the VLAN it needs to be sent to .More indepth this is done by 802.1Q tagging by adding a VLAN tag to each frame so the router or other receiving switch can know which VLAN needs the frame.


I now go to packet tracer:

pick a HQ switch and start creating the VLANS

the cli on the switch to create the different vlans:
<img width="761" height="674" alt="image" src="https://github.com/user-attachments/assets/1d700a2f-476e-44e6-80cf-d04815ed8603" />


After the VLAN Creation I assinged the accsess ports to each vlan.

<img width="472" height="357" alt="image" src="https://github.com/user-attachments/assets/6081bdfc-d05b-4a98-b558-c81217a4edb4" />

After those commands we do the trunk port.
enable 
confifure terminal 
interface fa0/24
switchport mode trunk

then:
show interfaces trunk
show vlan brief
<img width="717" height="530" alt="image" src="https://github.com/user-attachments/assets/d70fc23b-e802-4edb-857a-96cf1b602b65" />



We then configure the router 
To enable the router interface we use 
GigabitEthernet0/0/0
<img width="721" height="212" alt="image" src="https://github.com/user-attachments/assets/46198039-8f69-4ca3-b317-6a29420564f3" />

then on that interface we create the sub interfaces with these commands  
<img width="491" height="555" alt="image" src="https://github.com/user-attachments/assets/76b781cc-a0b5-4c38-9506-9aca288117ed" />


<img width="737" height="226" alt="image" src="https://github.com/user-attachments/assets/c59e1fcf-2e49-4306-95c8-e9008a4c3289" />


After that we then configure dhcp for all the subinterfaces

with these commands 
First we exclude the gateway addresses to the vlans
<img width="397" height="102" alt="image" src="https://github.com/user-attachments/assets/28552050-da0a-4cea-b87b-cc178ecdc1f6" />

Then create the pools from the other ranges from the vlsm plan
<img width="494" height="572" alt="image" src="https://github.com/user-attachments/assets/9a8a0574-d846-458c-81d8-180f66953ffe" />


Now I have to test it on the pcs by pinging the gateway addresses to the other vlans


<img width="530" height="559" alt="image" src="https://github.com/user-attachments/assets/0931e59f-fd2a-48f4-8174-dd08cfb0dacb" />
<img width="572" height="349" alt="image" src="https://github.com/user-attachments/assets/451a673b-1e2f-4f73-af0e-5866a44f7ebb" />

This shows the everthing else works 

On the switch and router 

write memory to save configuration






