---
title: Azure Quick Start - Create VM CLI | Azure
description: Quickly learn to create virtual machines with the Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
wacn.date: ''
ms.author: nepeters
---

# Create a Linux virtual machine with the Azure CLI

The Azure CLI is used to create and manage Azure resources from the command line or in scripts. This guide details using the Azure CLI to deploy a virtual machine running Ubuntu 16.04 LTS.

Before you start, make sure that the Azure CLI has been installed. For more information, see [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## Log in to Azure 

Log in to your Azure subscription with the [az login](https://docs.microsoft.com/cli/azure/#login) command and follow the on-screen directions.

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

```azurecli
az login
```

## Create a resource group

Create a resource group with the [az group create](https://docs.microsoft.com/cli/azure/group#create) command. An Azure resource group is a logical container into which Azure resources are deployed and managed. 

The following example creates a resource group named `myResourceGroup` in the `chinanorth` location.

```azurecli
az group create --name myResourceGroup --location chinanorth
```

## Create virtual machine

Create a VM with the [az vm create](https://docs.microsoft.com/cli/azure/vm#create) command. 

The following example creates a VM named `myVM` and creates SSH keys if they do not already exist in a default key location. To use a specific set of keys, use the `--ssh-key-value` option.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

When the VM has been created, the Azure CLI shows information similar to the following example. Take note of the public IP address. This address is used to access the VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "chinanorth",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## Connect to virtual machine

Use the following command to create an SSH session with the virtual machine. Replace the IP address with the public IP address of your virtual machine.

```bash 
ssh <Public IP Address>
```

## Delete virtual machine

When no longer needed, the following command can be used to remove the Resource Group, VM, and all related resources.

```azurecli
az group delete --name myResourceGroup
```

## Next steps

[Create highly available virtual machines tutorial](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explore VM deployment CLI samples](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)