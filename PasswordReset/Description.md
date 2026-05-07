# RPA Password Rotation & Vaulting

This project automates the lifecycle of RPA bot credentials by rotating passwords in Microsoft Entra ID (Azure AD) and securely updating them in Azure Key Vault.

## 🚀 Overview

The script performs the following workflow:
1.  **Identity Fetching:** Connects to Microsoft Graph to identify all users within a specific security group (e.g., `RPABots`).
2.  **Secure Generation:** Generates a cryptographically strong, random password for each member.
3.  **Entra ID Update:** Updates the user's password profile without requiring a change at next sign-in.
4.  **Vaulting:** Sanitizes the user's Email/UPN into a valid Azure Key Vault secret name and stores the new password as a secret.

## 🛠 Prerequisites

- **Azure PowerShell Modules:** `Az.Accounts` and `Az.KeyVault`.
- **Microsoft Graph Module:** `Microsoft.Graph.Users` and `Microsoft.Graph.Groups`.
- **Permissions:** - Entra ID: `User.ReadWrite.All` and `Group.Read.All`.
    - Key Vault: `Secrets Officer` or `Key Vault Secrets User` (for write access).

## 📋 Configuration

Modify the header of `Powershell.ps1` to match your environment:

| Variable | Description |
| :--- | :--- |
| `$GroupName` | The DisplayName of the group containing your RPA bots. |
| `$PassLength` | The length of the generated password (default: 16). |
| `$KeyVaultName` | The name of your existing Azure Key Vault. |

## 📋 Test Evidence

<img width="1208" height="224" alt="image" src="https://github.com/user-attachments/assets/1ae02325-c7ad-4c42-9fe0-b35b4ca24c68" />



<img width="1208" height="771" alt="image" src="https://github.com/user-attachments/assets/51bf473c-c9c2-4be9-a6de-8425b4d8769f" />

