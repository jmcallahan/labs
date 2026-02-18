***The contents of this folder includes progress screenshots, lab guide, and lab notes.***
- Feel free to test this lab out for yourself
## Deliverables
#### Evidence Files (Required)
1. `01-pt-lab1-switch-vlan-config.png` – Switch running config showing VLAN database
2. `02-pt-lab1-vlan-10-assigned-ports.png` – Interface config (Fa0/1-2) assigned to VLAN 10
3. `03-pt-lab1-vlan-20-assigned-ports.png` – Interface config (Fa0/3-4) assigned to VLAN 20
4. `04-pt-lab1-vlan-30-assigned-ports.png` – Interface config (Fa0/5-6) assigned to VLAN 30
5. `05-pt-lab1-trunk-port-config.png` – Fa0/24 trunk configuration (802.1Q, allowed VLANs 10,20,30)
6. `06-pt-lab1-device-ip-assignment.png` – Clinical Desktop IP: 10.1.10.10, Gateway: 10.1.10.1
7. `07-pt-lab1-device-ip-assignment-admin.png` – Admin Desktop IP: 10.1.20.10, Gateway: 10.1.20.1
8. `08-pt-lab1-device-ip-assignment-it.png` – IT Desktop IP: 10.1.30.10, Gateway: 10.1.30.1
9. `09-pt-lab1-vlan-isolation-test.png` – Ping from Clinical to Admin (FAILS—expected isolation)
10. `10-pt-lab1-same-vlan-connectivity.png` – Ping within VLAN 10 (SUCCEEDS—same broadcast domain)
#### Documentation (Required)
- [x] **VLAN configuration notes** documenting:
    - Why VLAN 10,20,30 chosen (align with business role)
    - /24 subnet justification (supports 254 hosts per department—adequate for current + growth)
    - Port assignments and reasoning
- [x] **Connectivity matrix** showing:
    - Which VLANs can reach each other (currently: none—Layer 2 isolation only)
    - Expected vs. actual ping results