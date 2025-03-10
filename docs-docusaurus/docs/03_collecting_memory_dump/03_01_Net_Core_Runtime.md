---
title: 'Using .NET Runtime (createdump)'
---

## Scenario

In this step, you will learn how to collect memory dump Azure Kubernetes Service (AKS) without any additional tool required.

## Steps

Generally, if there is only one process in the container, its process ID will be 1. The following command assumes the process ID is 1:

```bash
kubectl get pods
kubectl exec -it buggybits-deployment-f8b566c7c-qk7bh -- bash

dotnet --list-runtimes

#update the runtime version
/usr/share/dotnet/shared/Microsoft.NETCore.App/8.0.13/createdump --full 1

```

