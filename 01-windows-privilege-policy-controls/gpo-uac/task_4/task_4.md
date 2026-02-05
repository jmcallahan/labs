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
        
