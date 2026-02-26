# AtlasPay Linux Hardening Lab
**Overview**

This project documents the complete hardening of an Ubuntu Linux virtual machine deployed in Microsoft Azure for the simulated FinTech payment processor AtlasPay. The objective was to strengthen the security posture of a cloud‑hosted Linux system by implementing foundational controls aligned with NIST SP 800‑53 Rev. 5.

Rather than focusing solely on command execution, this work emphasizes control intent, operational relevance, and security governance. Each configuration step is tied to a specific security objective, demonstrating how technical hardening supports organizational risk reduction and compliance expectations.

This lab represents the type of baseline system hardening you would see in regulated industries such as FinTech, financial services, and cloud‑forward organizations.

**Technologies & Tools Used**

- Microsoft Azure (VM deployment, access control, network configuration)
- Ubuntu Server 22.04 LTS
- OpenSSH (secure remote access)
- UFW (host‑based firewall)
- auditd (audit logging)
- NIST SP 800‑53 Rev. 5 (control alignment)
- Linux system administration utilities (nano, visudo, systemctl, useradd, etc.)

**Deliverable Features**

- Hardened Linux VM aligned with NIST 800‑53
- Secure SSH configuration (root login disabled, password auth controlled)
- Enforced password aging and authentication policies
- Least‑privilege sudo configuration for role‑based access
- Host‑based firewall restricting inbound traffic
- Audit logging for privileged file changes
- Full command‑level documentation with screenshots
- Governance‑aligned narrative explaining the purpose of each control

**Notes for Reviewers**

This lab is intentionally written in clear, business‑focused language while still demonstrating technical depth. Each configuration step includes:

- The security rationale
- The NIST control mapping
- The operational impact

This structure mirrors real‑world security hardening documentation used in regulated environments where clarity, traceability, and control justification are essential.

**Process (Start to Finish)**

The project began by deploying a Linux VM in Azure and establishing secure SSH access. Once connected, the system was updated and hardened through a series of configuration steps focused on identity, access, authentication, boundary protection, and governance.

Each step was executed manually to simulate real‑world system administration and control validation. Screenshots were also captured throughout the process to provide evidence of implementation and to support audit readiness.

**1. Environment Setup**

A new Ubuntu Server 22.04 LTS VM was deployed in Azure under the AtlasPay lab resource group. SSH access was configured using secure credentials, and the VM was validated as reachable.

[Insert Screenshot: VM Overview]

**2. System Updates & Patch Management (SI‑2)**

The system was fully updated to ensure all packages and libraries were current.

Commands: 

- sudo apt update && sudo apt upgrade -y

[Insert Screenshot: System Update Output]

This step reduces exposure to known vulnerabilities and establishes a secure baseline for the rest of the project.

**3. User & Group Management (AC‑2)**

A dedicated finance group and user (alice) were created to simulate organizational role separation.

Commands: 

- sudo groupadd finance
- sudo useradd -m -s /bin/bash alice
- sudo usermod -aG finance alice
- sudo passwd alice
- id alice

[Insert Screenshot: id alice Output]

This establishes controlled identity management and prepares for least‑privilege enforcement.

**4. Least Privilege via Sudo Configuration (AC‑6)**

The finance group was granted restricted sudo permissions, allowing only package management commands.

Sudoers Entry:

- %finance ALL=(ALL) /usr/bin/apt

[Insert Screenshot: sudoers File Showing Finance Rule]

This ensures users can perform only the administrative tasks required for their role.

**5. SSH Hardening (AC‑17, IA‑2)**

SSH configuration was updated to disable root login and enforce controlled password authentication.

Updated /etc/ssh/sshd_config:

- PermitRootLogin no
- PasswordAuthentication yes

SSH was restarted and validated.

[Insert Screenshot: sshd_config Edits]
[Insert Screenshot: systemctl status ssh]

This reduces attack surface and strengthens remote access security.
