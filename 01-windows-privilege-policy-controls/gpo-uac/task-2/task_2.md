Task 2 — Domain GPO: Change UAC Elevation Behavior for Standard Users

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
