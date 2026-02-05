### Task 1 — Baseline: Prove UAC is Doing Something
#### 1.1 Sign in as the standard user
- Sign into the Windows client as `\WendyV` (standard user).

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
