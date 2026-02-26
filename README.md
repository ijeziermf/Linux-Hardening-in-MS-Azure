# **AtlasPay Linux Hardening Lab**


## **Video Demonstration**

(Soon: Video walkthrough of the full hardening process)

## **Overview**

This project documents the complete hardening of an Ubuntu Linux virtual machine deployed in Microsoft Azure for the simulated FinTech payment processor AtlasPay. The objective was to strengthen the security posture of a cloud‑hosted Linux system by implementing foundational controls aligned with NIST SP 800‑53 Rev. 5.

Rather than focusing solely on command execution, this work emphasizes control intent, operational relevance, and security governance. Each configuration step is tied to a specific security objective, demonstrating how technical hardening supports organizational risk reduction and compliance expectations.

This lab represents the type of baseline system hardening you would see in regulated industries such as FinTech, financial services, and cloud‑forward organizations.

## **Technologies & Tools Used**

- Microsoft Azure (VM deployment, access control, network configuration)
- Ubuntu Server 22.04 LTS
- OpenSSH (secure remote access)
- UFW (host‑based firewall)
- auditd (audit logging)
- NIST SP 800‑53 Rev. 5 (control alignment)
- Linux system administration utilities (nano, visudo, systemctl, useradd, etc.)

## **Deliverable Features**

- Hardened Linux VM aligned with NIST 800‑53
- Secure SSH configuration (root login disabled, password auth controlled)
- Enforced password aging and authentication policies
- Least‑privilege sudo configuration for role‑based access
- Host‑based firewall restricting inbound traffic
- Audit logging for privileged file changes
- Full command‑level documentation with screenshots
- Governance‑aligned narrative explaining the purpose of each control

## **Notes for Reviewers**

This lab is intentionally written in clear, business‑focused language while still demonstrating technical depth. Each configuration step includes:

- The security rationale
- The NIST control mapping
- The operational impact

This structure mirrors real‑world security hardening documentation used in regulated environments where clarity, traceability, and control justification are essential.

## **Process (Start to Finish)**

The project began by deploying a Linux VM in Azure and establishing secure SSH access. Once connected, the system was updated and hardened through a series of configuration steps focused on identity, access, authentication, boundary protection, and governance.

Each step was executed manually to simulate real‑world system administration and control validation. Screenshots were also captured throughout the process to provide evidence of implementation and to support audit readiness.

### **1. Environment Setup**

A new Ubuntu Server 22.04 LTS VM was deployed in Azure under the AtlasPay lab resource group. SSH access was configured using secure credentials, and the VM was validated as reachable.

[Insert Screenshot: VM Overview]

### **2. System Updates & Patch Management (SI‑2)**

The system was fully updated to ensure all packages and libraries were current.

Commands: 

- sudo apt update && sudo apt upgrade -y

[Insert Screenshot: System Update Output]

This step reduces exposure to known vulnerabilities and establishes a secure baseline for the rest of the project.

### NIST Control Mapping — System Updates (SI‑2)

| Field | Details |
|-------|---------|
| **Control ID** | SI‑2 |
| **Control Name** | Flaw Remediation |
| **Control Objective** | Ensure the system is updated with the latest security patches to reduce exposure to known vulnerabilities. |
| **Implementation Details** | Executed `sudo apt update && sudo apt upgrade -y` to apply all available security and package updates. Restarted services as prompted. |
| **Risk Addressed** | Outdated packages, unpatched vulnerabilities, exposure to known exploits. |
| **Evidence** | [Insert Screenshot: System Update Output] |
| **Residual Risk** | Low — updates applied, but ongoing patching is required as part of continuous operations. |
| **Notes for Auditors** | System was fully patched at the time of configuration. Future updates depend on operational maintenance cadence. |


### **3. User & Group Management (AC‑2)**

A dedicated finance group and user (alice) were created to simulate organizational role separation.

Commands: 

- sudo groupadd finance
- sudo useradd -m -s /bin/bash alice
- sudo usermod -aG finance alice
- sudo passwd alice
- id alice

[Insert Screenshot: id alice Output]

This establishes controlled identity management and prepares for least‑privilege enforcement.

### NIST Control Mapping — Account Management (AC‑2)

| Field | Details |
|-------|---------|
| **Control ID** | AC‑2 |
| **Control Name** | Account Management |
| **Control Objective** | Establish and manage user accounts based on organizational roles and responsibilities. |
| **Implementation Details** | Created `finance` group and user `alice`. Added user to group using `usermod -aG finance alice`. Verified with `id alice`. |
| **Risk Addressed** | Uncontrolled account creation, lack of role separation, unauthorized access. |
| **Evidence** | [Insert Screenshot: id alice Output] |
| **Residual Risk** | Low — account exists with proper group membership; ongoing monitoring required. |
| **Notes for Auditors** | User creation aligns with least‑privilege principles and supports role‑based access control. |


### **4. Least Privilege via Sudo Configuration (AC‑6)**

The finance group was granted restricted sudo permissions, allowing only package management commands.

Sudoers Entry:

- %finance ALL=(ALL) /usr/bin/apt

[Insert Screenshot: sudoers File Showing Finance Rule]

This ensures users can perform only the administrative tasks required for their role.

### NIST Control Mapping — Least Privilege (AC‑6)

| Field | Details |
|-------|---------|
| **Control ID** | AC‑6 |
| **Control Name** | Least Privilege |
| **Control Objective** | Ensure users operate with the minimum privileges necessary to perform their duties. |
| **Implementation Details** | Added `%finance ALL=(ALL) /usr/bin/apt` to `/etc/sudoers` to restrict finance group to package management only. |
| **Risk Addressed** | Excessive privileges, unauthorized administrative actions, privilege escalation. |
| **Evidence** | [Insert Screenshot: sudoers File Showing Finance Rule] |
| **Residual Risk** | Medium — user can still install packages; additional restrictions may be applied depending on policy. |
| **Notes for Auditors** | Sudoers entry validated using `visudo` to ensure syntax integrity. |


### **5. SSH Hardening (AC‑17, IA‑2)**

SSH configuration was updated to disable root login and enforce controlled password authentication.

Updated /etc/ssh/sshd_config:

- PermitRootLogin no
- PasswordAuthentication yes

SSH was restarted and validated.

[Insert Screenshot: sshd_config Edits]

[Insert Screenshot: systemctl status ssh]

This reduces attack surface and strengthens remote access security.

### NIST Control Mapping — Remote Access (AC‑17)

| Field | Details |
|-------|---------|
| **Control ID** | AC‑17 |
| **Control Name** | Remote Access |
| **Control Objective** | Secure remote access mechanisms to prevent unauthorized external access. |
| **Implementation Details** | Updated SSH configuration to disable root login (`PermitRootLogin no`) and enforce controlled password authentication. |
| **Risk Addressed** | Unauthorized remote access, brute‑force attacks, credential compromise. |
| **Evidence** | [Insert Screenshot: sshd_config Edits] |
| **Residual Risk** | Medium — password authentication remains enabled; MFA or key‑based auth recommended for production. |
| **Notes for Auditors** | SSH service validated using `systemctl status ssh`. |

### NIST Control Mapping — Identification & Authentication (IA‑2)

| Field | Details |
|-------|---------|
| **Control ID** | IA‑2 |
| **Control Name** | Identification and Authentication |
| **Control Objective** | Ensure users are uniquely identified and authenticated before system access. |
| **Implementation Details** | Enforced password authentication and disabled root login to ensure all access is tied to individual accounts. |
| **Risk Addressed** | Anonymous access, root‑level compromise, lack of traceability. |
| **Evidence** | [Insert Screenshot: systemctl status ssh] |
| **Residual Risk** | Medium — password‑based authentication is inherently weaker than key‑based or MFA. |
| **Notes for Auditors** | Authentication logs available via `/var/log/auth.log`. |


### **6. Password Policy Enforcement (IA‑5)**

Password aging and warning policies were configured against the defaults to enforce stronger authentication hygiene.

Updated /etc/login.defs:

- PASS_MAX_DAYS   90
- PASS_MIN_DAYS   1
- PASS_WARN_AGE   14

[Insert Screenshot: login.defs Edits]

This aligns with organizational authentication requirements and reduces credential‑related risk.

### NIST Control Mapping — Authenticator Management (IA‑5)

| Field | Details |
|-------|---------|
| **Control ID** | IA‑5 |
| **Control Name** | Authenticator Management |
| **Control Objective** | Enforce secure password creation, aging, and management policies. |
| **Implementation Details** | Updated `/etc/login.defs` to enforce password aging: `PASS_MAX_DAYS 90`, `PASS_MIN_DAYS 1`, `PASS_WARN_AGE 14`. |
| **Risk Addressed** | Weak or stale passwords, credential compromise, non‑rotating credentials. |
| **Evidence** | [Insert Screenshot: login.defs Edits] |
| **Residual Risk** | Medium — password complexity and MFA not enforced at OS level. |
| **Notes for Auditors** | Password aging applies to all local accounts unless overridden by PAM. |


### **7. Firewall Configuration (SC‑7)**

UFW was enabled to restrict inbound traffic to SSH only.

Commands:

- sudo ufw allow OpenSSH
- sudo ufw enable
- sudo ufw status verbose

[Insert Screenshot: UFW Status]

This established host‑level boundary protection while reducing exposure to unauthorized access attempts.

### NIST Control Mapping — Boundary Protection (SC‑7)

| Field | Details |
|-------|---------|
| **Control ID** | SC‑7 |
| **Control Name** | Boundary Protection |
| **Control Objective** | Restrict and monitor communications at system boundaries. |
| **Implementation Details** | Enabled UFW, allowed only OpenSSH, blocked all other inbound traffic. |
| **Risk Addressed** | Unauthorized network access, lateral movement, exposure to scanning. |
| **Evidence** | [Insert Screenshot: UFW Status] |
| **Residual Risk** | Low — host firewall active; network‑level controls still recommended. |
| **Notes for Auditors** | UFW logs available under `/var/log/ufw.log`. |


### **8. Audit Logging (AU‑2, AU‑6)**

Auditd was configured to monitor changes to /etc/passwd, which is a critical system file in the environment.

Commands:

- sudo apt install auditd audispd-plugins -y
- sudo systemctl enable auditd
- sudo systemctl start auditd
- sudo auditctl -w /etc/passwd -p wa -k passwd_changes
- sudo passwd alice
- sudo ausearch -k passwd_changes

[Insert Screenshot: ausearch Output]

This provides traceability and supports incident investigation and compliance reporting.

### NIST Control Mapping — Audit Events (AU‑2)

| Field | Details |
|-------|---------|
| **Control ID** | AU‑2 |
| **Control Name** | Audit Events |
| **Control Objective** | Ensure the system generates audit records for relevant security events. |
| **Implementation Details** | Installed auditd and configured monitoring for `/etc/passwd` using `auditctl -w`. |
| **Risk Addressed** | Lack of visibility into privileged changes, undetected account manipulation. |
| **Evidence** | [Insert Screenshot: ausearch Output] |
| **Residual Risk** | Medium — additional audit rules may be required for full coverage. |
| **Notes for Auditors** | Audit logs stored in `/var/log/audit/audit.log`. |

### NIST Control Mapping — Audit Review, Analysis, and Reporting (AU‑6)

| Field | Details |
|-------|---------|
| **Control ID** | AU‑6 |
| **Control Name** | Audit Review, Analysis, and Reporting |
| **Control Objective** | Ensure audit logs are reviewed and analyzed to detect anomalies. |
| **Implementation Details** | Validated audit rule functionality by triggering a password change event and reviewing logs with `ausearch`. |
| **Risk Addressed** | Undetected malicious activity, lack of forensic traceability. |
| **Evidence** | [Insert Screenshot: ausearch Output] |
| **Residual Risk** | Medium — manual review performed; automated log monitoring recommended. |
| **Notes for Auditors** | Auditd configuration supports centralized log forwarding if required. |


## **Key Takeaways & Discoveries**

- Hardening is most effective when tied to control objectives, not just loose commands.
- SSH hardening immediately reduced exposure to automated root login attempts.
- Least‑privilege enforcement requires both group management and sudo configuration.
- Audit logging provides visibility into privileged operations that often go unmonitored.
- Host‑based firewalls remain a critical layer even in cloud environments.

## **Value to Security & GRC**

This project demonstrates how technical hardening directly supports:

- Regulatory expectations
- Security governance
- Operational resilience
- Audit readiness
- Cloud security best practices

It bridges the gap between hands‑on system administration and the control‑driven mindset required in modern governance and security roles.

## **Growth & Next Improvements**

- Future enhancements may include:
- Automating hardening steps using Ansible or cloud‑init
- Implementing CIS Benchmark controls
- Expanding audit rules to cover additional privileged files
- Integrating logs with Azure Monitor or a SIEM
