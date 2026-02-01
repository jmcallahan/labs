Task 3 — Risk Demonstration: “Disable UAC” via GPO (and why this is bad)

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
