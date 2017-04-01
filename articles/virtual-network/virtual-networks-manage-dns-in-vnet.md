---
title: Manage DNS servers used by a virtual network (Classic) - Azure portal preview (Classic) | Azure
description: Learn how to add and remove DNS servers in a virtual network (Classic) using the Azure portal preview (Classic).
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn

ms.assetid: b582be7d-dc78-4cfe-a766-185bd7e5dc68
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
wacn.date: ''
ms.author: jdial
ms.custom: H1Hack27Feb2017

---
# Manage DNS servers used by a virtual network (Classic) using the Azure portal preview (Classic)

You can manage the list of DNS servers used in a virtual network (VNet) in the Azure portal preview (Classic) or in the network configuration file. You can add up to 12 DNS servers for each VNet. When specifying DNS servers, it's important to verify that you list your DNS servers in the correct order for your environment. DNS server lists do not work round-robin. They are used in the order that they are specified. If the first DNS server on the list is able to be reached, the client will use that DNS server regardless of whether the DNS server is functioning properly or not. To change the DNS server order for your virtual network, remove the DNS servers from the list and add them back in the order that you want.

> [!WARNING]
> After the DNS list has been updated, you must restart the virtual machines located in your virtual network so that they pick up the new DNS server settings. Virtual machines will continue to use their current configuration until they are restarted.
> 
> 

## Edit a DNS server list for a virtual network using the Azure portal preview (Classic)
1. Log on to the [Azure portal preview (Classic)](https://manage.windowsazure.cn).
2. In the navigation pane, click **Networks**, and then click the name of your virtual network in the **Name** column.
3. Click **Configure**.
4. In **DNS Servers**, you can configure the following:

    * **To register (add) a new DNS server -** Simply type the name and IP address in the boxes. This adds a DNS server to your virtual network DNS Servers list and also registers the DNS server with Azure.
    * **To add a DNS server that was previously registered -** If you already registered a DNS server with Azure, you can select it from the pre-populated list.
    * **To remove a DNS server from your virtual network -** Click the X next to the server you want to remove. Note that this only removes the server from this virtual network list. The DNS server remains registered in Azure for your other virtual networks to use. To delete a DNS server from your subscription, go to the **Networks ->DNS Servers** page.
    * **To re-order DNS servers -** Remove all of the DNS servers that are listed, and then add them back in in the order that you want. Remember that this is not a round-robin DNS list.
    * **To rename a DNS server -** Highlight the DNS server in the list, then type the new name. This will register a new DNS server in Azure, as well as add it to the DNS Servers list for your virtual network. The old DNS server and its IP address will remain registered with Azure. You can delete it on the **DNS Servers** page if you are not using it for any other virtual networks.
5. Click **Save** at the bottom of the page to save your new DNS servers configuration.
6. Restart the virtual machines located in the virtual network to allow them to acquire the new DNS settings.

## Edit a DNS server list using a network configuration file
To edit a DNS server list by using a network configuration file, you'll first export your configuration settings from the Classic Management Portal. You'll then edit the network configuration file and import it back through the Azure portal preview (Classic). Below is a high-level list of steps to complete this process.

1. Export your virtual network settings to a network configuration file. For more information and steps to export your network configuration settings, see [Export Virtual Network Settings to a Network Configuration File](virtual-networks-using-network-configuration-file.md).
2. Specify the DNS server information for your virtual network. For more information about specifying a DNS server, see [Specifying a DNS Server in a Virtual Network Configuration File](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). For additional information about network configuration files, see [Azure Virtual Network Configuration Schema](https://msdn.microsoft.com/zh-cn/library/azure/jj157100.aspx) and [Configure a Virtual Network Using a Network Configuration File](virtual-networks-using-network-configuration-file.md).
3. Import the network configuration file. For more information and steps to import your network configuration file, see [Import a Network Configuration File](virtual-networks-using-network-configuration-file.md).
4. Restart the virtual machines located in the virtual network to allow them to acquire the new DNS settings.