---
title: Create an Internet-facing load balancer - Azure portal preview classic | Azure
description: Learn how to create an Internet facing load balancer in classic deployment model using the Azure Classic Management Portal
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: ''
tags: azure-service-management

ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
wacn.date: ''
ms.author: kumud
---

# Get started creating an Internet facing load balancer (classic) in the Azure Classic Management Portal
> [!div class="op_single_selector"]
>- [Azure Classic Management Portal](./load-balancer-get-started-internet-classic-portal.md)
>- [PowerShell](./load-balancer-get-started-internet-classic-ps.md)
>- [Azure CLI](./load-balancer-get-started-internet-classic-cli.md)
>- [Azure Cloud Services](./load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Before you work with Azure resources, it's important to understand that Azure currently has two deployment models: Azure Resource Manager and classic. Make sure you understand [deployment models and tools](../azure-classic-rm.md) before you work with any Azure resource. You can view the documentation for different tools by clicking the tabs at the top of this article. This article covers the classic deployment model. You can also [Learn how to create an Internet facing load balancer using Azure Resource Manager](./load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Set up an Internet-facing load balancer for virtual machines

In order to load balance network traffic from the Internet across the virtual machines of a cloud service, you must create a load-balanced set. This procedure assumes that you have already created the virtual machines and that they are all within the same cloud service.

**To configure a load-balanced set for virtual machines**

1. In the Azure Classic Management Portal, click **Virtual Machines**, and then click the name of a virtual machine in the load-balanced set.
2. Click **Endpoints**, and then click **Add**.
3. On the **Add an endpoint to a virtual machine** page, click the right arrow.
4. On the **Specify the details of the endpoint** page:

    * In **Name**, type a name for the endpoint or select the name from the list of predefined endpoints for common protocols.
    * In **Protocol**, select the protocol required by the type of endpoint, either TCP or UDP, as needed.
    * In **Public Port and Private Port**, type the port numbers that you want the virtual machine to use, as needed. You can use the private port and firewall rules on the virtual machine to redirect traffic in a way that is appropriate for your application. The private port can be the same as the public port. For example, for an endpoint for web (HTTP) traffic, you could assign port 80 to both the public and private port.

5. Select **Create a load-balanced set**, and then click the right arrow.
6. On the **Configure the load-balanced set** page, type a name for the load-balanced set, and then assign the values for probe behavior of the Azure Load Balancer. The Load Balancer uses probes to determine if the virtual machines in the load-balanced set are available to receive incoming traffic.
7. Click the check mark to create the load-balanced endpoint. You will see **Yes** in the **Load-balanced set name** column of the **Endpoints** page for the virtual machine.
8. In the portal, click **Virtual Machines**, click the name of an additional virtual machine in the load-balanced set, click **Endpoints**, and then click **Add**.
9. On the **Add an endpoint to a virtual machine** page, click **Add endpoint to an existing load-balanced set**, select the name of the load-balanced set, and then click the right arrow.
10. On the **Specify the details of the endpoint** page, type a name for the endpoint, and then click the check mark.

For the additional virtual machines in the load-balanced set, repeat steps 8-10.

## Next steps

[Get started configuring an internal load balancer](./load-balancer-get-started-ilb-arm-ps.md)

[Configure a load balancer distribution mode](./load-balancer-distribution-mode.md)

[Configure idle TCP timeout settings for your load balancer](./load-balancer-tcp-idle-timeout.md)