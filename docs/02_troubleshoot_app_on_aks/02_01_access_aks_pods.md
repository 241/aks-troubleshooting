---
title: 'Access to pods in AKS'
layout: default
nav_order: 2
parent: 'Step 2: Troubleshoot application'
---

# Step 1 - Access to pods in AKS

## Scenario

In this step, you will learn how to access the pods running in Azure Kubernetes Service (AKS) using `kubectl`.

## Objectives

After you complete this step, you will be able to:

* Access the pods running in AKS
* Troubleshoot issues with pod connectivity
* Understand the different ways to access pods in AKS
* Use `kubectl` to interact with pods
* Use `kubectl` to troubleshoot pod issues
* Use `kubectl` to view logs from pods
* Use `kubectl` to execute commands in pods
* Use `kubectl` to copy files to and from pods

## Duration

* **Estimated Time:** 60 minutes

```bash
TODO: açıklamalı olarak düzenlenecek
docker get pods
docker exec -it buggybits-deployment-f8b566c7c-67qdt -- bash


mv core_20250226_224831 core_20250226_224831.dmp
tar -czvf core_20250226_224831.tar.gz core_20250226_224831.dmp
ls -lh

kubectl cp buggybits-deployment-f8b566c7c-67qdt:/app/core_20250226_224831.tar.gz .core_20250226_224831.tar.gz
```

