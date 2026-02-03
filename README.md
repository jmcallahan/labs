# Lab Work Overview
### This repo contains lab work in preparation for CompTIA A+, Net+, Sec+ and CySA+ exams.### CompTIA Domain Coverage (High-Level)

These labs collectively touch the following CompTIA domains through applied tasks:
- Operating Systems & Software Troubleshooting
- Networking & Network Services (auth, time sync, policy effects)
- Security Controls & Hardening (UAC, GPO, application control)
- Virtualization & Cloud Fundamentals
- Operational Procedures & Documentation

## windows-privilege-policy-controls
- Analyzed how Windows enforces privilege boundaries using UAC, Group Policy, application control, and PowerShell execution policy. Demonstrated how policy changes affect standard user behavior, why insecure shortcuts (e.g., disabling UAC) increase risk, and how to verify enforcement with gpresult and event logs. Emphasis on diagnosis, evidence capture, and restoring a secure baseline rather than blind configuration.

## provision-vm-fix-time-drift
- Built a standardized Windows desktop VM using VMware, installed the OS and guest tools, and corrected VM time drift via host synchronization. Focused on why time drift causes real authentication and logging failures, how guest tools mitigate it, and how to document a clean, repeatable build with evidence.
