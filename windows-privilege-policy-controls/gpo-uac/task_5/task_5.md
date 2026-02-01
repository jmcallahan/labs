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
