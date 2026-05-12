# Automated Azure AD Bot Password Rotation with Azure Automation, Key Vault, and UiPath

## Overview

This document describes the architecture and operational design for automated password rotation of RPA bot accounts using:

* Azure Automation Runbooks
* Microsoft Entra ID (Azure AD)
* Azure Key Vault
* UiPath Orchestrator Credential Store Integration

The solution eliminates manual password management for bot accounts, improves security posture, and ensures compliance with enterprise password rotation policies.

---

# High-Level Architecture

---
<img width="1212" height="542" alt="image" src="https://github.com/user-attachments/assets/05dc16ae-810c-4208-b87b-048ba816cb2c" />

# Problem Statement

The organization enforces a security policy requiring Microsoft user account passwords to be reset every 90 days.

Within the RPA environment:

* Multiple unattended bot accounts are used
* Password changes are currently manual
* Manual updates create operational overhead on control room
* Password synchronization issues can cause bot failures
* Credentials may be stored in multiple locations
* Human visibility of passwords increases security risk

The objective is to create a fully automated, centralized, and secure password lifecycle management solution.

---

# Proposed Solution

An Azure Automation Runbook will:

1. Retrieve all bot accounts from a dedicated Microsoft Entra ID security group (e.g., `RPA Users`)
2. Generate strong random passwords automatically
3. Reset passwords for each bot account
4. Store the updated credentials securely in Azure Key Vault
5. Allow UiPath Orchestrator to retrieve credentials dynamically from Azure Key Vault through Credential Store integration

The process runs automatically on a scheduled basis (for example every 30 days).

No manual intervention is required.

---

# Detailed Process Flow

## Step 1 — Scheduled Runbook Execution

An Azure Automation Runbook is configured with a recurring schedule.

Recommended frequency:

* Every 60 days

This ensures passwords are rotated well before the 90-day expiration policy.

---

## Step 2 — Retrieve RPA Bot Accounts

The runbook queries a dedicated Entra ID group:

`RPA BOTS`

This group contains all unattended bot accounts that require automated password management.

Benefits:

* Centralized account management
* Easy onboarding/offboarding of bot accounts
* No hardcoded usernames in scripts

---

## Step 3 — Generate Secure Passwords

For each account:

* A cryptographically secure password is generated
* Password complexity requirements are enforced
* Passwords are never exposed to users or administrators

Recommended password characteristics:

* Minimum 12 characters
* Uppercase letters
* Lowercase letters
* Numbers
* Special characters

---

## Step 4 — Reset Password in Entra ID

The runbook updates the password for each bot account using Microsoft Graph API or Azure PowerShell modules.

Recommended permissions:

* User Administrator
* Privileged Authentication Administrator

Prefer using:

* Managed Identity for the Automation Account

instead of storing administrative credentials.

---

## Step 5 — Store Password in Azure Key Vault

After successful password reset:

* The new password is written to Azure Key Vault
* Existing secret versions are automatically versioned
* Access is controlled through RBAC or Key Vault Access Policies

Secret naming convention example:
User Email Address

Only Azure services and approved systems should have access.

---

## Step 6 — UiPath Credential Retrieval

UiPath Orchestrator is configured with:

* Azure Key Vault Credential Store integration

When processes execute:

* UiPath retrieves credentials dynamically from Key Vault
* Updated passwords are automatically used
* No manual asset updates are required

This creates a single source of truth for credentials.

---

# Sequence Flow Diagram

```text
Azure Automation Runbook
        |
        | Fetch members of "RPA Users"
        v
Microsoft Entra ID
        |
        | Return bot accounts
        v
Azure Automation Runbook
        |
        | Generate secure password
        |
        | Reset account password
        v
Microsoft Entra ID
        |
        | Password updated
        v
Azure Automation Runbook
        |
        | Store new password
        v
Azure Key Vault
        |
        | UiPath retrieves latest secret
        v
UiPath Orchestrator
        |
        | Robot execution
        v
UiPath Robots
```

---

# Security Benefits

## Centralized Credential Storage

Credentials are stored only in Azure Key Vault.

---

## Reduced Human Exposure

Passwords are:

* Auto-generated
* Auto-rotated
* Auto-stored

Administrators and developers do not need to know the actual passwords.

---

## Compliance Alignment

The design supports:

* Enterprise password rotation policies
* Auditability
* Least privilege access
* Secret versioning
* Centralized governance

---

## Improved Operational Stability

Removes common production issues such as:

* Expired bot passwords
* Forgotten password updates
* Manual synchronization failures

---


---

# Recommended Permissions Model

## Automation Account Managed Identity

Required permissions:

| Service         | Permission                               |
| --------------- | ---------------------------------------- |
| Microsoft Graph | User.ReadWrite.All                       |
| Microsoft Graph | Group.Read.All                           |
| Azure Key Vault | Secrets Officer / Secret Set permissions |

Apply least privilege wherever possible.

---


Recommended logging:

| Event                    | Example                      |
| ------------------------ | ---------------------------- |
| Password reset success   | bot.finance reset successful |
| Key Vault update success | Secret updated successfully  |
| Failure events           | User locked / API failure    |

---

# Monitoring and Alerting

Recommended monitoring:

* Runbook execution failures
* Key Vault write failures
* Password reset failures

# Conclusion

This architecture provides a secure, scalable, and automated solution for managing RPA bot account passwords.

By combining:

* Azure Automation
* Microsoft Entra ID
* Azure Key Vault
* UiPath Credential Store Integration

The organization achieves:

* Centralized credential governance
* Automated password lifecycle management
* Reduced operational overhead
* Improved security posture
* Better compliance alignment

