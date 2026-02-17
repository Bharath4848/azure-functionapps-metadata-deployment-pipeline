# Azure Function Apps – Metadata-Driven Deployment Pipeline

A production-grade Azure DevOps YAML pipeline for dynamically building and deploying multiple Azure Function Apps using runtime UI selection and metadata configuration.

---

## Overview

This repository provides a scalable and enterprise-ready Azure DevOps pipeline that:

- Builds and deploys multiple Azure Function Apps
- Allows runtime selection from the pipeline UI
- Uses JSON metadata instead of hardcoded values
- Supports multi-slot deployments (DevTest → Staging → Production)
- Enables zero-downtime production releases using slot swap

This template is designed for teams managing multiple Function Apps who require centralized, safe, and automated deployments.

---

## Architecture Flow

Pipeline Runtime Parameters (UI)
↓
functionapps.json (Metadata)
↓
Dynamic Build + Publish
↓
ZIP Packaging
↓
Deploy to DevTest Slot
↓
Deploy to Staging Slot
↓
Slot Swap → Production


---

## Key Features

- Runtime checkbox selection of Function Apps
- “Deploy All” override option
- Metadata-driven configuration (no hardcoding in pipeline logic)
- Multi-slot deployment strategy
- Zero-downtime blue/green release model
- Scales from 1 to N Function Apps
- Enterprise-ready structure

---

## Repository Structure

├── .azure-pipelines/
│ └── azure-pipelines.yml
├── config/
│ └── functionapps.json
├── README.md
└── .gitignore


---

## How It Works

### 1. Runtime Selection

When running the pipeline manually:

- Select individual Function Apps using checkboxes
- OR enable "Deploy All" to deploy every app listed in metadata

---

### 2. Metadata Configuration

Function Apps are defined inside:

`config/functionapps.json`

Example:

```json
[
  {
    "name": "funcApp01",
    "path": "src/FunctionApp01/FunctionApp01.csproj",
    "resourceGroup": "rg-functionapps-dev"
  }
]

No pipeline logic needs to change when adding new applications.

3. Deployment Flow Per Application

For each selected Function App:

Restore NuGet packages

Build in Release mode

Publish artifacts

Create deployment ZIP

Deploy to DevTest slot

Deploy to Staging slot

Swap Staging → Production

Deployment Strategy

This implementation follows a safe release approach:

DevTest for validation

Staging for pre-production verification

Slot swap to Production

Zero downtime

Instant rollback capability

Adding a New Function App

Add a boolean parameter in azure-pipelines.yml

Add a new entry in functionapps.json

Commit changes

No changes required to deployment logic.

Technologies Used

Azure DevOps YAML Pipelines

Azure CLI

PowerShell

.NET 8

Azure Function Apps

Deployment Slots

JSON Metadata Configuration

Use Cases

This template is ideal for:

Teams managing multiple Azure Function Apps

Platform engineering teams standardizing CI/CD

Enterprises replacing manual Visual Studio publish workflows

DevOps engineers building scalable deployment templates

Prerequisites

Before using this template, configure:

Azure DevOps Service Connection

Target Azure Resource Groups

Azure Function Apps with deployment slots

Artifact feeds (if applicable)

Using This as a Template

Click "Use this template"

Create your repository

Update:

Service connection name

Resource group names

Function app names

Run the pipeline manually

Benefits

Centralized release management

Reduced manual deployment errors

Consistent deployment process

Production-safe release flow

Scalable architecture for growing systems
