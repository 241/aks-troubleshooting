---
title: 'Step 1: Deploy application'
layout: default
nav_order: 2
has_children: true
---

# Step 01 - Deploy Application

## Scenario

In this step, you will deploy the BuggyBits application to Azure Kubernetes Service (AKS). BuggyBits is a sample application designed to demonstrate common issues and troubleshooting techniques in kubernetes architecture.

## Objectives

After you complete this lab, you will be able to:

* Create an AKS cluster using Azure CLI
* Connect to the AKS cluster using kubectl
* Build and push a Docker image to Azure Container Registry
* Deploy the BuggyBits application to AKS

## Duration

* **Estimated Time:** 60 minutes

## Prerequisites

Before you begin, ensure you have the following:

* An active Azure subscription
* Latest Azure CLI installed

    ``` bash
    winget install --exact --id Microsoft.AzureCLI
    ```

    > If winget is not available, download from using this URL: [https://aka.ms/installazurecliwindowsx64](https://aka.ms/installazurecliwindowsx64)
* Latest kubectl installed

    ``` bash
    az aks install-cli
    ```

> **Note:** You will need to restart your terminal or shell for these installations to take effect.

## Steps

### Step 1: Provision Azure Resources

1. Open a Windows Terminal window (defaults to PowerShell):

2. Login to Azure and select the appropriate subscription:

    ``` bash
    az login
    ```

3. TODO: Check if needed? ~~Register needed providers?~~

    ``` bash
    az provider register --namespace Microsoft.Storage
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    az provider register --namespace Microsoft.Monitor
    az provider register --namespace Microsoft.Insights
    az provider register --namespace Microsoft.ManagedIdentity
    az provider register --namespace Microsoft.OperationalInsights
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.KeyVault
    az provider register --namespace Microsoft.ContainerRegistry
    az provider register --namespace Microsoft.ContainerService
    az provider register --namespace Microsoft.Kubernetes
    ```

4. Run these powershell commands:

    ```ps
    $SUFFIX=$(New-Guid).ToString().Substring(0, 3)
    $AKS_NAME="aks-troubleshooting-$SUFFIX"
    $RESOURCE_GROUP="$AKS_NAME-rg"
    $LOCATION="northeurope"
    $VM_SKU="Standard_D4ds_v5"
    ```

5. Create a resource group:

    ``` bash
    az group create --name $RESOURCE_GROUP --location $LOCATION
    ```

6. Create a simple AKS cluster:

    ``` bash
    az aks create --node-count 1 --generate-ssh-keys --node-vm-size $VM_SKU --name $AKS_NAME --resource-group $RESOURCE_GROUP
    ```

    > **Note:** The creation process may take about 5-10 minutes.

7. Once complete, connect the cluster to your local client machine.

    ``` bash
    az aks get-credentials --name $AKS_NAME --resource-group $RESOURCE_GROUP
    ```

8. Verify the connection to the cluster:

    ``` bash
    kubectl get nodes
    ```

You should be able to see output as shown below:

``` bash
NAME                                STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   10m   v1.21.2
```

It means that AKS cluster is successfully created and you are able to connect to it. Now, let's move to the next step.