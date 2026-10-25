# Enterprise Network Lab

A simulated enterprise network built with Cisco Packet Tracer to practice network segmentation, inter-VLAN routing, DHCP, and access control.

## Project Overview

This project simulates a small enterprise network with separate VLANs for administration, IT, servers, and guest devices. Router-on-a-stick enables inter-VLAN routing, while an extended Access Control List (ACL) restricts guest access to internal networks.

## Network Design

| VLAN | Name   | Network         | Gateway      |
| ---- | ------ | --------------- | ------------ |
| 10   | ADMIN  | 192.168.10.0/24 | 192.168.10.1 |
| 20   | IT     | 192.168.20.0/24 | 192.168.20.1 |
| 30   | SERVER | 192.168.30.0/24 | 192.168.30.1 |
| 40   | GUEST  | 192.168.40.0/24 | 192.168.40.1 |

## Technologies

- Cisco Packet Tracer
- VLAN segmentation
- IEEE 802.1Q trunking
- Router-on-a-stick
- DHCP
- Extended IPv4 ACL

## Security Policy

Traffic originating from VLAN 40 (GUEST) is denied access to VLANs 10 (ADMIN), 20 (IT), and 30 (SERVER). Other traffic is permitted by the configured ACL unless restricted elsewhere.

## Verification

- DHCP leases verified on the router.
- Inter-VLAN connectivity tested.
- Guest access to internal networks tested and blocked.
- ACL counters checked using `show access-lists`.

## Repository Structure

- `topology/` — Cisco Packet Tracer simulation.
- `configs/` — Router and switch configurations.
- `screenshots/` — Verification evidence.
- `docs/` — Network design documentation.

## How to Use

1. Install Cisco Packet Tracer.
2. Open `topology/enterprise-network.pkt`.
3. Inspect the router and switch configurations.
4. Repeat the connectivity and ACL verification tests.

## Scope and Limitations

This is a simulated lab environment. Internet connectivity, NAT, production-grade redundancy, and advanced monitoring are outside the current scope.
