#### - What changed in the user experience?:
    When UAC was enforced, the standard user was required to provide admin credentials to perform system-level changes. This created a clear separation between standard user actions and privileged administrative actions
    
    After UAC was disabled and the user was added to admin groups, the user could approve changes with their own credentials. This allowed system-level changes without an elevation prompt. Actions that previously required explicit admin approval could now be performed directly by the standard user, reducing the safeguards that were previously in place.

#### - Why disabling UAC increases risk:
    Disabling UAC allows a standard user to very easily approve system changes. While there are times this can be convenient, it's obvious that this poses great risks to any system the user can access, as they may approve changes without reading to see what might be changed, or without knowing how those changes could negatively affect that system, but also the organization as a whole.
    Without UAC prompts:
    - Malicious software can make system-level changes silently.
    - Users may approve or initiate changes without understanding their impact
    - Configuration drift becomes easier, weakening overall system security.
    While disabling UAC may seem convenient, it undermines the principle of least privilege and reduces visibility into when and how privileged user actions occur.

#### - Why CompTIA might include this as a distractor:
    Disabling UAC often appears to "fix" permission-related issues quickly, making it an attractive but unsafe option. This scenario technically works, but represents poor security practice.
    Admins should recognize that:
    - Convenience does not equal correctness.
    - Security controls should be configured, not removed.
    - The correct solution balances usability with risk management.
    
    Choosing to disable UAC demonstrates a lack of security awareness, even if it resolves an immediate symptom. The principle of least privilege is an industry standard and best practice for a reason: it works.
