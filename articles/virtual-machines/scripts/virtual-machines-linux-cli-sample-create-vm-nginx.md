---
title: Azure CLI Script Sample - Create a Linux VM with NGINX| Azure
description: Azure CLI Script Sample - Create a Linux VM with NGINX
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management

ms.assetid:
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
wacn.date: ''
ms.author: nepeters
---

# Create a VM with NGINX

This script creates an Azure Virtual Machine and uses the Azure Virtual Machine Custom Script Extension to install NGINX. After running the script, you can access a demo website on the public IP address of the virtual machine.

If needed, install the Azure CLI using the instruction found in the [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli), and then run `az login` to create a connection with Azure.

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

This sample works in a Bash shell. For options on running Azure CLI scripts on Windows client, see [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md).

## Sample script

```azurecli
#!/bin/bash

# Create a resource group.
az group create --name myResourceGroup --location chinanorth

# Create a new virtual machine, this creates SSH keys if not present.
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys

# Open port 80 to allow web traffic to host.
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 

# Use CustomScript extension to install NGINX.
az vm extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --vm-name myVM \
  --resource-group myResourceGroup \
  --settings '{"commandToExecute":"apt-get -y update && apt-get -y install nginx"}'
```

## Custom Script Extension

The custom script extension copies this script onto the virtual machine. The script is then run to install and configure an NGINX web server. 

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## Clean up deployment 

Run the following command to remove the resource group, VM, and all related resources.

```azurecli
az group delete --name myResourceGroup
```

## Script explanation

This script uses the following commands to create a resource group, virtual machine, and all related resources. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Creates a resource group in which all resources are stored. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Creates the virtual machine. This command also specifies the virtual machine image to be used, and administrative credentials.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Creates a network security group rule to allow inbound traffic. In this sample, port 80 is opened for HTTP traffic. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Adds and runs a virtual machine extension to a VM. In this sample, the custom script extension is used to install NGINX.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional virtual machine CLI script samples can be found in the [Azure Linux VM documentation](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).