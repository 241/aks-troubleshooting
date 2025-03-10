---
title: 'Using dotnet-dump'
---

## Scenario

First, you need to install dotnet-dump into your application runtime image. Then, you can use dotnet-dump to collect memory dumps from your application running on Azure Kubernetes Service (AKS).

> Starting with .NET 8, it is recommended to use non-root `app` user to create more secure environment.
https://devblogs.microsoft.com/dotnet/securing-containers-with-rootless/

### Working with non-root user in Dockerfile

Use the Dockerfile below to install these tools into container image:
- dotnet-dump as a global tool so `dotnet-dump collect` command can be used in the container.
- procps package to use the `ps`  and `top` commands.

```yaml
# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080

# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["BuggyBits.csproj", "."]
RUN dotnet restore "./BuggyBits.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "./BuggyBits.csproj" -c $BUILD_CONFIGURATION -o /app/build
# Install dotnet-dump as a global tool
RUN dotnet tool install -g dotnet-dump

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./BuggyBits.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
COPY --from=publish /root/.dotnet/tools /usr/local/bin

# Install procps package
USER root
RUN apt-get update && apt-get install -y procps

ENTRYPOINT ["dotnet", "BuggyBits.dll"]
```

```bash
TODO: açıklamalı olarak düzenlenecek
kubectl get pods
kubectl exec -it buggybits-deployment-f8b566c7c-qk7bh -- bash

dotnet-dump ps
dotnet-dump collect -p 1 --output /tmp/dump.dmp
```

### Working with root user in running pod

If you are using a container image with the root user, you can use the following commands to install dotnet-dump into the currently running container image:

Dockerfile:
```yaml title="Dockerfile"
# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
#USER $APP_UID
WORKDIR /app
EXPOSE 8080


# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["BuggyBits.csproj", "."]
RUN dotnet restore "./BuggyBits.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "./BuggyBits.csproj" -c $BUILD_CONFIGURATION -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./BuggyBits.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "BuggyBits.dll"]
```


```ps 
#first check the current user
whoami
#if you are root user, you can install dotnet-dump with the following commands
apt update
apt install wget
wget -O dotnet-dump https://aka.ms/dotnet-dump/linux-x64
chmod 777 ./dotnet-dump
#you can remove wget after installation
apt remove wget
#optionally you can move dotnet-dump to bin folder so you can easily run dotnet-dump command in bash
mv dotnet-dump /usr/local/bin
#check the dotnet-dump version
dotnet-dump --version
#check process id
dotnet-dump ps
#you can use dotnet-dump to collect memory dumps
dotnet-dump collect -p 1 --output /tmp/dump.dmp
```

> If you don't have internet connection, you can download the dotnet-dump binary to your local environment and copy it to the running pod.

