# Azure Function Apps – Metadata-Driven Deployment Pipeline

A production-grade Azure DevOps YAML pipeline for dynamically building and deploying multiple Azure Function Apps using runtime UI selection, metadata configuration, and environment-specific app settings management.

---

## Overview

This repository provides a scalable and enterprise-ready Azure DevOps pipeline that:

- Builds and deploys multiple Azure Function Apps
- Allows runtime selection from the pipeline UI
- Uses JSON metadata instead of hardcoded values
- Supports multi-slot deployments (DevTest → Staging → Production)
- Enables zero-downtime production releases using slot swap
- Manages environment-specific app settings per Function App per slot

This template is designed for teams managing multiple Function Apps who require centralized, safe, and automated deployments.

---

## Architecture Flow
```text
Pipeline Runtime Parameters (UI)
↓
functionapps.json (Metadata)
↓
Dynamic Build + Publish
↓
ZIP Packaging
↓
Deploy to DevTest Slot → Apply DevTest App Settings
↓
Deploy to Staging Slot → Apply Staging App Settings
↓
Slot Swap → Production → Apply Production App Settings
```

---

## Key Features

- Runtime checkbox selection of Function Apps
- "Deploy All" override option
- Metadata-driven configuration (no hardcoding in pipeline logic)
- Multi-slot deployment strategy
- Zero-downtime blue/green release model
- Environment-specific app settings managed as code
- App settings applied immediately after each deployment/swap
- Graceful handling when no settings file exists for an app
- Scales from 1 to N Function Apps
- Enterprise-ready structure

---

## Repository Structure
```text
├── .azure-pipelines/
│   └── azure-pipelines.yml
├── config/
│   └── functionapps.json
├── appsettings/
│   ├── funcApp01/
│   │   ├── devtest_appsettings.json
│   │   ├── staging_appsettings.json
│   │   └── production_appsettings.json
│   ├── funcApp02/
│   │   ├── devtest_appsettings.json
│   │   ├── staging_appsettings.json
│   │   └── production_appsettings.json
├── README.md
└── .gitignore
```

---

## How It Works

### 1. Runtime Selection

When running the pipeline manually:
- Select individual Function Apps using checkboxes
- OR enable "Deploy All" to deploy every app listed in metadata

---

### 2. Metadata Configuration

Function Apps are defined inside `config/functionapps.json`:
```json
[
  {
    "name": "funcApp01",
    "path": "src/FunctionApp01/FunctionApp01.csproj",
    "resourceGroup": "rg-functionapps-dev"
  }
]
```

No pipeline logic needs to change when adding new applications.

---

### 3. App Settings Management

Each Function App has its own app settings file per environment stored in the `appsettings/` folder. These are applied automatically after each deployment.

**File format:**
```json
{
  "Environment": "Development",
  "AppName": "funcApp01",
  "StorageAccountName": "devstorageaccount",
  "TimerSchedule": "0 */5 * * * *",
  "LogLevel": "Information"
}
```

**Key behaviours:**
- Settings are merged — only the keys you define are updated, existing Azure system settings are left untouched
- If no settings file exists for an app it is skipped gracefully without failing the pipeline
- Production settings are applied directly to the production slot after the swap completes
- Sensitive values should be stored as Key Vault references, never as plain text

**Key Vault reference example:**
```json
{
  "MySecret": "@Microsoft.KeyVault(SecretUri=https://your-kv.vault.azure.net/secrets/MySecret/)"
}
```

---

### 4. Deployment Flow Per Application

For each selected Function App:

1. Restore NuGet packages
2. Build in Release mode
3. Publish artifacts
4. Create deployment ZIP
5. Deploy to DevTest slot
6. Apply DevTest app settings
7. Deploy to Staging slot
8. Apply Staging app settings
9. Swap Staging to Production
10. Apply Production app settings

---

## Deployment Strategy

This implementation follows a safe release approach:

- DevTest for validation
- Staging for pre-production verification
- Slot swap to Production for zero downtime
- Production app settings applied immediately after swap
- Instant rollback capability by swapping back

---

## Adding a New Function App

1. Add a boolean parameter in `azure-pipelines.yml`
2. Add a new entry in `config/functionapps.json`
3. Create the app settings folder and files under `appsettings/your-app-name/`
4. Commit changes

No changes are required to deployment logic.

---

## Technologies Used

- Azure DevOps YAML Pipelines
- Azure CLI
- PowerShell
- .NET 8
- Azure Function Apps
- Deployment Slots
- JSON Metadata Configuration
- Azure Key Vault (for secrets in app settings)

---

## Use Cases

This template is ideal for:

- Teams managing multiple Azure Function Apps
- Platform engineering teams standardizing CI/CD
- Enterprises replacing manual Visual Studio publish workflows
- DevOps engineers building scalable deployment templates
- Teams who want app settings changes tracked in source control

---

## Prerequisites

Before using this template, configure:

- Azure DevOps Service Connection with contributor access to your Function Apps
- Target Azure Resource Groups
- Azure Function Apps with deployment slots (devtest, staging, production)
- Artifact feeds (if applicable)
- Azure Key Vault (recommended for storing secrets referenced in app settings)

---

## Using This as a Template

1. Click **"Use this template"**
2. Create your repository
3. Update the following in `azure-pipelines.yml`:
   - `azureServiceConnection` variable with your service connection name
   - NuGet restore sources with your feed URLs
   - Parameter names to match your actual Function App names
4. Update `config/functionapps.json` with your Function App names, project paths, and resource groups
5. Create app settings files under `appsettings/<your-app-name>/` for each environment
6. Run the pipeline manually

---

## App Settings File Location Convention

| Environment | File Path |
|---|---|
| DevTest | `appsettings/<appname>/devtest_appsettings.json` |
| Staging | `appsettings/<appname>/staging_appsettings.json` |
| Production | `appsettings/<appname>/production_appsettings.json` |

---

## Benefits

- Centralized release management
- App settings changes tracked in source control like code changes
- Reduced manual deployment errors
- Consistent deployment process across all environments
- Production-safe release flow with zero downtime
- Scalable architecture for growing systems
- No more manual portal edits for app settings
