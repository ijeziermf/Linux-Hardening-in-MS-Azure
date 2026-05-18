# AtlasPay Linux Hardening — Azure VM Security Baseline

> **NIST SP 800-53 Rev. 5 aligned Linux hardening for FinTech — SSH security, least privilege, audit logging, firewall configuration.**

---

## What This Demonstrates

| Capability | Details |
|---|---|
| **Platform** | Microsoft Azure, Ubuntu Server 22.04 LTS |
| **Framework Alignment** | NIST SP 800-53 Rev. 5 |
| **Focus Area** | Linux system hardening, access control, audit logging |
| **Deliverables** | Hardened VM, control mapping, governance documentation |
| **Stakeholder Focus** | Security teams, compliance officers, cloud operations |
| **Industry Relevance** | FinTech, financial services, regulated cloud environments |

---

## Overview

This project documents the complete hardening of an **Ubuntu Linux virtual machine** deployed in Microsoft Azure for **AtlasPay**, a simulated FinTech payment processor. The objective was to strengthen the security posture of a cloud-hosted Linux system by implementing **foundational controls** aligned with **NIST SP 800-53 Rev. 5**.

Rather than focusing solely on command execution, this work emphasizes **control intent**, **operational relevance**, and **security governance**. Each configuration step is tied to a specific security objective, demonstrating how technical hardening supports organizational risk reduction and compliance expectations.

---

## Hardening Summary

| Control Area | Implementation | NIST Mapping |
|---|---|---|
| **System Updates** | Full patch application | SI-2 (Flaw Remediation) |
| **User Management** | Role-based groups, least privilege | AC-2 (Account Management) |
| **Sudo Configuration** | Restricted administrative access | AC-6 (Least Privilege) |
| **SSH Hardening** | Root login disabled, controlled auth | AC-17 (Remote Access), IA-2 (Authentication) |
| **Password Policy** | Aging, warnings, complexity | IA-5 (Authenticator Management) |
| **Firewall** | Host-based boundary protection | SC-7 (Boundary Protection) |
| **Audit Logging** | Privileged file change monitoring | AU-2, AU-6 (Audit Events) |

---

## Implementation Details

### 1. Environment Setup

| Step | Action |
|---|---|
| **VM Deployment** | Ubuntu Server 22.04 LTS in Azure |
| **Resource Group** | AtlasPay lab environment |
| **Access Method** | SSH with secure credentials |
| **Validation** | Connectivity confirmed |

**Screenshot:** VM Overview

---

### 2. System Updates (SI-2)

```bash
sudo apt update && sudo apt upgrade -y
```

| Field | Details |
|---|---|
| **Control ID** | SI-2 |
| **Control Name** | Flaw Remediation |
| **Objective** | Reduce exposure to known vulnerabilities |
| **Implementation** | Full system patch application |
| **Evidence** | Update output screenshot |
| **Residual Risk** | Low — ongoing patching required |

**Screenshot:** System Update Output

---

### 3. User & Group Management (AC-2)

```bash
sudo groupadd finance
sudo useradd -m -s /bin/bash alice
sudo usermod -aG finance alice
sudo passwd alice
id alice
```

| Field | Details |
|---|---|
| **Control ID** | AC-2 |
| **Control Name** | Account Management |
| **Objective** | Role-based account creation |
| **Implementation** | Finance group + user alice |
| **Evidence** | `id alice` output |
| **Residual Risk** | Low — proper group membership |

**Screenshot:** User Creation Output

---

### 4. Least Privilege via Sudo (AC-6)

```bash
# /etc/sudoers entry
%finance ALL=(ALL) /usr/bin/apt
```

| Field | Details |
|---|---|
| **Control ID** | AC-6 |
| **Control Name** | Least Privilege |
| **Objective** | Minimum necessary privileges |
| **Implementation** | Finance group restricted to apt only |
| **Evidence** | Sudoers file screenshot |
| **Residual Risk** | Medium — package install still allowed |

**Screenshot:** Sudoers Configuration

---

### 5. SSH Hardening (AC-17, IA-2)

```bash
# /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication yes
```

| Field | Details |
|---|---|
| **Control ID** | AC-17, IA-2 |
| **Control Name** | Remote Access, Identification & Authentication |
| **Objective** | Secure remote access, individual accountability |
| **Implementation** | Root login disabled, password auth controlled |
| **Evidence** | sshd_config + systemctl status |
| **Residual Risk** | Medium — MFA/key-based recommended |

**Screenshots:** SSH Configuration, Service Status

---

### 6. Password Policy (IA-5)

```bash
# /etc/login.defs
PASS_MAX_DAYS   90
PASS_MIN_DAYS   1
PASS_WARN_AGE   14
```

| Field | Details |
|---|---|
| **Control ID** | IA-5 |
| **Control Name** | Authenticator Management |
| **Objective** | Password aging and rotation |
| **Implementation** | 90-day max, 1-day min, 14-day warning |
| **Evidence** | login.defs screenshot |
| **Residual Risk** | Medium — complexity/MFA not enforced |

**Screenshot:** Password Policy Configuration

---

### 7. Firewall Configuration (SC-7)

```bash
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status verbose
```

| Field | Details |
|---|---|
| **Control ID** | SC-7 |
| **Control Name** | Boundary Protection |
| **Objective** | Restrict inbound traffic |
| **Implementation** | SSH only, all else blocked |
| **Evidence** | UFW status output |
| **Residual Risk** | Low — host firewall active |

**Screenshot:** UFW Status

---

### 8. Audit Logging (AU-2, AU-6)

```bash
sudo apt install auditd audispd-plugins -y
sudo systemctl enable auditd
sudo systemctl start auditd
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
sudo passwd alice
sudo ausearch -k passwd_changes
```

| Field | Details |
|---|---|
| **Control ID** | AU-2, AU-6 |
| **Control Name** | Audit Events, Audit Review & Analysis |
| **Objective** | Traceability for privileged changes |
| **Implementation** | auditd monitoring /etc/passwd |
| **Evidence** | ausearch output |
| **Residual Risk** | Medium — manual review, automated monitoring recommended |

**Screenshot:** Audit Log Output

---

## Key Takeaways

1. **Hardening = Control Objectives, Not Just Commands** — Each step tied to NIST control
2. **SSH Hardening = Immediate Risk Reduction** — Root login disablement blocks automated attacks
3. **Least Privilege = Group Management + Sudo** — Both required for effective enforcement
4. **Audit Logging = Visibility into Privileged Ops** — Often overlooked but critical for forensics
5. **Host Firewalls = Critical Even in Cloud** — Defense-in-depth at multiple layers

---

## Value to GRC Consulting

This lab demonstrates **hands-on technical implementation** for:

| Service | Application |
|---|---|
| **Cloud Security Assessments** | VM hardening validation, baseline configuration review |
| **Compliance Mapping** | NIST 800-53 control implementation evidence |
| **Technical Control Testing** | SSH, sudo, firewall, audit validation |
| **Audit Readiness** | Control documentation, screenshot evidence, rationale |

---

## Tools & Frameworks

| Tool/Framework | Use |
|---|---|
| **Microsoft Azure** | VM deployment, network configuration |
| **Ubuntu Server 22.04 LTS** | Target operating system |
| **OpenSSH** | Secure remote access |
| **UFW** | Host-based firewall |
| **auditd** | Audit logging |
| **NIST SP 800-53 Rev. 5** | Control alignment framework |

---

## Growth & Next Iterations

Future enhancements:
- Ansible automation for repeatable hardening
- CIS Benchmark control expansion
- Additional audit rules for comprehensive coverage
- Azure Monitor / SIEM log integration

---

## Screenshots

| Description | Image |
|---|---|
| VM Overview | ![VM Overview](https://github.com/user-attachments/assets/...) |
| System Update Output | ![Updates](https://github.com/user-attachments/assets/...) |
| User Creation (id alice) | ![User](https://github.com/user-attachments/assets/...) |
| Sudoers Configuration | ![Sudoers](https://github.com/user-attachments/assets/...) |
| SSH Configuration | ![SSH Config](https://github.com/user-attachments/assets/...) |
| SSH Service Status | ![SSH Status](https://github.com/user-attachments/assets/...) |
| Password Policy (login.defs) | ![Password](https://github.com/user-attachments/assets/...) |
| UFW Status | ![Firewall](https://github.com/user-attachments/assets/...) |
| Audit Log Output | ![Audit](https://github.com/user-attachments/assets/...) |

*Note: Replace with actual screenshot URLs from the repository*

---

## License

This project is for educational and portfolio demonstration purposes. Methodology may be adapted for client engagements.
