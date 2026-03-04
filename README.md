first step to build network


have a Base network which is
 	10.50.0.0/16
 	it also includes departments with different host needs

first i have to allocate subnets using vslm or make  the largest network first

Admin (25 hosts)
Staff (60 hosts)
Students (120 hosts)
Servers (10 hosts)

which is the students

THis is done by using CIDR prefix (classless Inter Domain Rounting).

It is typically written as an IP address followed by a slash and a number (e.g., 192.168.0.0/24), where the number indicates the number of bits used for the network prefix.


For example, a CIDR prefix of /24 indicates that the first 24 bits are used for the network part, allowing for 256 IP addresses in that subnet.

How it works if the prefix is /32 only one Ip address is usable.
if it is /30 that mean 255.255.255.252 this means the amount of IP address you can use is 4 but due to network address and broadcast address the only amount oyu can use is 2.

this is basically 4 bytes and lowering the number increases the number of available addresses


256 /24
128 /25
64  /26
32 /27
16 /28
8 /29
4 /30
2 /31
1 /32

10.50.0.0/16
First I made the subnet for each department of hosts
started from the largest amount of host then worked my way down on the local network.
How i did it was determin the correct prefix by determinging which prefix was larger than the host section i was working on.
For example if there are 120 hosts i will choose the /25 prefix which will allow me 128 disctinct ip addresses.
Then I chose the network address which is the path to the specific subnet which in this example is 10.50.0.0.
then I chose the subnet mask. This is the network porition of the subnet that ips cannot interact with .
In this case it is 255.255.255.128.

The usable ranges of ips are 10.50.0.1 - .126.
And finally the broadcast is .127 which allows it to interact with all devices on the network.
Gateway is the first usable ip on the network.



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






