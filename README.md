# labs
Labwork in preparation for CompTIA A+, Net+, Sec+ and CySA+ exams

Step-by-step lab instructions can be found below. While I did my best to adhere to the steps, my VM version is Windows 11 Pro, not Enterprise, so AppLocker wasn't available and Task 4 is therefore incomplete.



## Associated Exam
- CompTIA A+ Core 2

## Objective Mapping
> **Note:** A+ Core 2 objective wording varies slightly by exam version, but these topics consistently map to:
- **2.x** Windows OS configuration & troubleshooting (permissions, tools, security settings)
- **3.x** Software troubleshooting (symptoms/causes, access denied, policy enforcement)
- **4.x** Operational procedures (change control, backups, documentation)
- **Security/Hardening** concepts: least privilege, application control, script governance

**Suggested mapping labels for your repo (adjust to match your course’s objective sheet):**
- 2.3 Troubleshoot common Windows OS problems (access denied, permissions, policy not applying)
- 2.5 Manage Windows settings (UAC, Local Security Policy, administrative tools)
- 3.1 Troubleshoot common security issues (unauthorized access, blocked apps, policy enforcement)
- 4.2 Change management / documentation / backups and recovery basics

---

## Scenario
You’re a junior Windows administrator in a small domain environment. Users report that:
- They’re sometimes prompted for admin credentials (UAC), sometimes not.
- A “blocked by your system administrator” message appears for certain tools.
- A workstation needs a basic backup configuration for user data.

Your job is to **analyze privilege boundaries**, **enforce policies**, and **prove** that your changes are applied (with evidence). You’ll also demonstrate why “quick fixes” like disabling UAC are risky.

---

## Learning Objectives
By the end of this lab, you will be able to:
- Explain and demonstrate **what triggers UAC** and how elevation works for standard users.
- Configure UAC behavior via **Domain GPO** and validate application with `gpupdate`, `gpresult`, and RSOP.
- Configure **AppLocker** rules (Path vs Hash), verify enforcement, and validate via Event Logs.
- Correctly interpret **PowerShell Execution Policy** (what it does, what it doesn’t) and set it safely by scope.
- Produce **GitHub-ready evidence artifacts** (command outputs + screenshots optional) that prove configuration and troubleshooting competence.

---

## Required Environment
### OS / Systems
- Windows 10/11 client joined to a domain (or simulated if you don’t have a domain)
- Windows Server (2019/2022) Domain Controller (for the GPO portion)

### Accounts
- **Domain Admin** (e.g., `HEXELO\Administrator`)
- **Standard domain user** (e.g., `HEXELO\WendyV`)

### Tools
- Built-in Windows tools: `gpupdate`, `gpresult`, Event Viewer, Local Security Policy (`secpol.msc`), Group Policy Management (`gpmc.msc`)
- PowerShell (Windows PowerShell 5.1 is fine)

### Permissions
- Local admin access on the client (for AppLocker setup and some verification steps)
- Domain admin access on the DC (for Default Domain Policy edits)

---

## Lab Tasks

### Task 0 — Repo Setup & Evidence Folder (Proof-of-Work)
Create a folder structure in your repo like this:
	/labs/windows-privilege-policy-controls/  
	README.md  
	/evidence/  
	baseline/  
	gpo-uac/  
	applocker/  
	executionpolicy/

On the Windows client, create a working evidence directory:
- `C:\LabEvidence\PrivilegePolicyControls\`

You will export outputs into this directory and then copy them into your repo’s `/evidence/` folders.

---

### Task 1 — Baseline: Prove UAC is Doing Something
#### 1.1 Sign in as the standard user
- Sign into the Windows client as `HEXELO\WendyV` (standard user).

#### 1.2 Trigger an elevation event (expected: UAC prompt)
Attempt to open an elevated action:
- Try to disable Windows Defender Firewall OR open `regedit` OR run an elevated CMD.

**Expected behavior (baseline):**
- A UAC prompt appears that requires admin credentials (because Wendy is not a local admin/domain admin).

#### 1.3 Capture baseline evidence
Run these in **non-elevated CMD** and save outputs:

```
whoami > C:\LabEvidence\PrivilegePolicyControls\baseline\whoami.txt
whoami /groups > C:\LabEvidence\PrivilegePolicyControls\baseline\whoami_groups.txt
gpresult /r > C:\LabEvidence\PrivilegePolicyControls\baseline\gpresult_r.txt
```

**Optional screenshot evidence:**

- Screenshot the UAC prompt (blur passwords if shown).
### Task 2 — Domain GPO: Change UAC Elevation Behavior for Standard Users

> Goal: Demonstrate how a domain can control whether standard users are **prompted** or **denied automatically**.

#### 2.1 On the Domain Controller: edit Default Domain Policy

Sign into the DC as `HEXELO\Administrator`.

Open:
- `gpmc.msc` → Forest → Domains → your domain → **Default Domain Policy** → Edit
    

Navigate to:

- Computer Configuration  
    → Policies  
    → Windows Settings  
    → Security Settings  
    → Local Policies  
    → Security Options
    

Set:

- **User Account Control: Behavior of the elevation prompt for standard users** = **Automatically deny elevation requests**
    

#### 2.2 Force policy update + validate on the client

On the Windows client (you can do this as Wendy, but you may need admin for some commands):

- Reboot OR run:
```
gpupdate /force
```
Now capture evidence:
```
gpresult /r > C:\LabEvidence\PrivilegePolicyControls\gpo-uac\gpresult_after_deny.txt
gpresult /h C:\LabEvidence\PrivilegePolicyControls\gpo-uac\gpresult_after_deny.html
```
#### 2.3 Retest the same privileged action (expected: no credential prompt)

Attempt the same admin action again (disable firewall / open regedit elevated).

**Expected behavior:**

- Standard user is **denied** without being offered a credential prompt (the elevation request is automatically denied).
    

#### 2.4 Troubleshooting fork (if behavior didn’t change)

If you still get the old UAC experience:

- Confirm the machine is domain-joined.
    
- Confirm policy scope and precedence.
    
- Confirm GPO applied using:
    
    - `gpresult /r`
        
    - `rsop.msc` (Resultant Set of Policy)
Capture:
```
gpresult /r > C:\LabEvidence\PrivilegePolicyControls\gpo-uac\gpresult_troubleshoot.txt
```
### Task 3 — Risk Demonstration: “Disable UAC” via GPO (and why this is bad)

> This is here because CompTIA likes to test “what happens if…” and because MyCC included it.  
> In real environments, disabling UAC is generally a **bad** security posture.

#### 3.1 On the DC: modify UAC-related policies (demonstration only)

In **Default Domain Policy** (same location as before), configure:

- **User Account Control: Run all administrators in Admin Approval Mode** = **Disabled**
    
- **User Account Control: Detect application installations and prompt for elevation** = **Disabled**
    
- **User Account Control: Only elevate UIAccess applications that are installed in secure locations** = **Disabled**
    
- **User Account Control: Behavior of the elevation prompt for administrators in Admin Approval Mode** = **Elevate without prompting** _(this removes a key checkpoint)_
    

> Your goal is not “best practice.” Your goal is to observe behavior changes and explain them.

#### 3.2 Apply policy and retest as standard user

On the client:

- Reboot (preferred) then sign in as Wendy.
    

Attempt to change Windows Defender Firewall settings again.

**Expected behavior:**

- The experience is less protected. Depending on the configuration and system context, you may see fewer prompts and more direct access to configuration surfaces.
    

Capture evidence:
```
gpresult /r > C:\LabEvidence\PrivilegePolicyControls\gpo-uac\gpresult_after_disable_uac.txt
```
#### 3.3 Short analysis note (required)

Create a markdown note in your repo:

- `/evidence/gpo-uac/analysis_disable_uac.md`
    

Include:

- What changed in the user experience
    
- Why disabling UAC increases risk (removes friction that prevents silent elevation/changes)
    
- Why CompTIA might include this as a distractor (it _works_ but it’s not a smart default)
    

---

### Task 4 — Local Enforcement: AppLocker Deny Rules (Path vs Hash)

> Goal: demonstrate “even admins can be blocked” and how app control creates a hard stop.

#### 4.1 Verify prerequisites

Sign into the client as an admin (local admin or domain admin).

Start an elevated Command Prompt and run:
```
net start appidsvc
```
Capture:
```
sc query appidsvc > C:\LabEvidence\PrivilegePolicyControls\applocker\appidsvc_status.txt
```
#### 4.2 Enable AppLocker rule enforcement

Open:

- `secpol.msc` → Security Settings → Application Control Policies → AppLocker
    

Select:

- **Configure Rule Enforcement**  
    Enable and set to **Enforce rules** for:
    
- Executable rules
    
- Windows Installer rules
    
- Script rules
    
- Packaged app Rules
    

Capture evidence (best effort):

- Screenshot AppLocker enforcement settings OR note them in:
    
    - `C:\LabEvidence\PrivilegePolicyControls\applocker\applocker_enforcement_notes.txt`
        

#### 4.3 Create deny rule for regedit by Path

In AppLocker → Executable Rules:

- Create New Rule
    
- Action: **Deny**
    
- User or group: (use Everyone for a strong demo, or a test group)
    
- Condition: **Path**
    
- Path: `%WINDIR%\regedit.exe`
    
- Create default rules when prompted
    

#### 4.4 Create deny rule for Microsoft Edge by Hash

Create New Rule:

- Action: **Deny**
    
- Condition: **File hash**
    
- Select Edge binary (commonly):
    
    - `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
        

#### 4.5 Force policy update and test blocks

Run:
```
gpupdate /force
```
Test:

- Open `regedit` → expected block message
    
- Open Edge → expected immediate close / block behavior
    

Capture:
```
gpresult /r > C:\LabEvidence\PrivilegePolicyControls\applocker\gpresult_after_applocker.txt
```
#### 4.6 Verify AppLocker events (proof it actually enforced)

Open Event Viewer:

- Applications and Services Logs  
    → Microsoft  
    → Windows  
    → AppLocker
    

Look for events indicating the app was blocked.

Capture evidence:

- Screenshot of AppLocker event OR export the relevant events.
    

Minimum acceptable proof:

- Write down event IDs + timestamps into:
    
    - `C:\LabEvidence\PrivilegePolicyControls\applocker\applocker_event_notes.txt`
        

---

### Task 5 — PowerShell Execution Policy: Safe Governance (Correct Framing)

> Execution Policy is a _control and safety rail_, not a true security boundary.  
> CompTIA tests what the modes mean and how they affect script execution.

#### 5.1 Record current execution policies by scope

Open PowerShell (non-elevated) and run:
```
Get-ExecutionPolicy -List | Out-File C:\LabEvidence\PrivilegePolicyControls\executionpolicy\executionpolicy_list.txt
```
#### 5.2 Set to Restricted (demo)

In an **elevated** PowerShell:
```
Set-ExecutionPolicy Restricted -Scope LocalMachine -Force
Get-ExecutionPolicy -List
```
Export:
```
Get-ExecutionPolicy -List | Out-File C:\LabEvidence\PrivilegePolicyControls\executionpolicy\executionpolicy_restricted.txt
```
5.3 Set to AllSigned (demo)
```
Set-ExecutionPolicy AllSigned -Scope LocalMachine -Force
Get-ExecutionPolicy -List | Out-File C:\LabEvidence\PrivilegePolicyControls\executionpolicy\executionpolicy_allsigned.txt
```
#### 5.4 Explain the modes (required)

Create:

- `/evidence/executionpolicy/executionpolicy_notes.md`
    

Include:

- Restricted: scripts blocked
    
- AllSigned: only signed scripts run without prompt
    
- RemoteSigned: local scripts run; downloaded scripts require signing (common compromise)
    
- Unrestricted/Bypass: minimal friction (not recommended for normal endpoints)
    
- **Important:** attackers can bypass; don’t treat as “malware-proof”
    

---

### Task 6 — Rollback: Return to Secure Defaults

> CompTIA loves “restore secure baseline.”

#### 6.1 Re-enable UAC defaults (Domain GPO)

On the DC:

- Undo your UAC changes by clearing (“Not Configured”) the settings you changed.
    

On the client:
```
gpupdate /force
```
Capture:
```
gpresult /r > C:\LabEvidence\PrivilegePolicyControls\baseline\gpresult_restored.txt
```
#### 6.2 Remove/disable AppLocker rules

In `secpol.msc`:

- Disable enforcement OR delete your deny rules.
    

Force update:
```
gpupdate /force
```
Verify regedit and Edge work again.

#### 6.3 Restore Execution Policy

Choose a reasonable default for lab systems:

- `RemoteSigned` (common admin workstation compromise) OR
    
- whatever your environment baseline was (use Task 5.1 output)
    

Example:
```
Set-ExecutionPolicy RemoteSigned -Scope LocalMachine -Force
Get-ExecutionPolicy -List | Out-File C:\LabEvidence\PrivilegePolicyControls\executionpolicy\executionpolicy_restored.txt
```
## Observations & Failure Modes

### Common failure modes

- **GPO “didn’t apply”**
    
    - Machine not domain-joined
        
    - Wrong OU / inheritance blocked
        
    - Security filtering / WMI filtering
        
    - Policy not refreshed (use `gpupdate /force` and reboot)
        
- **AppLocker doesn’t block**
    
    - Application Identity service not running (`appidsvc`)
        
    - Enforcement not set to “Enforce rules”
        
    - Default rules missing (can cause weird allow/deny outcomes)
        
    - Testing under a different user context than intended
        
- **Execution Policy confusion**
    
    - Setting scope matters (CurrentUser vs LocalMachine)
        
    - Group Policy can override local changes
        
    - Policy affects script execution behavior, not interactive commands
        

---

## Analysis & Reflection

### What’s actually happening

- **UAC** is a privilege boundary mechanism that adds friction and reduces silent privileged changes.
    
- **GPO** is centralized policy control that enforces consistent security posture across domain-joined machines.
    
- **AppLocker** is application control—useful for stopping known-risk tools, reducing attack surface, and enforcing least privilege.
    
- **ExecutionPolicy** is governance and safety—useful for reducing casual risk and aligning admin workflows, but not a hardened security boundary.
    

### What CompTIA is really testing

- Can you tell **why** an action is blocked (permissions vs policy vs UAC)?
    
- Can you identify the right tool (Local Security Policy vs GPO vs Event Viewer)?
    
- Can you verify changes with evidence (`gpresult`, logs, observed behavior)?
    
- Can you restore a secure operational baseline after testing?
    

---

## Exam Relevance Notes

### Key terms

- UAC, elevation, Admin Approval Mode
    
- Standard user vs administrator
    
- GPO, gpupdate, gpresult, RSOP
    
- AppLocker, Application Identity service, Path rule vs Hash rule
    
- Execution Policy: Restricted, AllSigned, RemoteSigned, Unrestricted, Bypass
    

### Traps and distractors

- “Disable UAC” solves friction but **creates risk**.
    
- Execution Policy ≠ real security boundary.
    
- “It didn’t work” → CompTIA expects you to **verify policy application**, not guess.
    
- Path rules can be bypassed by moving/renaming binaries (Hash rules are more robust but require maintenance).
    

---

## Deliverables (What to commit to GitHub)

Minimum:

- `/labs/windows-privilege-policy-controls/README.md` (this file)
    
- `/evidence/baseline/`:
    
    - `whoami.txt`
        
    - `whoami_groups.txt`
        
    - `gpresult_r.txt`
        
- `/evidence/gpo-uac/`:
    
    - `gpresult_after_deny.txt`
        
    - `gpresult_after_disable_uac.txt`
        
    - `analysis_disable_uac.md`
        
- `/evidence/applocker/`:
    
    - `appidsvc_status.txt`
        
    - `gpresult_after_applocker.txt`
        
    - `applocker_event_notes.txt` (or exported event log)
        
- `/evidence/executionpolicy/`:
    
    - `executionpolicy_list.txt`
        
    - `executionpolicy_restricted.txt`
        
    - `executionpolicy_allsigned.txt`
        
    - `executionpolicy_notes.md`
        
    - `executionpolicy_restored.txt`
        

Optional:

- Screenshots (sanitized, no passwords), stored under `/evidence/screenshots/`
    

---

## Post-Exam Expansion (Optional)

- Add a section comparing:
    
    - NTFS vs Share permissions (Effective Access limitations)
        
    - AppLocker vs WDAC (Windows Defender Application Control)
        
    - Local Policy vs Domain GPO precedence
        
- Add a blue-team angle:
    
    - Identify event IDs and detection logic for blocked apps
        
    - Build a simple “policy verification checklist” script in PowerShell
