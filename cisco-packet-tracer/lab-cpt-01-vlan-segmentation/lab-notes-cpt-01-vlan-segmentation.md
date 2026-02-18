## Logical Network VLAN Segmentation 

#### What I'm doing:
- Created the following devices:
	- Lab-PC-1 & Lab-PC-2
	- VoIP-1 & VoIP-2
	- Admin-PC-1 & Admin-PC-2
	- IT-PC-1 & IT-PC-2
	- Server-1 & Server-2
	- WAP-1 & WAP-2
		- Laptop0 & Laptop1
	- Printer0
	- Router0
	- Cable Modem0
- Created VLANs to segment previously made devices:
	- VLAN 10 - Lab-PCs & Printer0
	- VLAN 20 - Admin-PCs
	- VLAN 30 - IT-PCs
	- VLAN 40 - VoIPs
	- VLAN 50 - Servers
	- VLAN 60 - WAPs

## Commands used:
```node
Switch:
-enable
-config terminal
-interface fastethernet #/# 
-switchport mode access
-switchport access vlan ## 
-no shutdown
-end
-show vlan brief

 Command Prompt:
 -Ping
```

#### Observations
1. Pinging a known IP on a segmented subnet will time out and produce now ping
	1. That's actually the point of segmentation
	2. This fails because the ping can't traverse a Layer 3 device between VLANs
		1. The switch only does layer 2

#### Problems hit:
- **Issue:** Most devices didn't have MAC Addresses when the CLI command was sent from the switch
- **Root cause:** After investigation, this seems to be a Cisco thing, devices show MAC addresses like [00CE.R501.1010] but not in the standard 00:11:AA:BB:22:CC kind of way.
- **Fix:** Any attempt to manually assign a MAC address was met with errors. Turns out Cisco numbers their MAC address in their over way and the IEEE standard, it's the same address, just formatted differently.
#### Connectivity Matrix:

| Source        | Destination | Expected | Actual    | Reason                              |
| ------------- | ----------- | -------- | --------- | ----------------------------------- |
| Lab-PC-1      | Lab-PC-2    | ✓ Ping   | ✓ Success | Same VLAN, same broadcast domain    |
| Clinical-PC-1 | Admin-PC-1  | ✗ Ping   | ✗ Timeout | Different VLANs, no Layer 3 routing |
| Admin-PC-1    | IT-PC-1     | ✗ Ping   | ✗ Timeout | Different VLANs, no Layer 3 routing |
| Admin-PC-1    | Admin-PC-2  | ✓ Ping   | ✓ Success | Same VLAN, same broadcast domain    |

### Time spent: 180 minutes
