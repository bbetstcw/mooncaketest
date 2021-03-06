---
title: Azure CLI Script Sample - Create a Premium Azure Redis Cache with clustering | Azure
description: Azure CLI Script Sample - Create a Premium tier Azure Redis Cache with clustering
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: 
tags: azure-service-management

ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/14/2017
wacn.date: ''
ms.author: sdanie
---

# Create a Premium Azure Redis Cache with clustering

In this scenario, you learn how to create a 6 GB Premium tier Azure Redis Cache with clustering enabled and two shards.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## Sample script

```azurecli
#/bin/bash

# Creates a Resource Group named contosoGroup, and creates a Premium Redis Cache with clustering in that group named contosoCache

# Create a Resource Group 
az group create --name contosoGroup --location chinaeast

# Create a Redis Cache
az redis create --name contosoCache --resource-group contosoGroup --location chinaeast --sku-capacity 1 --sku-family P --sku-name Premium --shard-count 2

```

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## Script explanation

This script uses the following commands to create a resource group and a Premium tier redis cache with clustering enable. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Creates a resource group in which all resources are stored. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#create) | Create Redis Cache instance. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional Azure Redis Cache CLI script samples can be found in the [Azure Redis Cache documentation](../cli-samples.md).