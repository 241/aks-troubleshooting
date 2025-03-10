---
title: '1. Create Azure Resources'
---

# Step 1 - Create Azure Resources

## Scenario

In this step, you will create the necessary Azure resources, including an Azure Kubernetes Service (AKS) cluster and an Azure Container Registry (ACR). These resources will be used to deploy and manage your applications in a Kubernetes environment.

## Objectives

After you complete this step, you will be able to:

* Create an Azure Container Registry (ACR) to store Docker images
* Create an AKS cluster using Azure CLI
* Connect to the AKS cluster using kubectl

## Duration

* **Estimated Time:** 30 minutes

## Prerequisites

Before you begin, ensure you have the following:

<!-- * Windows? -->
* An active Azure subscription
* Latest Azure CLI installed

    ``` bash
    winget install --exact --id Microsoft.AzureCLI
    ```

    > **Note:** If `winget` is not available, you can download the Azure CLI from the following URL: [https://aka.ms/installazurecliwindowsx64](https://aka.ms/installazurecliwindowsx64)
* Latest kubectl installed

    ``` bash
    az aks install-cli
    ```

> **Note:** You will need to restart your terminal or shell for these installations to take effect.

## Steps

### Connect to Azure via CLI

1. Open a Windows Terminal window (defaults to PowerShell):

2. Login to Azure and select the appropriate subscription:

    ``` bash
    az login
    ```

<!-- 
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
-->

### Create Azure Container Registry

1. Create an Azure Container Registry (ACR) to store the Docker images:

    ``` bash
    $SUFFIX=$(New-Guid).ToString().Substring(0, 3)
    $RESOURCE_GROUP="aks-troubleshooting-$SUFFIX-rg"
    $LOCATION="northeurope"
    $ACR_NAME="akstroubleshooting$(Get-Date -Format 'ddMMyy')$SUFFIX"
    az group create --name $RESOURCE_GROUP --location $LOCATION
    az acr create --resource-group $RESOURCE_GROUP --name $ACR_NAME --sku Basic
    Write-Host "ACR_NAME: $ACR_NAME"
    ```

    > **Note:** Please note ACR_NAME for future reference. You will need it while pushing your Docker images to the ACR.

### Step 2: Create AKS Cluster

1. Run these powershell commands:

    ```ps
    $AKS_NAME="aks-troubleshooting-$SUFFIX"
    $VM_SKU="Standard_D4ds_v5"
    az aks create --node-count 1 --generate-ssh-keys --node-vm-size $VM_SKU --name $AKS_NAME --resource-group $RESOURCE_GROUP --attach-acr $ACR_NAME
    ```

    > **Note:** The creation process may take about 5-10 minutes. The cluster will have 1 node and the VM size will be `Standard_D4ds_v5`. 

     > **Note 2:** If the VM creation fails in your selected location, consider changing the location to another region such as `westeurope` or `eastus`. To view all available locations, use the following command:

    ``` bash
    az account list-locations -o table
    ```

2. Once complete, connect the cluster to your local client machine. This command downloads the credentials and configures kubectl to use them. As kubectl stores the credentials in the `~/.kube/config` file (or `C:\Users\<your-username>\.kube\config` on Windows), kubectl will use these credentials to interact with the AKS cluster.

    ``` bash
    az aks get-credentials --name $AKS_NAME --resource-group $RESOURCE_GROUP
    ``` 

3. Verify the connection to the cluster:

    ``` bash
    kubectl get nodes
    ```

4. You should be able to see output as shown below:

    ``` bash
    NAME                                STATUS   ROLES    AGE     VERSION
    aks-nodepool1-12345678-vmss000000   Ready    <none>   4m47s   v1.30.9
    ```

The successful output indicates that the AKS cluster has been created and you are connected to it. Now, proceed to the next step.
