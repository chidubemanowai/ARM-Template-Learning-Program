# Azure VM ARM Template Deployment Guide

## Overview

This document provides instructions for deploying an Azure Virtual Machine infrastructure using an Azure Resource Manager (ARM) template.

The deployment creates the required Azure resources, including:

* Resource Group
* Virtual Network
* Subnet
* Network Security Group
* Public IP Address
* Network Interface
* Virtual Machine

## Prerequisites

Before deployment, ensure the following requirements are met:

* An active Azure subscription
* Azure CLI installed
* Appropriate permissions to create resources
* ARM template files available in the repository

Verify Azure CLI installation:

```bash
az --version
```

---

## 1. Authenticate to Azure

Sign in to Azure using Azure CLI:

```bash
az login
```

List available subscriptions:

```bash
az account list --output table
```

Select the required subscription:

```bash
az account set --subscription "<SUBSCRIPTION_ID>"
```

---

## 2. Create Resource Group

Create a resource group to contain the deployed resources:

```bash
az group create \
  --name ARMDeploymentRG \
  --location eastus
```

Verify the resource group:

```bash
az group show \
  --name ARMDeploymentRG
```

---

## 3. Validate ARM Template

Before deployment, validate the ARM template:

```bash
az deployment group validate \
  --resource-group ARMDeploymentRG \
  --template-file ../arm-template/azuredeploy.json \
  --parameters ../arm-template/azuredeploy.parameters.json
```

A successful validation returns:

```
Validation succeeded.
```

---

## 4. Deploy Azure Infrastructure

Deploy the ARM template:

```bash
az deployment group create \
  --resource-group ARMDeploymentRG \
  --template-file ../arm-template/azuredeploy.json \
  --parameters ../arm-template/azuredeploy.parameters.json \
  --name VMDeployment
```

Example successful output:

```text
ProvisioningState: Succeeded
DeploymentName: VMDeployment
ResourceGroup: ARMDeploymentRG
```

---

## 5. Monitor Deployment Status

Check deployment details:

```bash
az deployment group show \
  --resource-group ARMDeploymentRG \
  --name VMDeployment
```

List all deployments:

```bash
az deployment group list \
  --resource-group ARMDeploymentRG \
  --output table
```

---

## 6. Retrieve Deployment Outputs

Export deployment outputs:

```bash
az deployment group show \
  --resource-group ARMDeploymentRG \
  --name VMDeployment \
  --query properties.outputs \
  --output json > ../outputs/deployment-output.json
```

The output file contains information such as:

* Virtual Machine Resource ID
* Public IP Address
* Network Interface ID

---

## 7. Verify Virtual Machine Deployment

List the deployed virtual machine:

```bash
az vm list \
  --resource-group ARMDeploymentRG \
  --output table
```

Check VM status:

```bash
az vm get-instance-view \
  --resource-group ARMDeploymentRG \
  --name <VM_NAME> \
  --query instanceView.statuses
```

Expected result:

```
PowerState/running
```

---

## 8. Connect to the Virtual Machine

### Linux Virtual Machine

Retrieve the public IP address:

```bash
az vm show \
  --resource-group ARMDeploymentRG \
  --name <VM_NAME> \
  --show-details \
  --query publicIps \
  --output tsv
```

Connect using SSH:

```bash
ssh <USERNAME>@<PUBLIC_IP>
```

Successful connection example:

```text
<USERNAME>@<VM_NAME>:~$
```

---

### Windows Virtual Machine

Retrieve the public IP:

```bash
az vm show \
  --resource-group ARMDeploymentRG \
  --name <VM_NAME> \
  --show-details \
  --query publicIps \
  --output tsv
```

Connect using Remote Desktop:

```bash
mstsc /v:<PUBLIC_IP>
```

---

## 9. Verification Evidence

The following evidence is included in the repository:

| File                                  | Description                                  |
| ------------------------------------- | -------------------------------------------- |
| `verification/deployment-success.png` | Screenshot showing successful ARM deployment |
| `verification/vm-connectivity.png`    | Screenshot proving VM access                 |
| `verification/deployment-log.txt`     | Deployment completion log                    |
| `outputs/deployment-output.json`      | Deployment output values                     |

---

## 10. Cleanup Resources

To remove all deployed resources:

```bash
az group delete \
  --name ARMDeploymentRG \
  --yes \
  --no-wait
```

---

## Deployment Summary

| Item              | Value                           |
| ----------------- | ------------------------------- |
| Deployment Method | Azure Resource Manager Template |
| Deployment Tool   | Azure CLI                       |
| Template File     | `azuredeploy.json`              |
| Parameters File   | `azuredeploy.parameters.json`   |
| Resource Group    | ARMDeploymentRG                 |
| Deployment Status | Succeeded                       |
