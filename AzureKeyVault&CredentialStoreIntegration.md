# UiPath Orchestrator: Azure Key Vault (Read-Only) Plugin Configuration

This documentation outlines the steps to configure the **Azure Key Vault (Read-Only)** Plugin for UiPath Orchestrator. Using this plugin requires you to manually provision secrets directly within your Azure Key Vault.

---

## Prerequisites

Before starting, ensure you have:
- An active **Azure Subscription**.
- Permissions to create **App Registrations** in Microsoft Entra ID (formerly Azure AD).
- An existing **Azure Key Vault**.
- Admin access to **UiPath Orchestrator**.

---

## Step 1: Azure App Registration

1. Navigate to the [Azure Portal](https://portal.azure.com/) and go to **App registrations**.
2. Click **New registration** and follow the prompts to create a new app.
3. Once created, **Copy and save** the `Application (Client) ID` for later use.
4. Navigate to **Certificates & secrets** in the left-hand menu.
5. Click **+ New client secret**, add a description (e.g., `Orchestrator-KV-ReadOnly`), and choose an expiration period.
6. **IMPORTANT:** **Copy and save** the secret **Value** immediately. It will be permanently hidden once you navigate away from the page.

## Step 2: Configure Azure Key Vault Access Policies

1. In your **Azure Key Vault**, go to the **Overview** page.
2. **Copy and save** the `Vault URI` and the `Directory (tenant) ID`.
3. **Assign Permissions**:
   - The Azure Key Vault (Read-Only) plugin specifically requires the **Key Vault Secrets User** role assigned in Azure to the service principal.
   - Navigate to **Settings > Access Policies**.
   - Click **+ Create** (or **Add Access Policy**).
   - Under **Secret permissions**, ensure **Get** is selected.
   - Click **None selected** under **Principal** (or Authorized application), search for the **App Registration** name created in Step 1, select it, and click **Add**.
4. Click **Save** to apply the changes.

## Step 3: Provision Secrets in Azure Key Vault

When using the Read-Only plugin, secrets must be created manually in the Vault. The **Value** of the secret must be a **JSON string**.

### Asset Formats

| Asset Type | JSON Format |
| :--- | :--- |
| **Credential Assets** | `{"Username": "your_username", "Password": "your_password"}` |
| **Secret Assets** | `{"Username": "Asset_Name", "Password": "secret_value"}` |



## Step 4: Configure the Credential Store in Orchestrator

1. Log in to your **UiPath Orchestrator** instance.
2. Navigate to **Tenant > Credential Stores**.
3. Click **Add** to create a new credential store.
4. Select **Azure Key Vault (Read-Only)** as the provider.
5. Enter the details gathered in the previous steps:
   - **Vault URI**: The URI from Azure Key Vault.
   - **Directory ID**: The Tenant ID.
   - **Application ID**: The Client ID of your App Registration.
   - **Client Secret**: The Value of the secret created in Step 1.
6. Click **Create** or **Save** to finalize the configuration.

---
## Test Evidence
<img width="1206" height="690" alt="image" src="https://github.com/user-attachments/assets/69836034-f6a0-44cf-af0d-d62f44c03afb" />

