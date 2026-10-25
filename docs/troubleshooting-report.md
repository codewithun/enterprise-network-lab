# Troubleshooting Report

## 1. Overview

This document records troubleshooting activities performed while building the Enterprise Network Lab in Cisco Packet Tracer.

## 2. Issue: Trunk Link Not Connected

### Symptoms

The switch command `show interfaces trunk` returned no active trunk information. The status of SW1 GigabitEthernet0/1 showed:

```text
Gig0/1  notconnect  trunk  auto  auto
```

### Diagnosis

The switch port was configured as a trunk, but the physical link was not active.

### Resolution

- Verified the cable between R1 GigabitEthernet0/0 and SW1 GigabitEthernet0/1.
- Checked the router interface status.
- Ensured the router interface was enabled using `no shutdown`.
- Waited for the link to become active.

### Verification

SW1 GigabitEthernet0/1 subsequently reported `connected`.

## 3. Issue: Inter-VLAN Routing Verification

### Objective

Verify that endpoints in different VLANs could communicate through R1.

### Diagnosis

Checked the router subinterfaces and their IP addresses using:

```text
show ip interface brief
```

### Resolution

Configured router-on-a-stick using 802.1Q subinterfaces for VLANs 10, 20, 30, and 40.

### Verification

All four router subinterfaces reported `up/up`. DHCP clients received addresses from their respective VLAN subnets, and the inter-VLAN ping tests succeeded.

## 4. Issue: DHCP Address Assignment

### Objective

Ensure that ADMIN, IT, and GUEST endpoints received IP addresses automatically.

### Diagnosis

Inspected DHCP leases using:

```text
show ip dhcp binding
```

### Resolution

Configured DHCP pools on R1 for VLANs 10, 20, and 40, with excluded addresses reserved for gateways and other static assignments.

### Verification

The router assigned the following addresses during testing:

- PC-ADMIN: 192.168.10.21
- PC-IT: 192.168.20.21
- PC-GUEST: 192.168.40.21

## 5. Issue: Restricting Guest Access

### Objective

Prevent devices in VLAN 40 from accessing the ADMIN, IT, and SERVER subnets.

### Resolution

Created the extended ACL GUEST-IN and applied it inbound on R1 GigabitEthernet0/0.40.

The ACL denies traffic from 192.168.40.0/24 to:

- 192.168.10.0/24
- 192.168.20.0/24
- 192.168.30.0/24

Other traffic is permitted by the final ACL rule unless restricted elsewhere.

### Verification

Guest-to-internal ping tests were blocked. The command `show access-lists` showed matching packets on all three deny rules.

## 6. Final Verification

| Test                 | Expected Result  | Observed Result |
| -------------------- | ---------------- | --------------- |
| Router subinterfaces | Up/up            | Passed          |
| DHCP for ADMIN       | Address assigned | Passed          |
| DHCP for IT          | Address assigned | Passed          |
| DHCP for GUEST       | Address assigned | Passed          |
| ADMIN to IT          | Allowed          | Passed          |
| ADMIN to SERVER      | Allowed          | Passed          |
| GUEST to ADMIN       | Blocked          | Passed          |
| GUEST to IT          | Blocked          | Passed          |
| GUEST to SERVER      | Blocked          | Passed          |
| GUEST to own gateway | Allowed          | Passed          |

## 7. Lessons Learned

- A trunk configuration alone does not guarantee that a physical link is active.
- Router-on-a-stick requires matching VLAN IDs between the switch trunk and router subinterfaces.
- DHCP pools must match the corresponding VLAN subnets and default gateways.
- ACL behavior should be verified through actual connectivity tests and ACL match counters.
