# OSPF-Multi-router-connectivity-mini-lab

OSPF Multi-Router Connectivity Lab (Cisco Packet Tracer)

# Overview

This lab demonstrates how to configure OSPF (Open Shortest Path First) across multiple routers to allow communication between two remote LAN networks.

# Concepts covered

OSPF process configuration
Router ID assignment
OSPF neighbor formation
Route advertisement
Troubleshooting missing routes

# Network Topology

PC1 ---- R1 ---- R2 ---- R3 ---- PC2

# Network Segments

Network	Purpose
192.168.1.0/24	LAN1
10.0.12.0/30	R1–R2 link
10.0.23.0/30	R2–R3 link
192.168.2.0/24	LAN2

# IP Addressing Plan

Device	Interface	IP Address
PC1	NIC	192.168.1.10/24
R1	G0/0	192.168.1.1/24
R1	G0/1	10.0.12.1/30
R2	G0/0	10.0.12.2/30
R2	G0/1	10.0.23.1/30
R3	G0/0	10.0.23.2/30
R3	G0/1	192.168.2.1/24
PC2	NIC	192.168.2.10/24

Default Gateways
PC1 → 192.168.1.1
PC2 → 192.168.2.1

# Step 1 — Configure Router Interfaces

Router 1
conf t
interface g0/0
ip address 192.168.1.1 255.255.255.0
no shutdown

interface g0/1
ip address 10.0.12.1 255.255.255.252
no shutdown
Router 2
conf t
interface g0/0
ip address 10.0.12.2 255.255.255.252
no shutdown

interface g0/1
ip address 10.0.23.1 255.255.255.252
no shutdown
Router 3
conf t
interface g0/0
ip address 10.0.23.2 255.255.255.252
no shutdown

interface g0/1
ip address 192.168.2.1 255.255.255.0
no shutdown

# Step 2 — Configure OSPF

Router 1
router ospf 1
router-id 1.1.1.1
network 192.168.1.0 0.0.0.255 area 0
network 10.0.12.0 0.0.0.3 area 0

Router 2
router ospf 2
router-id 2.2.2.2
network 10.0.12.0 0.0.0.3 area 0
network 10.0.23.0 0.0.0.3 area 0

Router 3
router ospf 3
network 10.0.23.0 0.0.0.3 area 0
network 192.168.2.0 0.0.0.255 area 0

# Step 3 — Verify OSPF Neighbor Formation

Command
show ip ospf neighbor

Example output

Neighbor ID     State       Address
2.2.2.2         FULL/DR     10.0.12.2
3.3.3.3         FULL/BDR    10.0.23.2

This confirms:
Neighbor discovery
Database synchronization
Successful adjacency

# Step 4 — Verify Routing Table

show ip route

Example

O 10.0.23.0/30 via 10.0.12.2
O 192.168.2.0/24 via 10.0.12.2

Route codes:

Code	Meaning
C	Connected
L	Local
O	OSPF learned route
Issue Encountered During Lab

Ping from PC1 to PC2 failed initially.
Destination unreachable
Router1 routing table showed that 192.168.2.0/24 was missing.

Root Cause:

Router3 had an incorrect OSPF network command:
network 192.168. 0.0.0.255 area 0
Because of this, Router3 did not advertise its LAN network.

Solution:

Correct Router3 configuration:
router ospf 3
network 192.168.2.0 0.0.0.255 area 0

After fixing this, OSPF propagated the route to all routers.

Connectivity Test:

From PC1
ping 192.168.2.10

Result
Reply from 192.168.2.10
Connectivity successfully established.

Packet Flow

Forward path
PC1 → R1 → R2 → R3 → PC2

Return path

PC2 → R3 → R2 → R1 → PC1
Lab Outcome

✔ OSPF adjacency established
✔ Routes successfully exchanged
✔ End-to-end connectivity achieved
