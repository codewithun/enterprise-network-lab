# IP Addressing Plan

## 1. Overview

This document describes the IPv4 addressing scheme used in the Enterprise Network Lab built with Cisco Packet Tracer.

The network uses separate /24 subnets for Administration, IT, Server, and Guest VLANs. Inter-VLAN routing is implemented using router-on-a-stick on router R1.

## 2. VLAN and Subnet Allocation

| VLAN ID | VLAN Name | Network         | Subnet Mask   | Default Gateway |
| ------- | --------- | --------------- | ------------- | --------------- |
| 10      | ADMIN     | 192.168.10.0/24 | 255.255.255.0 | 192.168.10.1    |
| 20      | IT        | 192.168.20.0/24 | 255.255.255.0 | 192.168.20.1    |
| 30      | SERVER    | 192.168.30.0/24 | 255.255.255.0 | 192.168.30.1    |
| 40      | GUEST     | 192.168.40.0/24 | 255.255.255.0 | 192.168.40.1    |

## 3. Device Addressing

| Device                  | VLAN | IP Address    | Assignment |
| ----------------------- | ---- | ------------- | ---------- |
| R1 subinterface G0/0.10 | 10   | 192.168.10.1  | Static     |
| R1 subinterface G0/0.20 | 20   | 192.168.20.1  | Static     |
| R1 subinterface G0/0.30 | 30   | 192.168.30.1  | Static     |
| R1 subinterface G0/0.40 | 40   | 192.168.40.1  | Static     |
| PC-ADMIN                | 10   | 192.168.10.21 | DHCP lease |
| PC-IT                   | 20   | 192.168.20.21 | DHCP lease |
| SERVER-01               | 30   | 192.168.30.10 | Static     |
| PC-GUEST                | 40   | 192.168.40.21 | DHCP lease |

Note: The DHCP addresses listed above are the leases observed during testing. DHCP leases may change after renewal or reconfiguration.

## 4. DHCP Configuration

DHCP runs on router R1 for VLANs 10, 20, and 40.

| DHCP Pool | Network         | Excluded Address Range     | Default Gateway |
| --------- | --------------- | -------------------------- | --------------- |
| ADMIN     | 192.168.10.0/24 | 192.168.10.1–192.168.10.20 | 192.168.10.1    |
| IT        | 192.168.20.0/24 | 192.168.20.1–192.168.20.20 | 192.168.20.1    |
| GUEST     | 192.168.40.0/24 | 192.168.40.1–192.168.40.20 | 192.168.40.1    |

The SERVER VLAN uses a static IP address for SERVER-01 and does not have a DHCP pool configured.

The configured DNS server distributed by DHCP is 8.8.8.8. DNS resolution requires a reachable DNS service.

## 5. Network Design Notes

- Each VLAN uses a separate /24 IPv4 subnet.
- R1 provides the default gateway for each VLAN.
- SW1 uses access ports for endpoint connections.
- SW1 GigabitEthernet0/1 carries VLANs 10, 20, 30, and 40 as an 802.1Q trunk to R1.
- Extended ACL GUEST-IN blocks traffic originating from VLAN 40 to the ADMIN, IT, and SERVER subnets.

## 6. Verification

The addressing plan was verified through DHCP lease inspection, gateway ping tests, inter-VLAN connectivity tests, and ACL testing.

Commands used include:

```text
show ip interface brief
show ip dhcp binding
show access-lists
```
