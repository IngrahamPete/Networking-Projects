# Network Design & Implementation Project

## Inter-VLAN Routing with VLSM (Router-on-a-Stick)

**Author:** Pete Ingraham
**Classification:** Educational / Training Exercise  
**Version:** 1.0  

---

## 1. Executive Summary

This project documents the design and implementation of a segmented network using **VLSM (Variable Length Subnet Masking)** and **Inter-VLAN Routing** with a **Router-on-a-Stick** configuration. The goal was to divide the company’s base network into multiple subnetworks based on department size, assign each department to its own VLAN, and enable communication between VLANs through router subinterfaces.

The company was provided with the base network **10.50.0.0/16**. Different departments required different numbers of host addresses, so VLSM was used to allocate address space efficiently. The departments included **Students, Staff, Admin, and Servers**, each with different host requirements.

After subnet allocation, VLANs were created on the switch, access ports were assigned, and a trunk port was configured between the switch and router. The router was then configured with subinterfaces using **802.1Q encapsulation** so it could route traffic between VLANs. DHCP was also configured to dynamically assign IP addresses to hosts in each VLAN.

Testing confirmed that hosts could successfully receive addresses and communicate with their default gateways and other VLANs, demonstrating that the router-on-a-stick implementation was functioning correctly.

---

## 2. Scope & Objectives

### 2.1 Scope

#### In Scope
- VLSM subnet planning
- VLAN creation on the switch
- Access port assignment
- Trunk port configuration
- Router-on-a-Stick configuration
- DHCP configuration for each subnet
- Inter-VLAN communication testing

#### Out of Scope
- WAN connectivity
- Internet access configuration
- Advanced routing protocols
- Security ACL implementation
- Wireless network design

### 2.2 Objectives

- Subnet the base network using VLSM
- Allocate address space efficiently based on department size
- Create VLANs for each department
- Configure switch access and trunk ports
- Configure router subinterfaces for inter-VLAN routing
- Configure DHCP pools for automatic host addressing
- Verify connectivity using ping tests

---

## 3. Base Network & Planning

### Company Base Network
`10.50.0.0/16`

The base network was provided for the company, and it needed to be divided among departments with different host requirements.

### Department Host Requirements

| Department | Required Hosts |
|---|---:|
| Students | 120 |
| Staff | 60 |
| Admin | 25 |
| Servers | 10 |

Because the departments require different numbers of hosts, **VLSM** was used instead of assigning equal-sized subnets.

---

## 4. CIDR and Subnetting Overview

This subnetting process is accomplished using **CIDR (Classless Inter-Domain Routing)** notation.

CIDR is written as an IP address followed by a forward slash and a number, such as `192.168.0.0/24`. The number after the slash represents how many bits are used for the **network portion** of the address.

For example:

- A prefix of `/24` means the first 24 bits identify the network.
- Since IPv4 addresses are 32 bits total, that leaves 8 bits for hosts.
- `2^8 = 256` total addresses are available in that subnet.

### Common Subnet Sizes

| Prefix | Total Addresses |
|---|---:|
| /24 | 256 |
| /25 | 128 |
| /26 | 64 |
| /27 | 32 |
| /28 | 16 |
| /29 | 8 |
| /30 | 4 |
| /31 | 2 |
| /32 | 1 |

### Important Notes

- As the prefix number increases, more bits are used for the network portion.
- This leaves fewer bits available for hosts.
- As the prefix number decreases, more host addresses become available.

Examples:

- `/32` identifies a single host address.
- `/30` provides 4 total addresses, with 2 usable addresses after excluding the network and broadcast addresses. This is often used for point-to-point links.

---

## 5. VLSM Subnetting Plan

### Step 1: Start With the Largest Department

When using VLSM, subnetting begins with the **largest host requirement first**. This prevents fragmentation and ensures efficient use of the available address space.

The department with the largest host requirement is:

- **Students: 120 hosts**

To support at least 120 usable addresses, the smallest appropriate subnet is:

- **/25**
- 128 total addresses
- 126 usable addresses

Since 126 usable addresses is enough for 120 hosts, `/25` is the correct choice.

### Example: Students Subnet

- **Network Address:** `10.50.0.0/25`
- **Subnet Mask:** `255.255.255.128`
- **Usable Range:** `10.50.0.1 - 10.50.0.126`
- **Broadcast Address:** `10.50.0.127`
- **Default Gateway:** `10.50.0.1`

The network address identifies the subnet itself.  
The broadcast address is used to communicate with all devices in that subnet.  
The usable range is assigned to hosts.  
The default gateway is typically the first usable IP address and is assigned to the router subinterface.

### Final VLSM Allocation

| Department | Hosts Needed | Prefix | Subnet Mask | Network Address | Usable Host Range | Broadcast Address | Default Gateway |
|---|---:|---|---|---|---|---|---|
| Students | 120 | /25 | 255.255.255.128 | 10.50.0.0 | 10.50.0.1 - 10.50.0.126 | 10.50.0.127 | 10.50.0.1 |
| Staff | 60 | /26 | 255.255.255.192 | 10.50.0.128 | 10.50.0.129 - 10.50.0.190 | 10.50.0.191 | 10.50.0.129 |
| Admin | 25 | /27 | 255.255.255.224 | 10.50.0.192 | 10.50.0.193 - 10.50.0.222 | 10.50.0.223 | 10.50.0.193 |
| Servers | 10 | /28 | 255.255.255.240 | 10.50.0.224 | 10.50.0.225 - 10.50.0.238 | 10.50.0.239 | 10.50.0.225 |

This plan efficiently uses address space while meeting the needs of each department.

---

## VLSM Planning Diagram

![VLSM Planning Diagram]<img width="1275" height="365" alt="image" src="https://github.com/user-attachments/assets/d7967f36-3929-42e1-932c-5392ee0bf166" />

---

## 6. VLAN Design

Each department was assigned its own VLAN to separate broadcast domains and improve network organization.

### VLAN Assignment Plan

| VLAN ID | Department |
|---|---|
| 10 | Students |
| 20 | Staff |
| 30 | Admin |
| 40 | Servers |

Creating separate VLANs means that devices in one VLAN cannot communicate directly with devices in another VLAN unless routing is configured.

---

## 7. Why Trunking Is Required

Because the network contains multiple VLANs, the router must be able to receive traffic from each VLAN and determine where that traffic belongs.

A standard access link can only carry traffic for one VLAN. To allow traffic from multiple VLANs to travel between the switch and router, a **trunk link** is required.

### Trunking and 802.1Q

Trunk links use **802.1Q tagging**. This process adds a VLAN tag to each Ethernet frame so that the receiving device knows which VLAN the frame belongs to.

This allows one physical link between the switch and router to carry traffic for all VLANs.

---

## 8. Switch Configuration

### 8.1 Create VLANs on the Switch

The first step in Packet Tracer was to select the HQ switch and create the VLANs for each department.

Example switch CLI process:

```bash
enable
configure terminal
vlan 10
name Students
vlan 20
name Staff
vlan 30
name Admin
vlan 40
name Servers
```
````markdown
## 8. Switch Configuration

### 8.1 Create VLANs on the Switch

```bash
enable
configure terminal
vlan 10
name Students
vlan 20
name Staff
vlan 30
name Admin
vlan 40
name Servers
````

![VLAN Creation on Switch](https://github.com/user-attachments/assets/1d700a2f-476e-44e6-80cf-d04815ed8603)

### 8.2 Assign Access Ports to VLANs

```bash
interface range fa0/1-10
switchport mode access
switchport access vlan 10
```

![Access Port Assignment](https://github.com/user-attachments/assets/6081bdfc-d05b-4a98-b558-c81217a4edb4)

### 8.3 Configure the Trunk Port

```bash
enable
configure terminal
interface fa0/24
switchport mode trunk
```

```bash
show interfaces trunk
show vlan brief
```

![Trunk Port Verification](https://github.com/user-attachments/assets/d70fc23b-e802-4edb-857a-96cf1b602b65)

## 9. Router Configuration

### 9.1 Enable the Physical Router Interface

```bash
interface GigabitEthernet0/0/0
no shutdown
```

![Router Physical Interface Configuration](https://github.com/user-attachments/assets/46198039-8f69-4ca3-b317-6a29420564f3)

### 9.2 Configure Router Subinterfaces

```bash
interface GigabitEthernet0/0/0.10
encapsulation dot1Q 10
ip address 10.50.0.1 255.255.255.128

interface GigabitEthernet0/0/0.20
encapsulation dot1Q 20
ip address 10.50.0.129 255.255.255.192

interface GigabitEthernet0/0/0.30
encapsulation dot1Q 30
ip address 10.50.0.193 255.255.255.224

interface GigabitEthernet0/0/0.40
encapsulation dot1Q 40
ip address 10.50.0.225 255.255.255.240
```

![Router Subinterface Configuration](https://github.com/user-attachments/assets/76b781cc-a0b5-4c38-9506-9aca288117ed)

![Router Subinterface Verification](https://github.com/user-attachments/assets/c59e1fcf-2e49-4306-95c8-e9008a4c3289)

## 10. DHCP Configuration

### 10.1 Exclude Gateway Addresses

```bash
ip dhcp excluded-address 10.50.0.1
ip dhcp excluded-address 10.50.0.129
ip dhcp excluded-address 10.50.0.193
ip dhcp excluded-address 10.50.0.225
```

![DHCP Excluded Addresses](https://github.com/user-attachments/assets/28552050-da0a-4cea-b87b-cc178ecdc1f6)

### 10.2 Create DHCP Pools

```bash
ip dhcp pool STUDENTS
network 10.50.0.0 255.255.255.128
default-router 10.50.0.1

ip dhcp pool STAFF
network 10.50.0.128 255.255.255.192
default-router 10.50.0.129

ip dhcp pool ADMIN
network 10.50.0.192 255.255.255.224
default-router 10.50.0.193

ip dhcp pool SERVERS
network 10.50.0.224 255.255.255.240
default-router 10.50.0.225
```

![DHCP Pool Configuration](https://github.com/user-attachments/assets/9a8a0574-d846-458c-81d8-180f66953ffe)


## 11. Testing and Verification

### 11.1 Test Connectivity from PCs

![PC Connectivity Test](https://github.com/user-attachments/assets/0931e59f-fd2a-48f4-8174-dd08cfb0dacb)

![Inter-VLAN Ping Test](https://github.com/user-attachments/assets/451a673b-1e2f-4f73-af0e-5866a44f7ebb)

## 12. Save the Configuration

```bash
write memory
````

## 13. Summary

The successful ping tests confirmed that the VLAN assignments were correct, trunking was functioning properly, the router subinterfaces were routing traffic correctly, DHCP was assigning valid addresses, and inter-VLAN communication was successful. After testing, the configurations on both the switch and router were saved using `write memory` so the settings would persist after a reboot.

```
```



