# Connectivity Test Report

## 1. Overview

This document records connectivity and access-control tests performed on the Enterprise Network Lab built with Cisco Packet Tracer.

The objective is to verify VLAN connectivity, DHCP address assignment, inter-VLAN routing, server reachability, and guest network isolation.

## 2. Test Environment

| Component      | Configuration                          |
| -------------- | -------------------------------------- |
| Router         | Cisco 2911 (R1)                        |
| Switch         | Cisco 2960 (SW1)                       |
| VLANs          | 10 ADMIN, 20 IT, 30 SERVER, 40 GUEST   |
| Routing        | Router-on-a-stick with 802.1Q          |
| DHCP           | R1 DHCP pools for ADMIN, IT, and GUEST |
| Access Control | Extended ACL GUEST-IN                  |

## 3. Addressing Used During Testing

| Device            | IP Address    | VLAN |
| ----------------- | ------------- | ---- |
| R1 ADMIN gateway  | 192.168.10.1  | 10   |
| PC-ADMIN          | 192.168.10.21 | 10   |
| R1 IT gateway     | 192.168.20.1  | 20   |
| PC-IT             | 192.168.20.21 | 20   |
| R1 SERVER gateway | 192.168.30.1  | 30   |
| SERVER-01         | 192.168.30.10 | 30   |
| R1 GUEST gateway  | 192.168.40.1  | 40   |
| PC-GUEST          | 192.168.40.21 | 40   |

The endpoint addresses are the leases and static configuration used during the test session.

## 4. DHCP Verification

**Objective:** Confirm that endpoints receive IP addresses from the correct DHCP pool.

Command executed on R1:

```text
show ip dhcp binding
```

Observed leases:

| Client   | Assigned Address | Result |
| -------- | ---------------- | ------ |
| PC-ADMIN | 192.168.10.21    | PASS   |
| PC-IT    | 192.168.20.21    | PASS   |
| PC-GUEST | 192.168.40.21    | PASS   |

**Result:** DHCP address assignment succeeded for all three client VLANs.

## 5. Gateway Connectivity Tests

| Source   | Destination  | Expected Result | Result |
| -------- | ------------ | --------------- | ------ |
| PC-ADMIN | 192.168.10.1 | Successful ping | PASS   |
| PC-IT    | 192.168.20.1 | Successful ping | PASS   |
| PC-GUEST | 192.168.40.1 | Successful ping | PASS   |

**Result:** Each client can reach its own VLAN gateway.

## 6. Inter-VLAN Connectivity Tests

| Source   | Destination               | Expected Result | Result |
| -------- | ------------------------- | --------------- | ------ |
| PC-ADMIN | PC-IT (192.168.20.21)     | Allowed         | PASS   |
| PC-ADMIN | SERVER-01 (192.168.30.10) | Allowed         | PASS   |

**Result:** Inter-VLAN routing allows authorized connectivity between ADMIN, IT, and SERVER.

## 7. Guest Isolation Tests

| Source   | Destination               | Expected Result | Result |
| -------- | ------------------------- | --------------- | ------ |
| PC-GUEST | PC-ADMIN (192.168.10.21)  | Blocked         | PASS   |
| PC-GUEST | PC-IT (192.168.20.21)     | Blocked         | PASS   |
| PC-GUEST | SERVER-01 (192.168.30.10) | Blocked         | PASS   |
| PC-GUEST | Gateway (192.168.40.1)    | Allowed         | PASS   |

**Result:** The extended ACL GUEST-IN blocks guest-originated traffic to the internal ADMIN, IT, and SERVER subnets while permitting access to the guest gateway.

## 8. ACL Verification

Command executed on R1:

```text
show access-lists
```

Observed ACL counters:

```text
10 deny ip 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255 (4 match(es))
20 deny ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255 (4 match(es))
30 deny ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255 (4 match(es))
40 permit ip any any (4 match(es))
```

The deny-rule counters confirm that packets matched all three guest isolation rules. ACL counters represent matching packets, not necessarily the number of distinct connections.

## 9. Overall Result

The tested network functions behaved as expected:

- DHCP assigned addresses from the appropriate subnets.
- Router subinterfaces were operational.
- Authorized inter-VLAN communication succeeded.
- Guest access to internal subnets was blocked.
- ACL counters confirmed matches on the guest isolation rules.

## 10. Scope and Limitations

These tests were performed in Cisco Packet Tracer. They validate the configured lab behavior but do not represent production-network performance testing.

Internet connectivity, NAT, external DNS resolution, redundancy, and throughput testing are outside the scope of this lab.
