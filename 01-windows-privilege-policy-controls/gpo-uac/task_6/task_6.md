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
