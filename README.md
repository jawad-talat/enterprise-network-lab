# Enterprise Network Lab — Cisco Packet Tracer

A full enterprise network simulation built in Cisco Packet Tracer covering HQ and Branch connectivity with real-world routing, switching, security, and management features.

---

## Topology Overview

```
PC-Internet
     |
   [R1 - HQ Router]  ======= WAN (Gig0/1 — Gig0/1) =======  [R2 - Branch Router]
     |                                                                  |
   [SW1 - HQ Core]                                             [SW3 - Branch SW]
  /            \                                               /              \
[SW2]       [Server0]                                   [PC-Branch1]    [PC-Branch2]
  |           VLAN 40                                    VLAN 10          VLAN 20
  |
[PC-Sales]  VLAN 10
[PC-IT]     VLAN 20
[PC-HR]     VLAN 30
[PC-Mgmt]   VLAN 40
```

---

## IP Addressing Table

| Device | Interface | IP Address | Subnet Mask |
|--------|-----------|------------|-------------|
| R1 | Gig0/0.10 | 192.168.10.1 | /26 |
| R1 | Gig0/0.20 | 192.168.20.1 | /27 |
| R1 | Gig0/0.30 | 192.168.30.1 | /28 |
| R1 | Gig0/0.40 | 192.168.40.1 | /29 |
| R1 | Gig0/1 | 10.0.0.1 | /30 |
| R1 | Gig0/2 | 203.0.113.1 | /30 |
| R2 | Gig0/0.10 | 192.168.110.1 | /27 |
| R2 | Gig0/0.20 | 192.168.120.1 | /28 |
| R2 | Gig0/1 | 10.0.0.2 | /30 |
| SW1 | Vlan40 | 192.168.40.3 | /29 |
| SW2 | Vlan40 | 192.168.40.5 | /29 |
| SW3 | Vlan20 | 192.168.120.3 | /28 |
| Server0 | NIC | 192.168.40.2 | /29 |
| PC-Internet | NIC | 203.0.113.2 | /30 |

---

## VLAN Table

| VLAN ID | Name | Network | Subnet Mask |
|---------|------|---------|-------------|
| 10 | Sales | 192.168.10.0 | /26 (62 hosts) |
| 20 | IT | 192.168.20.0 | /27 (30 hosts) |
| 30 | HR | 192.168.30.0 | /28 (14 hosts) |
| 40 | Management | 192.168.40.0 | /29 (6 hosts) |
| 10 (Branch) | Branch1 | 192.168.110.0 | /27 (30 hosts) |
| 20 (Branch) | Branch2 | 192.168.120.0 | /28 (14 hosts) |

---

## Features Implemented

### Switching
- VLANs configured on all three switches
- Trunk ports between SW1-R1, SW1-SW2, SW3-R2
- Router-on-a-Stick (Inter-VLAN Routing) on R1 and R2
- Rapid PVST spanning tree mode
- PortFast on all access ports

### Routing
- OSPF (Process 1, Area 0) between R1 and R2
- Default route on R1 toward internet gateway

### DHCP
- Centralized DHCP server on R1 with 6 pools
- DHCP Relay (ip helper-address) on R2 for Branch VLANs
- Server0 assigned static IP (192.168.40.2)

### NAT
- PAT (NAT Overload) on R1 — all private networks translated to 203.0.113.1
- Standard ACL NAT_POOL matching 192.168.0.0/8

### Security
- Extended ACL — HR blocked from reaching Server0
- Extended ACL — Sales restricted to HTTP/HTTPS to Server0 only (ICMP and all other TCP blocked)
- SSH v2 on all 5 devices (R1, R2, SW1, SW2, SW3)
- Console password on all devices
- Port Security with sticky MAC and restrict violation on all access ports
- DHCP Snooping on all switches with rate limiting (15 pps) on untrusted ports

---

## Subnetting

### VLSM — Manual subnetting from 192.168.0.0/16

Right-sized each subnet to its host requirement instead of using equal blocks:

| Subnet | Hosts Needed | Mask | Network | Broadcast | Usable Range |
|--------|-------------|------|---------|-----------|--------------|
| VLAN 10 Sales | 50 | /26 | 192.168.10.0 | 192.168.10.63 | .1 — .62 |
| VLAN 20 IT | 25 | /27 | 192.168.20.0 | 192.168.20.31 | .1 — .30 |
| VLAN 30 HR | 10 | /28 | 192.168.30.0 | 192.168.30.15 | .1 — .14 |
| VLAN 40 Mgmt | 5 | /29 | 192.168.40.0 | 192.168.40.7 | .1 — .6 |
| Branch VLAN 10 | 20 | /27 | 192.168.110.0 | 192.168.110.31 | .1 — .30 |
| Branch VLAN 20 | 10 | /28 | 192.168.120.0 | 192.168.120.15 | .1 — .14 |

### FLSM — Equal subnetting from 172.16.1.0/24

Divided into 4 equal subnets for a server farm:

| Subnet | Network | Mask | Broadcast | Usable Range |
|--------|---------|------|-----------|--------------|
| Web | 172.16.1.0 | /26 | 172.16.1.63 | .1 — .62 |
| DNS | 172.16.1.64 | /26 | 172.16.1.127 | .65 — .126 |
| FTP | 172.16.1.128 | /26 | 172.16.1.191 | .129 — .190 |
| Reserved | 172.16.1.192 | /26 | 172.16.1.255 | .193 — .254 |

---

## Key Design Decisions

- Used extended ACL for HR instead of standard ACL — standard ACL blocks source IP entirely, breaking HR access to other VLANs. Extended ACL targets specific source and destination.
- DHCP Snooping option 82 disabled — required in Packet Tracer to prevent DHCP relay breakage.
- Server0 assigned static IP — servers must have fixed addresses; DHCP assignments change on reboot.
- Management VLAN 40 used for SSH access to all devices — isolates management traffic from user traffic.

---

## Device Configurations

See the `/configs` folder for full running configurations of all devices.

---

## Tools Used

- Cisco Packet Tracer
- Cisco IOS 15.x

---

## Author

Jawad Talat — BS Cybersecurity Student | CCNA Certified
