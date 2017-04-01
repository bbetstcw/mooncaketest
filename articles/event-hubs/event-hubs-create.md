---
title: Create an Azure Event Hub | Azure
description: Create an Azure Event Hubs namespace and an Event Hub using the Azure portal preview
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: ''

ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2017
wacn.date: ''
ms.author: jotaub
---

# Create an Event Hubs namespace and an Event Hub using the Azure portal preview

## Create an Event Hubs namespace

1. Log on to the [Azure portal][Azure portal], and click **New** at the top left of the screen.

2. Click **Internet of Things**, then click **Event Hubs**.

    ![](./media/event-hubs-create/create-event-hub9.png)

3. In the **Create namespace** blade, enter a namespace name. The system immediately checks to see if the name is available.

    ![](./media/event-hubs-create/create-event-hub1.png)

4. After making sure the namespace name is available, choose the pricing tier (Basic or Standard). Also, choose an Azure subscription, resource group, and location in which to create the resource. 

5. Click **Create** to create the namespace.

6. In the Event Hubs namespace list, click the newly-created namespace.      

    ![](./media/event-hubs-create/create-event-hub2.png)

7. In the namespace blade, click **Event Hubs**.

    ![](./media/event-hubs-create/create-event-hub3.png)

## Create an Event Hub

1. At the top of the blade, click **Add Event Hub**.

    ![](./media/event-hubs-create/create-event-hub4.png)

2. Type a name for your Event Hub, then click **Create**.

    ![](./media/event-hubs-create/create-event-hub5.png)

3. In the list of Event Hubs, click the newly created Event Hub name. 

     ![](./media/event-hubs-create/create-event-hub6.png)

4. Back in the namespace blade (not the specific Event Hub blade), click **Shared access policies**, and then click **RootManageSharedAccessKey**.

     ![](./media/event-hubs-create/create-event-hub7.png)

5. Click the copy button to copy the **RootManageSharedAccessKey** connection string to the clipboard. Save this connection string to use later in the tutorial.

     ![](./media/event-hubs-create/create-event-hub8.png)

Your Event Hub is now created, and you have the connection strings you need to send and receive events.

## Next steps
To learn more about Event Hubs, visit these links:

* [Event Hubs overview](./event-hubs-overview.md)
* [Event Hubs API overview](./event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.cn/