## Associated Exam

- CompTIA A+ Core 2 (220-1202)
    

## Objective Mapping

- **2.11** Given a scenario, troubleshoot common Windows OS issues _(includes time drift)_
    
- **4.1** Given a scenario, apply change management procedures _(rollback plan, sandbox testing, documented change)_
    
- **3.4** Given a scenario, implement best practices associated with documentation and support systems information management
    

## Scenario

Hexelo needs a standardized Windows desktop VM for internal testing. You must:

1. create the VM with thin-provisioned storage,
    
2. install Windows,
    
3. install guest tools,
    
4. correct/avoid **time drift** (a real cause of auth and logging weirdness).
    

## Learning Objectives

- Create a VM in vSphere using correct compute/storage placement.
    
- Explain **thin vs thick provisioning** and why it matters operationally.
    
- Install guest OS and guest tools.
    
- Diagnose and remediate **time drift** using VM time sync.
    
- Document the build in a ticket/SOP-style format.
    

## Required Environment

### Primary (matches lab)

- vCenter/vSphere 8 environment (lab or org)
    
- Datastore containing Windows ISO
    
- Permissions to create/edit/power on VMs
    

### Home-lab adaptation (if you don’t have vCenter)

- VMware Workstation + a Windows VM is fine for **guest tools** concepts
    
- Time sync concepts still apply (host sync / NTP), but UI steps differ
    
## Deliverables
**What goes in your repo:**
### Evidence files (required)

- `evidence/lab-01-vsphere-winvm-time-sync/01-vm-created.png`
    
    - VM Summary page showing **Desktop01**, datastore **Local-DS**, disk **40GB**, and **thin** provisioning (or disk details pane).
        
- `evidence/lab-01-vsphere-winvm-time-sync/02-iso-mounted.png`
    
    - Edit Settings showing **WIN_ENT_10_64BIT.ISO** mounted + **Connect at power on** checked.
        
- `evidence/lab-01-vsphere-winvm-time-sync/03-windows-installed.png`
    
    - Windows desktop after first login (or Windows “About” page showing OS installed).
        
- `evidence/lab-01-vsphere-winvm-time-sync/04-vmware-tools-installed.png`
    
    - Screenshot of VMware Tools installed status (VM Summary “VMware Tools: Running/Installed” or Programs & Features).
        
- `evidence/lab-01-vsphere-winvm-time-sync/05-time-sync-enabled.png`
    
    - VM Options → VMware Tools showing **Synchronize time periodically** enabled.
        

### Notes (required)

- `notes/lab-01-vsphere-winvm-time-sync.md` containing:
    
    - Build specs (CPU/RAM/disk/network, datastore, host)
        
    - Thin vs thick: 2–3 bullets on **why** thin was chosen
        
    - Time drift: what you observed and how you validated time after enabling sync
        
    - Any failure modes you hit and how you fixed them

## Lab Tasks

### Task 1 — Create the VM (vSphere Web Client)

1. Open browser → vCenter URL → login.
    
2. Select host/cluster (per lab) → **Actions → New Virtual Machine**
    
3. Use:
    
    - Name: `Windows 11 x64`
        
    - Compute: `esxi01...`
        
    - Storage: `Local-DS`
        
    - Guest OS: Windows / Windows 10 (64-bit)
        
    - Memory: 2 GB
        
    - Disk: 40 GB
        
    - Disk provisioning: **Thin**
        

**Proof artifact**

- Screenshot: VM Summary page showing name, datastore, disk size/type.
    

### Task 2 — Mount ISO + Install Windows

1. VM → **Edit Settings**
    
2. CD/DVD drive → **Datastore ISO File** → select Windows ISO
    
3. Check **Connect at Power On**
    
4. Power on → Launch console → boot from CD
    
5. Install Windows to **Drive 0 Unallocated Space**
    
6. Create local user `admin` with blank password _(lab requirement; note: insecure in real life)_
    

**Proof artifact**

- Screenshot: Disk selection screen (Drive 0) OR desktop after install.
    

### Task 3 — Install VMware Tools

1. VM → Actions → Guest OS → **Install VMware Tools**
    
2. In Windows, open DVD drive → run installer → Typical → reboot.
    

**Proof artifact**

- Screenshot: “VMware Tools” showing installed version (or Programs & Features).
    

### Task 4 — Configure Time Sync (fix time drift)

1. VM → **Edit Settings**
    
2. VM Options → VMware Tools → enable **Synchronize time with host** (periodic)
    

**Validation**

- In Windows: confirm clock matches expected time.
    
- If time is still off: document what you checked (timezone vs sync vs NTP).
    

**Proof artifact**

- Screenshot: VM Options time sync enabled.
    

## Observations & Failure Modes

- **Can’t connect to vCenter**: services still starting; refresh after ~60 seconds (common in labs).
    
- **Windows won’t boot from ISO**: CD not set to “connect at power on” or ISO not actually mounted.
    
- **Time drift persists**:
    
    - Wrong timezone inside guest
        
    - Guest tools not installed/running
        
    - Host time wrong (bad NTP upstream)
        
    - VM paused/suspended frequently (clock skew)
        

## Analysis & Reflection

- **Thin provisioning** saves datastore space but increases risk of overcommit; change management should track datastore capacity.
    
- **Time drift** breaks real systems: Kerberos/auth, log correlation, certificate validation, domain joins, “random” app failures.
    

## Exam Relevance Notes

- CompTIA loves “symptom → likely cause” mapping:
    
    - **Time drift** shows up as authentication/cert errors and event log confusion.
        
- Thin vs thick: know what “allocates immediately” vs “grows as used” means operationally.