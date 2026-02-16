# Small-Enterprise-VLAN-Inter-VLAN-Routing-Router-on-a-stick-
Build a small two switch network with multiple VLANS, trunking DHCP, and inter-VLAN routing
<img width="1101" height="643" alt="Screenshot 2026-02-16 112558" src="https://github.com/user-attachments/assets/e05cd657-8752-4cd5-a903-ef274fdd3dab" />

Topology
- Devices: 1 Router (R1), 2 Switches (S1, S2), 4 PCs (PC1–PC4).
- Links: R1 G0/0 ↔ S1 G0/1 (trunk), S1 G0/2 ↔ S2 G0/1 (trunk), PCs to access ports.
- IP plan
VLAN	Name	Subnet	Gateway (R1)	DHCP Range
- 10	USERS	192.168.10.0/24	192.168.10.1	192.168.10.100–192.168.10.199
- 20	SERVERS	192.168.20.0/24	192.168.20.1	192.168.20.100–192.168.20.199
- 30	GUEST	192.168.30.0/24	192.168.30.1	192.168.30.100–192.168.30.199
- 99	MGMT	192.168.99.0/24	192.168.99.1	192.168.99.50–192.168.99.99

1.	Open Packet Tracer → drag in: 1x Router (e.g., 2911), 2x Switch (2960), 4x PCs.
2.	Cable: R1 G0/0 to S1 G0/1. Cable S1 G0/2 to S2 G0/1. Connect PCs to access ports on S1 and S2.
3.	On S1: create VLANs 10, 20, 30, 99; name them USERS, SERVERS, GUEST, MGMT.
4.	On S1: set G0/1 as trunk (to router) and G0/2 as trunk (to S2). Allow VLANs 10, 20, 30, 99.
5.	On S2: create the same VLANs. Set G0/1 as trunk. Allow VLANs 10, 20, 30, 99.
6.	Assign access ports: PC1→VLAN10, PC2→VLAN20, PC3→VLAN30, PC4→VLAN10 (or any mix you want).
7.	Configure R1 subinterfaces on G0/0 for VLANs 10/20/30/99 with 802.1Q encapsulation and gateway IPs.
8.	Configure DHCP pools on R1 for each VLAN (exclude .1–.99 as needed).
9.	Set each PC to DHCP. Verify they receive an IP in the right subnet.
10.	Test: ping PC1 → PC2 (inter-VLAN), PC1 → default gateway, PC1 → PC3.
11.	Save the project as: whatever you want .pkt file

Configs (copy/paste)
- S1 (2960) – VLANs, trunks, access ports (adjust port numbers to match your cabling):
- enable
- conf t
- vlan 10
- name USERS
- vlan 20
-name SERVERS
- vlan 30
- name GUEST
- vlan 99
- name MGMT
- interface g0/1
- switchport mode trunk
- switchport trunk allowed vlan 10,20,30,99
- interface g0/2
- switchport mode trunk
- switchport trunk allowed vlan 10,20,30,99
- interface f0/1
- switchport mode access
- switchport access vlan 10
- spanning-tree portfast
- interface f0/2
- switchport mode access
 - switchport access vlan 20
 - spanning-tree portfast
- end
- wr

<img width="900" height="921" alt="Screenshot 2026-02-14 135258" src="https://github.com/user-attachments/assets/0ec90239-3ec4-436f-a39b-751da2da8839" />

S2 (2960) – match VLANs and trunk:
- Enable
- conf t
- vlan 10
- name USERS
- vlan 20
- name SERVERS
- vlan 30
- name GUEST
- vlan 99
- name MGMT
- interface g0/1
- switchport mode trunk
- switchport trunk allowed vlan 10,20,30,99
- end
- wr

<img width="690" height="561" alt="Screenshot 2026-02-14 135427" src="https://github.com/user-attachments/assets/0de3c6f1-0daa-4bd6-bdb6-c68a4f083b4b" />

On Router 1(2911) - subinterfaces+DHCP
- enable
- conf t
- interface g0/0
- no shut
- interface g0/0.10
- encapsulation dot1Q 10
- ip address 192.168.10.1 255.255.255.0
- interface g0/0.20
- encapsulation dot1Q 20
- ip address 192.168.20.1 255.255.255.0
- interface g0/0.30
- encapsulation dot1Q 30
- ip address 192.168.30.1 255.255.255.0
- interface g0/0.99
- encapsulation dot1Q 99
- ip address 192.168.99.1 255.255.255.0
- ip dhcp excluded-address 192.168.10.1 192.168.10.99
- ip dhcp excluded-address 192.168.20.1 192.168.20.99
- ip dhcp excluded-address 192.168.30.1 192.168.30.99
- ip dhcp excluded-address 192.168.99.1 192.168.99.49
- ip dhcp pool VLAN10
- network 192.168.10.0 255.255.255.0
- default-router 192.168.10.1
- dns-server 8.8.8.8
- ip dhcp pool VLAN20
- network 192.168.20.0 255.255.255.0
- default-router 192.168.20.1

<img width="781" height="938" alt="Screenshot 2026-02-14 140500" src="https://github.com/user-attachments/assets/5cfa6f49-bc37-48ee-a734-9ce83d8b82e1" />

Verification checklist at the end
- S1/S2: show vlan brief
- S1/S2: show interfaces trunk
- R1: show ip interface brief
- PCs: confirm IP/gateway per VLAN
- Ping between VLANs (or isolate where it fails)

<img width="1050" height="616" alt="Screenshot 2026-02-16 114406" src="https://github.com/user-attachments/assets/a21b91b2-c3c3-44c2-8684-3f1d3c196ca2" />

