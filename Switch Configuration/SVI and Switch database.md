# SVI and Switch Database Configuration

## Objective

- Configure VLAN database on a Cisco 2960 switch
- Create Switch Virtual Interface (SVI) for remote management
- Assign VLANs to switch ports
- Enable SSH management access

## Concepts

**Switch Database**: Stores the different VLANs on the switch.

**SVI (Switch Virtual Interface)**: A Layer 3 interface on the switch that allows you to assign an IP address to a switch. This enables remote management of the switch using SSH.

![Network Diagram](https://github.com/user-attachments/assets/75415ccd-ae56-4241-be63-cb7be2104828)

---

## Network Topology

### Hardware
- **Switch**: Cisco 2960
- **PCs**: 3 devices

### Connections
| Device   | Port    |
|----------|---------|
| PC1      | Fa0/1   |
| PC2      | Fa0/2   |
| Admin PC | Fa0/24  |

---

## IP Configuration

| Device     | VLAN    | IP Address    | Subnet Mask   | Gateway |
|------------|---------|---------------|---------------|---------|
| PC1        | VLAN 10 | 192.168.10.10 | 255.255.255.0 | blank   |
| PC2        | VLAN 20 | 192.168.20.10 | 255.255.255.0 | blank   |
| Admin PC   | VLAN 99 | 192.168.99.10 | 255.255.255.0 | blank   |
| Switch SVI | VLAN 99 | 192.168.99.2  | 255.255.255.0 | none    |

---

## Switch Configuration

### Step 1: Enter Configuration Mode

```
enable
configure terminal
```

### Step 2: Create VLANs

```
vlan 10
 name Students
vlan 20
 name Staff
vlan 99
 name Management
```

### Step 3: Bind Interfaces to VLANs

```
interface f0/1
 switchport mode access
 switchport access vlan 10

interface f0/2
 switchport mode access
 switchport access vlan 20

interface f0/24
 switchport mode access
 switchport access vlan 99
```

### Step 4: Create SVI (Virtual Interface)

**Note**: A device must already exist in the VLAN with an IP address before creating a virtual interface in that VLAN.

```
interface vlan 99
 ip address 192.168.99.2 255.255.255.0
 no shutdown
```

### Step 5: Save Configuration

```
end
write memory
```

---

## Verification Commands

### Show VLAN Database

```
show vlan brief
```

**Output**: Confirms VLAN creation and port assignment.

![VLAN Brief Output](https://github.com/user-attachments/assets/8feacc94-bc1e-4fdd-b078-e28f57ff9c21)

### Show IP Interfaces

```
show ip interfaces brief
```

**Output**: Shows all interfaces with assigned IP addresses.

![IP Interfaces Output](https://github.com/user-attachments/assets/1400247b-bd16-479e-927c-d0621901d594)

### Confirm Switch Connectivity

From the Admin PC, ping the switch SVI:

```
ping 192.168.99.2
```

**Expected Result**: ✓ Success

![Ping Test](https://github.com/user-attachments/assets/0b0c03fe-dd95-4d19-87fa-e9bc9c0f39fe)

---

## Summary

Once configured, the switch is accessible for remote management via SSH from the Admin PC using the SVI IP address (192.168.99.2). This provides out-of-band management access without interfering with data traffic on the access VLANs.
