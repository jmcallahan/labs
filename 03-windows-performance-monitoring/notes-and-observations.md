### VM Build Specs
-  Memory: 8 GB
 - Processors: 2
 - Hard Disk(NVMe): 64 GB
 - Network Adapter: NAT
 - TPM: Present
 - OS: Windows 11 Pro
 - Hypervisor: VMware Workstation Pro
### System Performance Observations:
#### Resource Monitor Observations:
- Resource monitor answers "What's happening right now?" and is best used for immediate triage, not historical analysis.
	- CPU usage fluctuates per process.
	- Memory shows commit, working set, and faults.
	- This data is **real-time only**.
		- Does not retain historical data'
#### Performance Monitor Observations
- Default collectors are **read-only**.
- Includes recommended counters only.
- Customizable, but verify re-usability vs. single use user custom collectors
	- Custom collectors can answer what happened over a time they were engaged
	- Helpful for intermittent issues
#### Reliability Monitor: 
- An Improper shutdown recorded after the force power-off    
- Reliability Index updates automatically
	- Will show events ranging from Normal, to Warning, to Critical
- It is:
	- Fast
	- High-Level
	- Timeline-based
- Use it before Event Viewer for Context
#### Restore Point Creation & Rollback:
- System Restore is designed for:
	- Bad drivers
	- Problematic software
	- Not user data recovery
- Allow for rapid rollback of system state - drivers, registry, installed apps; while typically retaining user files and data. 
	- ***DO NOT*** rely on this as a data protection mechanism!

### Key Takeaways:
- Tool Selection:
	- Resource Monitor = Live Triage
	- Performance Monitor = Historical Analysis
	Reliability Monitor = Stability Context
	System Restore = Rapid rollback, not a backup or full disk image
