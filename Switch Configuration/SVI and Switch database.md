
A Switch Data is basically what stores the differnt vlans on the Switch.
SVI is Switch Vitual Interface.Layer three interface on switch that allows you to assing a ip to a switch.
This is done so if you wanted to manage oyur switch remotely you can use SSH.
Picture:<img width="513" height="312" alt="Screenshot 2026-06-26 123124" src="https://github.com/user-attachments/assets/75415ccd-ae56-4241-be63-cb7be2104828" />

1 Switch: Cisco 2960
3 PCs:
- PC1
- PC2
- Admin PC

PC1       → Switch Fa0/1
PC2       → Switch Fa0/2
Admin PC  → Switch Fa0/24


| Device     |    VLAN | IP Address    | Subnet Mask   | Gateway |
| ---------- | ------: | ------------- | ------------- | ------- |
| PC1        | VLAN 10 | 192.168.10.10 | 255.255.255.0 | blank   |
| PC2        | VLAN 20 | 192.168.20.10 | 255.255.255.0 | blank   |
| Admin PC   | VLAN 99 | 192.168.99.10 | 255.255.255.0 | blank   |
| Switch SVI | VLAN 99 | 192.168.99.2  | 255.255.255.0 | none    |


commands to now configure the switch:
enable
configure terminal 
vlan 10
 name Students
vlan 20
 name Staff
vlan 99
  Management
(binding the interfaces to the vlan's)
interface f0/1
switchport mode access
switchport access vlan 10


interface f0/2
switchport mode access
switchport access vlan 20


interface f0/24
switchport mode access
switchport access vlan 99

(inorder to ake a virtual interface on the switch you first will need a device already 
in the vlan with a ip as well)
interface vlan 99
 ip address 192.168.99.2 255.255.255.0
 no shutdown 

 end 
 write memory

 show vlan bried 
 (checks the VLAN Database):
 <img width="772" height="296" alt="Screenshot 2026-06-26 124029" src="https://github.com/user-attachments/assets/8feacc94-bc1e-4fdd-b078-e28f57ff9c21" />

show ip interfaces brief:
(shows all interfaces with ip addresses if thye have any)
<img width="748" height="420" alt="Screenshot 2026-06-26 124126" src="https://github.com/user-attachments/assets/1400247b-bd16-479e-927c-d0621901d594" />

 Confirming the switch is up:
 (from the admin pc ping the switch)
 <img width="676" height="227" alt="Screenshot 2026-06-26 124326" src="https://github.com/user-attachments/assets/0b0c03fe-dd95-4d19-87fa-e9bc9c0f39fe" />


