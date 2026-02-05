### Build Specs:
 - Memory: 8 GB
 - Processors: 2
 - Hard Disk(NVMe): 64 GB
 - Network Adapter: NAT
 - TPM: Present
 - Hypervisor: VMware Workstation Pro

### Thin vs Thick Provisioning:
- N/A - VMware Workstations use dynamically expanding virtual disks

### Time drift: 
- #### Observed issues:
	- Time zone misalignment, but not time drift
	- While VM system time was correct it displayed time in PST rather than host CST
- #### Root Cause: 
	- Guest OS time zone misconfiguration, not clock drift
- #### Resolution: 
	- Corrected guest system time zone to CST
	- Enable VMware Tools guest time synchronization to prevent future drift
- #### Validation:
	- Guest matched Host time after reboot