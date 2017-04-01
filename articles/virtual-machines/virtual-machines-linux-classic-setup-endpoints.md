---
title: Set up endpoints on a classic Linux VM | Azure
description: Learn to set up endpoints for a Linux VM in the Azure Classic Management Portal to allow communication with a Linux virtual machine in Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
wacn.date: ''
ms.author: cynthn

---
# How to set up endpoints on a Linux classic virtual machine in Azure
All Linux virtual machines that you create in Azure using the classic deployment model can automatically communicate over a private network channel with other virtual machines in the same cloud service or virtual network. However, computers on the Internet or other virtual networks require endpoints to direct the inbound network traffic to a virtual machine. This article is also available for [Windows virtual machines](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure has two different deployment models for creating and working with resources: [Resource Manager and Classic](../azure-resource-manager/resource-manager-deployment-model.md). This article covers using the Classic deployment model. Azure recommends that most new deployments use the Resource Manager model.

In the **Resource Manager** deployment model, endpoints are configured using **Network Security Groups (NSGs)**. For more information, see [Opening ports and endpoints](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

When you create a Linux virtual machine in the Azure Classic Management Portal, an endpoint for Secure Shell (SSH) is typically created for you automatically. You can configure additional endpoints while creating the virtual machine or afterwards as needed.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Next steps
* You can also create a VM endpoint by using the [Azure Command-Line Interface](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Run the **azure vm endpoint create** command.
* If you created a virtual machine in the Resource Manager deployment model, you can use the Azure CLI in Resource Manager mode to [create network security groups](../virtual-network/virtual-networks-create-nsg-arm-cli.md) to control traffic to the VM.