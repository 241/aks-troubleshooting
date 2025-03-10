---
title: 'Automatic Crash dumps'
---

## Scenario

In this step, you will learn how to collect crash dumps from a .NET Core application running in Azure Kubernetes Service (AKS). You will configure the application to generate crash dumps automatically whenever it crashes.

When a crash occurs, the application will generate a crash dump at the specified path. However, each crash will cause the application to restart, resulting in the loss of the dump data. To prevent this, you can mount an Azure disk to store the crash dump data. By doing so, the crash dump data will be persisted even after the application restarts, ensuring that you can analyze the crash dumps later.

In the yaml file below, you will find the configuration to deploy the application, create a persistent volume claim, and mount the Azure disk to store the crash dump data.

```yaml title="deploy.yaml"
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-disk
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Standard_LRS
  kind: Managed
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-disk-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azure-disk
  resources:
    requests:
      storage: 5Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: buggybits-deployment
  labels:
    app: buggybits
spec:
  replicas: 1
  selector:
    matchLabels:
      app: buggybits
  template:
    metadata:
      labels:
        app: buggybits
        color: yellow
    spec:
      containers:
      - name: buggybits
        image: akstroubleshooting26022512b.azurecr.io/buggybits:latest
        securityContext:
          capabilities:
            add: ["SYS_PTRACE"]
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
        env:
        - name: ASPNETCORE_ENVIRONMENT
          value: "DEV"
        - name: ASPNETCORE_URLS
          value: "http://+:8080"
        - name: DOTNET_DbgEnableMiniDump
          value: "1"
        - name: DOTNET_DbgMiniDumpType
          value: "4"
        - name: DOTNET_DbgMiniDumpName
          value: "/mnt/azure/crashdump_%h_<pid>_%t.dmp"
        volumeMounts:
          - mountPath: "/mnt/azure"
            name: azure-disk-storage
      volumes:
      - name: azure-disk-storage
        persistentVolumeClaim:
          claimName: azure-disk-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: buggybits-service
  labels:
    app: buggybits
spec:
  type: LoadBalancer
  ports:
  - name: http-port
    port: 80
    targetPort: 8080
    protocol: TCP
  selector:
    app: buggybits

```
