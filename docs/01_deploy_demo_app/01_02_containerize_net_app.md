---
title: '2. Containerize & Deploy BuggyBits Application'
layout: default
nav_order: 2
parent: 'Step 1: Deploy application'
---

# Step 2 - Containerize BuggyBits Application

## Scenario

In this step, you will containerize the BuggyBits application and deploy it to Azure Kubernetes Service (AKS). BuggyBits is a sample application designed to demonstrate common issues and troubleshooting techniques in Kubernetes architecture.

## Objectives

After you complete this lab, you will be able to:

* Build and push a Docker image to Azure Container Registry
* Deploy the BuggyBits application to AKS
* Verify the deployment
* Access the BuggyBits application

## Duration

* **Estimated Time:** 60 minutes

## Prerequisites

Before you begin, ensure you have the following:

* Docker Desktop installed
* VSCode installed
* .NET 8 SDK installed

## Steps

### Step 1: Clone the BuggyBits Repository

1. Open Visual Studio Code.

2. Open the Command Palette by pressing `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac).

3. Type `Git: Clone` and select the `Git: Clone` option.

4. When prompted, enter the repository URL: `https://github.com/241/aks-troubleshooting.git`.

5. Choose a local directory to clone the repository into.

6. Once the repository is cloned, open the cloned directory in Visual Studio Code.

7. You should now see the BuggyBits repository files in the VSCode Explorer pane.

### Step 2: Build the BuggyBits Application

1. Open a terminal in src\BuggyBits folder.
2. Run the following command to build the application:

    ```bash
    dotnet build
    ```

### Step 3: Build Docker Image

1. Run the following command to build the Docker image:

    ```bash
    $ACR_NAME = "akstroubleshooting26022512b" #(Replace the value from the previous step)
    docker build -t "$ACR_NAME.azurecr.io/buggybits" .
    ```

    > **Note:** Dockerfile is already provided in the folder.


### Step 4: Push Docker Image to Azure Container Registry

1. Run the following command to log in to Azure Container Registry:

    ```bash
    az acr login --name $ACR_NAME
    ```

2. Run the following command to push the Docker image to Azure Container Registry:

    ```bash
    docker push "$ACR_NAME.azurecr.io/buggybits"
    ```

### Step 5: Deploy BuggyBits Application to AKS

1. Open deploy.yaml file and replace the value of `image` with the ACR_NAME from previous step and save the file.

2. Run the following command to deploy the BuggyBits application to AKS:

    ```bash
    kubectl apply -f deploy.yaml
    ```

    > **Note:** The `deploy.yaml` file is already provided in the repository.
3. Run the following command to verify the deployment:

    ```bash
    kubectl get pods
    ```

4. Run the following command to verify the service:

    ```bash
    kubectl get svc
    ```

5. Run the following command to get the external IP address:

    ```bash
    kubectl get svc buggybits-service --output jsonpath='{.status.loadBalancer.ingress[0].ip}'
    ```

6. Open a browser and navigate to `http://<external-ip>` as the application will be exposed to internet with 80 port.

   ``` bash
   http://74.178.99.127
   ```

7. You should see the BuggyBits application running.

You have successfully containerized the BuggyBits application and deployed it to AKS. In the next step, you will troubleshoot the application.

