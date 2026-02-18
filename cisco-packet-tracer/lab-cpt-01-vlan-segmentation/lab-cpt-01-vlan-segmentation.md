---
title: VLAN Segmentation & IPv4 Addressing for Multi-Department Network
difficulty:
Cert:
  - Network+
  - NT10-009
Exam-Objectives:
  - Net+ 2.1
  - Net+ 2.2
  - Net+ 3.1
  - Net+5.3
created: 2026-02-14
completed:
time-hours: 3
complete: false
GitHub-ready: false
evidence-files: 10
notes:
tags:
  - "#network-plus-nt0-009"
  - "#switching"
  - vlans
  - ipv4-addressing
  - layer-2
  - packet-tracer
  - packet-analysis
  - cisco-catalyst
  - broadcast-domains
  - vlan-isolation
  - segmentation
---
---
## Scenario

You're a junior network engineer at a healthcare compliance firm standardizing network infrastructure across three departments: Clinical (patient systems), Administrative (HR/finance), and IT/Security. Your CISO mandates VLAN segmentation for HIPAA compliance—Clinical data cannot route directly to Administrative systems without inspection. You've been tasked with building a 3-VLAN network topology in Packet Tracer, assigning proper IPv4 addressing schemes, and validating inter-department connectivity constraints.

**Business Context:** Department isolation reduces lateral movement risk. Your topology must demonstrate proper VLAN tagging, subnet mask assignment, and switch port configuration to prepare for the production implementation review.

## Learning Objectives

By completing this lab, you will demonstrate:

- Configurable management of Layer 2 switching and VLAN creation
- IPv4 subnetting and CIDR notation applied to real departmental requirements
- Proper VLAN tagging on trunk ports and access port assignment
- Troubleshooting of common VLAN addressing misconfigurations
- Documentation of network topology with compliance mapping

## Required Environment

### Hardware/Software

- Cisco Packet Tracer (6.0+, free via Cisco NetAcad)
- Host system with minimum 4GB RAM available
- VMware or Hyper-V (optional—PT runs standalone)

### Devices Required

- 1x Cisco Catalyst 2960 Switch (or equivalent managed switch)
- 3x Cisco 2811 Routers (for inter-VLAN routing in Lab 3—foundation for this lab)
- 6x End devices (3x Desktop computers, 3x IP Phones or laptops)
- Console cable for switch configuration

### Network Design Specifications

```
VLAN 10 (Clinical):     10.1.10.0/24    (Subnet mask: 255.255.255.0)
VLAN 20 (Administrative): 10.1.20.0/24  (Subnet mask: 255.255.255.0)
VLAN 30 (IT/Security):   10.1.30.0/24   (Subnet mask: 255.255.255.0)
Trunk Link (Switch-to-Router): 802.1Q tagged
```

## Deliverables

### Evidence Files (Required)

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

### Documentation (Required)

- **Network topology diagram** (ASCII or screenshot) showing:
    - 3 end devices per VLAN, color-coded by department
    - Switch with VLAN assignments labeled on each port
    - Trunk port marked on Fa0/24
    - Subnet masks and gateway IPs per VLAN
- **VLAN configuration notes** documenting:
    - Why VLAN 10,20,30 chosen (align with business role)
    - /24 subnet justification (supports 254 hosts per department—adequate for current + growth)
    - Port assignments and reasoning
- **Connectivity matrix** showing:
    - Which VLANs can reach each other (currently: none—Layer 2 isolation only)
    - Expected vs. actual ping results

## Lab Tasks

### Phase 1: Topology Build & Device Placement (30 minutes)

- [x] Launch Cisco Packet Tracer and create new project
- [x] Add 1x Catalyst 2960 Switch to canvas
- [x] Add 6x Desktop computers (or mix of desktops/IP phones) to canvas
- [x] Add 1x 2811 Router (for future inter-VLAN routing; leave disconnected for this phase)
- [x] Label devices clearly: Clinical-PC-1, Clinical-PC-2, Admin-PC-1, Admin-PC-2, IT-PC-1, IT-PC-2
- [x] Connect devices to switch using Ethernet copper cables:
    - Clinical PCs → Fa0/1 and Fa0/2
    - Admin PCs → Fa0/3 and Fa0/4
    - IT PCs → Fa0/5 and Fa0/6
    - Leave Fa0/24 open for future trunk link
- [x] **Evidence:** Screenshot full topology before configuration (`01-initial-topology.png`)

### Phase 2: VLAN Creation & Configuration (45 minutes)

#### Step 2a: Access Switch CLI and Create VLANs

- [x] Right-click switch → **Open CLI** (or use console port with Console cable)
- [x] Enter privileged exec mode:

```bash
Switch> enable
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name Clinical
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name Administrative
Switch(config-vlan)# vlan 30
Switch(config-vlan)# name IT-Security
Switch(config-vlan)# end
Switch# write memory
```

- [x] **Verify VLAN creation:**

```bash
Switch# show vlan brief
```

Expected output should show:

```
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/7,Fa0/8... (other ports)
10   Clinical                         active    
20   Administrative                   active    
30   IT-Security                      active    
```

- [x] **Evidence:** Screenshot VLAN database showing all 3 VLANs created (`02-pt-lab1-switch-vlan-config.png`)

#### Step 2b: Assign Access Ports to VLANs

- [x] Configure Fa0/1 and Fa0/2 for VLAN 10 (Clinical):

```bash
Switch(config)# interface fastethernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# no shutdown
Switch(config-if)# interface fastethernet 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# no shutdown
Switch(config-if)# end
```

- [x] **Evidence:** Screenshot interface config for Fa0/1-2 (`03-pt-lab1-vlan-10-assigned-ports.png`)
    
- [x] Configure Fa0/3 and Fa0/4 for VLAN 20 (Administrative):
    

```bash
Switch(config)# interface fastethernet 0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# no shutdown
Switch(config-if)# interface fastethernet 0/4
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# no shutdown
Switch(config-if)# end
```

- [ ] **Evidence:** Screenshot interface config for Fa0/3-4 (`04-pt-lab1-vlan-20-assigned-ports.png`)
    
- [x] Configure Fa0/5 and Fa0/6 for VLAN 30 (IT/Security):
    

```bash
Switch(config)# interface fastethernet 0/5
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 30
Switch(config-if)# no shutdown
Switch(config-if)# interface fastethernet 0/6
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 30
Switch(config-if)# no shutdown
Switch(config-if)# end
```

- [x] **Evidence:** Screenshot interface config for Fa0/5-6 (`05-pt-lab1-vlan-30-assigned-ports.png`)
    
- [x] **Verify all VLAN assignments:**
    

```bash
Switch# show vlan brief
Switch# show interface fastethernet 0/1 switchport
Switch# show interface fastethernet 0/3 switchport
Switch# show interface fastethernet 0/5 switchport
```

#### Step 2c: Configure Trunk Port (Preparation for Lab 3)

- [x] Configure Fa0/24 as trunk port (will connect to router for inter-VLAN routing in Lab 3):

```bash
Switch(config)# interface fastethernet 0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30
Switch(config-if)# no shutdown
Switch(config-if)# end
```

- [x] **Evidence:** Screenshot trunk config on Fa0/24 (`06-pt-lab1-trunk-port-config.png`)
    
- [x] **Verify trunk:**
    

```bash
Switch# show interface fastethernet 0/24 switchport
```

Expected: `Operational Mode: trunk`, `Trunking VLANs Enabled: 10,20,30`

### Phase 3: End Device IP Configuration (30 minutes)

#### Step 3a: Configure Clinical VLAN (VLAN 10) Devices

- [x] Right-click **Clinical-PC-1** → **Configure** (or Desktop tab)
    
- [x] Click **IP Configuration** → **Static**
    
- [x] Assign IPv4:
    
    - IP Address: `10.1.10.10`
    - Subnet Mask: `255.255.255.0`
    - Default Gateway: `10.1.10.1` (leave blank for now—Lab 3 will add router)
- [x] **Evidence:** Screenshot IP config (`07-pt-lab1-device-ip-assignment.png`)
    
- [x] Repeat for **Clinical-PC-2**:
    
    - IP Address: `10.1.10.11`
    - Subnet Mask: `255.255.255.0`
    - Default Gateway: `10.1.10.1`

#### Step 3b: Configure Administrative VLAN (VLAN 20) Devices

- [x] Right-click **Admin-PC-1** → **Configure**
    
- [x] Assign IPv4:
    
    - IP Address: `10.1.20.10`
    - Subnet Mask: `255.255.255.0`
    - Default Gateway: `10.1.20.1`
- [x] **Evidence:** Screenshot IP config (`08-pt-lab1-device-ip-assignment-admin.png`)
    
- [x] Repeat for **Admin-PC-2**:
    
    - IP Address: `10.1.20.11`
    - Subnet Mask: `255.255.255.0`
    - Default Gateway: `10.1.20.1`

#### Step 3c: Configure IT/Security VLAN (VLAN 30) Devices

- [x] Right-click **IT-PC-1** → **Configure**
    
- [x] Assign IPv4:
    
    - IP Address: `10.1.30.10`
    - Subnet Mask: `255.255.255.0`
    - Default Gateway: `10.1.30.1`
- [x] **Evidence:** Screenshot IP config (`09-pt-lab1-device-ip-assignment-it.png`)
    
- [x] Repeat for **IT-PC-2**:
    
    - IP Address: `10.1.30.11`
    - Subnet Mask: `255.255.255.0`
    - Default Gateway: `10.1.30.1`

### Phase 4: Connectivity Testing & VLAN Isolation Verification (30 minutes)

#### Step 4a: Test Same-VLAN Connectivity (Should SUCCEED)

- [x] Click **Clinical-PC-1** → **Command Prompt**
- [x] Ping Clinical-PC-2:

```bash
C:\> ping 10.1.10.11
```

Expected output:

```
Pinging 10.1.10.11 with 32 bytes of data:
Reply from 10.1.10.11: bytes=32 time=1ms TTL=128
Reply from 10.1.10.11: bytes=32 time=1ms TTL=128
Reply from 10.1.10.11: bytes=32 time=1ms TTL=128
Reply from 10.1.10.11: bytes=32 time=1ms TTL=128

Ping statistics for 10.1.10.11:
    Packets sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
    Minimum = 1ms, Maximum = 1ms, Average = 1ms
```

- [x] **Evidence:** Screenshot successful ping within VLAN 10 (`10-pt-lab1-same-vlan-connectivity.png`)

#### Step 4b: Test Cross-VLAN Connectivity (Should FAIL—Expected)

- [x] From Clinical-PC-1, ping Admin-PC-1:

```bash
C:\> ping 10.1.20.10
```

Expected: **Request timed out** (no route between VLANs—Layer 2 isolation working)

- [x] **Evidence:** Screenshot failed ping (cross-VLAN isolation) (`11-pt-lab1-vlan-isolation-test.png`)
    
- [x] **Document expected failure:**
    
    - Why it fails: No Layer 3 device routing between VLANs; switch only performs Layer 2 forwarding
    - This is intentional: VLANs provide isolation; inter-VLAN routing comes in Lab 3

#### Step 4c: Verify Switch Port Membership

- [x] From switch CLI, verify VLAN membership:

```bash
Switch# show vlan brief
Switch# show mac-address-table
```

Expected: MAC addresses from each device listed under correct VLAN

### Phase 5: Documentation & Analysis (30 minutes)

- [ ] Create **network topology diagram** showing:
    - 3-VLAN architecture with devices color-coded
    - VLAN 10 (red): Clinical
    - VLAN 20 (blue): Administrative
    - VLAN 30 (green): IT/Security
    - Trunk link on Fa0/24
    - All IP addresses and subnet masks labeled
- [ ] Document **VLAN design justification:**
    - Why /24 subnetting: Supports up to 254 hosts per VLAN; Clinical dept has ~50 systems, Admin ~40, IT ~30—leaves room for growth
    - Why these VLAN IDs: 10,20,30 follow common convention (tens = departments); easy to remember and extend
    - Compliance mapping: HIPAA requires patient data (VLAN 10) isolated from financial (VLAN 20); IT (VLAN 30) handles monitoring of both
- [ ] Create **connectivity matrix** (table):

|Source|Destination|Expected|Actual|Reason|
|---|---|---|---|---|
|Clinical-PC-1|Clinical-PC-2|✓ Ping|✓ Success|Same VLAN, same broadcast domain|
|Clinical-PC-1|Admin-PC-1|✗ Ping|✗ Timeout|Different VLANs, no Layer 3 routing|
|Admin-PC-1|IT-PC-1|✗ Ping|✗ Timeout|Different VLANs, no Layer 3 routing|
|Admin-PC-1|Admin-PC-2|✓ Ping|✓ Success|Same VLAN, same broadcast domain|

- [ ] **Time log:**
    - Phase 1: ___ min
    - Phase 2: ___ min
    - Phase 3: ___ min
    - Phase 4: ___ min
    - Phase 5: ___ min
    - **Total:** ___ min (target: 150-180 min / 2.5-3 hours)

## Observations & Failure Modes

**Issue:** Ping between devices in same VLAN fails

- **Cause:** Access port not properly assigned to VLAN; device connected to wrong port; IP address misconfiguration
- **Solution:**
    - Verify port is in `switchport mode access` (not trunk)
    - Confirm VLAN assignment: `show interface Fa0/X switchport`
    - Check device IP is in correct subnet (10.1.X.0/24)
    - Use `show mac-address-table` to verify switch learned device MAC

**Issue:** Cross-VLAN ping succeeds when it should fail

- **Cause:** Router is already in topology and routing between VLANs (unexpected for Lab 1); wrong device IP assigned to wrong VLAN
- **Solution:**
    - Verify each device is connected to correct access port
    - Confirm VLAN assignments on all ports: `show vlan brief`
    - If router present, disconnect it or check its routing table

**Issue:** Trunk port configuration error ("allowed vlan 10,20,30" not recognized)

- **Cause:** Syntax error; some PT versions use different command format
- **Solution:**
    - Try alternate: `switchport trunk allowed vlan add 10,20,30` or `switchport trunk allowed vlan all`
    - Verify with `show interface Fa0/24 switchport | include Trunking`

**Issue:** Devices cannot ping even within same VLAN

- **Cause:** Device not powered on; cable not connected (red X on cable); wrong cable type (should be copper Ethernet)
- **Solution:**
    - Right-click device → **Power On**
    - Check cable connections (should be solid green/yellow line)
    - Use copper Ethernet cable, not fiber

**Issue:** Switch won't accept VLAN commands in config mode

- **Cause:** Switch doesn't support VLANs (wrong device selected—need managed switch, not unmanaged)
- **Solution:**
    - Verify using Catalyst 2960 or 2950 (Layer 2 managed switch)
    - Avoid unmanaged switches in Packet Tracer

## Analysis & Reflection

### Security Observations

- **What's insecure here:** VLANs provide Layer 2 isolation only; no encryption of inter-VLAN traffic (yet). If router is added without access control lists (ACLs), all traffic between VLANs flows freely.
- **Production hardening:** In Lab 3, add router with ACLs to restrict Clinical ↔ Administrative traffic. Block VLAN 10 from initiating connections to VLAN 20 (read-only access only).
- **Attack vector identified:** VLAN hopping via 802.1Q double-tagging (not applicable in this basic PT lab, but real concern in production). Configure trunk ports to untrust end-device ports.

### Real-World Application

In production healthcare networks, VLAN segmentation like this separates:

- **Clinical systems (VLAN 10):** EHR servers, imaging systems—PII and PHI must not leak to admin
- **Administrative (VLAN 20):** HR/payroll—financial PII
- **IT/Security (VLAN 30):** Monitoring, logging, security appliances—has visibility into both

Without this isolation, a compromised admin workstation could move laterally to clinical systems and exfiltrate patient data.

### Time Tracking

- Design time (before PT): ~10 min (subnet planning, VLAN numbering)
- Build time (Phase 1): ~25 min (topology placement)
- Configuration time (Phases 2-3): ~50 min (VLAN creation, port assignment, IP config)
- Testing time (Phase 4): ~20 min (connectivity verification)
- Documentation time (Phase 5): ~30 min (topology diagram, analysis)
- **Total lab time: ~2.5-3 hours (Intermediate difficulty)**

## Exam Relevance Notes

**N10-009 Direct Mappings:**

- **Objective 2.1 (Switches, managed vs. unmanaged):** This lab uses Catalyst 2960 (managed Layer 2 switch). The exam tests knowledge of when managed switches are required (VLAN support, STP, port security) vs. unmanaged (simple forwarding only). Evidence: Switch CLI output showing VLAN database.
    
- **Objective 2.2 (VLAN concepts and inter-VLAN routing):** Lab demonstrates VLAN creation, port assignment, and intentional isolation. The exam question format: "A company wants to separate clinical and admin networks. Which feature isolates broadcast domains?" Answer: VLANs on a managed switch. Evidence: Connectivity matrix showing isolation.
    
- **Objective 3.1 (IPv4 addressing, subnetting, CIDR):** Lab applies /24 subnetting across three departments. Exam format: "A company has 10.1.10.0/24 for VLAN 10. How many usable hosts?" Answer: 254 (2^8 - 2). Evidence: Device IP config screenshots.
    
- **Objective 5.3 (Troubleshooting connectivity issues):** Common exam scenario: "Devices on VLAN 10 can't ping VLAN 20. Why?" Expected troubleshooting path: Check VLAN assignment → Check IP subnet → Check for routing. Evidence: Ping tests and failure analysis.
    

**Exam Scenario Examples:**

- "You're deploying a network with three departments. Subnet planning shows each needs 254 hosts. Which addressing scheme and VLAN design best meets this requirement?" _Answer: /24 per VLAN, with separate VLANs per department._
- "A switch has ports assigned to VLAN 10 and 20. Devices on VLAN 10 cannot reach VLAN 20. What is required?" _Answer: Layer 3 router with inter-VLAN routing (covered in Lab 3)._
- "Which port configuration supports multiple VLANs?" _Answer: Trunk port (802.1Q tagging)._

## Submission Checklist

- [x] All 11 evidence files captured and numbered sequentially
- [x] Switch VLAN configuration verified (`show vlan brief`, `show interface Fa0/X switchport`)
- [x] All 6 devices have IP addresses assigned to correct VLANs
- [x] Same-VLAN ping succeeds; cross-VLAN ping fails (as expected)
- [x] Trunk port on Fa0/24 configured with allowed VLANs 10,20,30
- [x] Network topology diagram created with VLAN labels and IP addresses
- [x] Connectivity matrix documents expected vs. actual results
- [x] Failure modes section completed with at least 3 common issues
- [x] Analysis section includes security observations and production hardening notes
- [x] Time log filled in; total duration 2.5-3 hours
- [x] Documentation uses professional language; no ambiguity about VLAN purpose
- [x] Ready for GitHub commit as `01-pt-lab1-vlan-ipv4-segmentation.md`