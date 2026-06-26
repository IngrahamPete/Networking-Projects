# VLAN Configuration Lab Report

## Objective

- Create VLAN 20 and VLAN 30 on two switches
- Assign hosts to VLANs
- Configure trunk links between switches
- Enable inter-VLAN communication

## Network Topology

![VLAN config](https://github.com/user-attachments/assets/35da40ee-5628-4211-9c17-1d83a73409c0)

---

## IP Configuration

### VLAN 20
| Device | IP Address | Subnet Mask |
|--------|-----------|------------|
| PC0 | 172.16.20.2 | 255.255.255.0 |
| PC1 | 172.16.20.3 | 255.255.255.0 |

### VLAN 30
| Device | IP Address | Subnet Mask |
|--------|-----------|------------|
| PC3 | 172.16.30.2 | 255.255.255.0 |
| PC4 | 172.16.30.3 | 255.255.255.0 |

---

## Configuration Overview

Devices within the same VLAN can communicate directly. When VLANs are introduced, trunk links are needed to allow inter-switch communication between devices in the same VLAN. Trunk links carry traffic from multiple VLANs between switches.

---

## Switch Configuration

### SW0 Configuration

```
enable
configure terminal

! Create VLANs
vlan 20
vlan 30

! Assign access ports
interface f0/1
  switchport mode access
  switchport access vlan 20

interface f0/2
  switchport mode access
  switchport access vlan 30

! Configure trunk port
interface g0/1
  switchport mode trunk
```

### SW1 Configuration

```
enable
configure terminal

! Create VLANs
vlan 20
vlan 30

! Assign access ports
interface f0/1
  switchport mode access
  switchport access vlan 20

interface f0/2
  switchport mode access
  switchport access vlan 30

! Note: Trunk configuration is automatic via crossover cable
```

---

## Verification Commands

### Switches

| Command | Purpose |
|---------|---------|
| `show vlan brief` | Confirms VLAN creation and port assignment |
| `show interfaces switchport` | Confirms access/trunk mode configuration |
| `show interfaces trunk` | Confirms VLANs allowed and active on trunk |

### PCs

| Test | Expected Result |
|------|-----------------|
| Ping between hosts in VLAN 20 (PC0 ↔ PC1) | ✓ Success |
| Ping between hosts in VLAN 30 (PC3 ↔ PC4) | ✓ Success |
| Ping between VLAN 20 and VLAN 30 | ⚠ Requires inter-VLAN routing |
