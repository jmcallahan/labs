## Associated Exam

- **CompTIA A+ Core 2**
    

---

## Objective Mapping

- **2.3** Given a scenario, troubleshoot common Windows OS problems
    
- **2.4** Given a scenario, manage disk partitions and volumes (indirect via restore disk usage)
    
- **3.1** Given a scenario, use operating system features and tools
    
- **3.3** Given a scenario, configure and use system utilities
    
- **4.1** Summarize malware removal best practices (rollback strategy)
    

---

## Scenario

You are responsible for endpoint stability at a mid-sized organization.  
A system exhibits performance degradation after software installation.  
Your task is to:

1. Observe **real-time performance**
    
2. Collect **historical performance data**
    
3. Identify **system reliability issues**
    
4. Roll the system back to a **known-good state**
    

This lab simulates real admin behavior when drivers, updates, or unwanted software destabilize a workstation.

---

## Learning Objectives

By completing this lab, you will be able to:

- Monitor live CPU and memory usage
    
- Collect performance metrics over time
    
- Build and analyze custom performance reports
    
- Interpret reliability history events
    
- Create, verify, and use System Restore points
    
- Validate rollback through logs and PowerShell
    

---

## Required Environment

- **OS:** Windows 11 Pro
    
- **Permissions:** Local Administrator
    
- **Tools:**
    
    - Resource Monitor
        
    - Performance Monitor
        
    - Reliability Monitor
        
    - Event Viewer
        
    - PowerShell (Admin)
        

## Deliverables (GitHub Proof-of-Work)

- Screenshots (sanitized):
    
    - Resource Monitor CPU view
        
    - Performance Monitor report
        
    - Reliability Monitor event
        
    - Restore confirmation
        
- PowerShell output (text)
    
- Lab markdown file
    
- `/screenshots/` directory
    
- `/notes/observations.md`
## Lab Tasks
### 1. Observe Real-Time System Performance (Resource Monitor)

#### Steps

1. Open **Windows Administrative Tools**
    
2. Launch **Resource Monitor**
    
3. On the **Overview** tab:
    
    - Expand **CPU**
        
    - Sort by **CPU** column
        
4. Launch **Microsoft Edge**, then minimize it
    
5. Confirm `msedge.exe` shows CPU usage
    
6. Expand **Memory** to view system memory statistics

---

### 2. Collect Baseline Performance Data (Default Data Collector Set)

#### Steps

1. Open **Performance Monitor**
    
2. Navigate to:
    
    `Data Collector Sets > System > System Performance`
    
3. Right-click **System Performance** → **Start**
    
4. Open and close **Microsoft Edge** twice
    
5. Wait for the collector to auto-stop (~1 minute)
    

#### Review the Report

1. Navigate to:
    
    `Reports > System > System Performance`
    
2. Open today’s report
    
3. Review summaries for:
    
    - CPU
        
    - Disk
        
    - Memory
        
    - Network
        
---

### 3. Create a Custom Data Collector Set

#### Configuration

|Setting|Value|
|---|---|
|Name|`test`|
|Creation Method|Manual (Advanced)|
|Data Type|Performance counters|
|Counters|Processor (All instances), Memory|
|Start Immediately|Yes|

#### Steps

1. Performance Monitor → **User Defined**
    
2. Right-click → **New > Data Collector Set**
    
3. Add counters:
    
    - `% Processor Time`
        
    - `Pages/sec`
        
    - `Page Faults/sec`
        
4. Start the collector
    
5. Open and close Edge twice
    
6. Stop the collector
    

#### View Collected Data

1. Navigate to:
    
    `Reports > User Defined > test`
    
2. Add counters to the report view
    
3. Switch between:
    
    - Line graph
        
    - Report view

---

### 4. Review System Stability (Reliability Monitor)

#### Steps

1. Open **View Reliability History**
    
2. Confirm no prior data (fresh VM)
    
3. Force an improper shutdown:
    
    - Power off VM without shutdown
        
4. Restart system
    
5. Reopen Reliability Monitor
    
6. Select today’s **Critical Event**
    
7. View technical details

---

### 5. Configure System Restore Protection

#### Steps

1. Search **Create a restore point**
    
2. Configure **Local Disk (C:)**
    
    - Enable protection
        
    - Set Max Usage to **1%** (lab-only shortcut)
        
3. Create restore point:
    
    - **Name:** `Test restore point`
        

#### Verify Creation

- Event Viewer → **Application Log**
    
- Event ID **8194**
    

#### PowerShell Validation

`Get-ComputerRestorePoint | Format-Table`

---

### 6. Simulate System Misconfiguration

#### Steps

1. Create folder:
    
    `C:\new-apps`
    
1. Extract provided cabinet file into folder
    
2. Install **Logitech Download Assistant**
    
3. Verify installation:
    
    - Apps & Features

---

### 7. Roll Back the System

#### Steps

1. Launch **System Restore**
    
2. Select **Test restore point**
    
3. **Scan for affected programs**
    
    - Confirm Logitech app is listed
        
4. Initiate restore
    
5. Reboot and wait for completion
    

#### Post-Restore Validation

- App removed from system
    
- Event Viewer:
    
    - Event ID **8199** (init)
        
    - Event ID **8202** (restoration)
        
- PowerShell:
    

`Get-ComputerRestorePoint`

---

## Observations & Failure Modes

|Issue|Cause|
|---|---|
|Restore point missing|Protection not enabled|
|Restore fails|Disk space exhausted|
|App still present|Installed after restore snapshot|
|Data loss|System Restore ≠ File Backup|

---

## Analysis & Reflection

- **Resource Monitor** = live triage
    
- **Performance Monitor** = historical diagnosis
    
- **Reliability Monitor** = stability timeline
    
- **System Restore** = rapid rollback
    

CompTIA is testing **tool selection logic**, not clicks.
## Exam Relevance Notes

### Common Traps

- Confusing **System Restore** with **Backup**
    
- Expecting Resource Monitor to log data
    
- Forgetting restore points are **per-disk**
### Key Terms

- Data Collector Set
    
- Performance Counters
    
- Reliability Index
    
- Restore Point
    
- Rollback