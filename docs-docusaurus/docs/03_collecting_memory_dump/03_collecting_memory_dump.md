---
title: 'Collecting memory dump'
---

## Scenario

In this step, you will learn how to collect memory dump Azure Kubernetes Service (AKS) without any additional tool required.

## Objectives

After you complete this step, you will be able to:

## Duration

* **Estimated Time:** 15 minutes

```bash
TODO: açıklamalı olarak düzenlenecek
kubectl get pods
kubectl exec -it buggybits-deployment-f8b566c7c-qk7bh -- bash

dotnet --list-runtimes

#update the runtime version
/usr/share/dotnet/shared/Microsoft.NETCore.App/8.0.13/createdump --full 1

```

