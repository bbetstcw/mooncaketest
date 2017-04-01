---
title: Defining and managing state | Azure
description: How to define and manage service state in Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
wacn.date: ''
ms.author: masnider
---

# Service state
**Service state** refers to the data that the service requires to function. It includes the data structures and variables that the service reads and writes to do work.

Consider a calculator service, for example. This service takes two numbers and returns their sum. This is a purely stateless service that has no data associated with it.

Now consider the same calculator, but in addition to computing sum, it also has a method for returning the last sum it has computed. This service is now stateful - it contains some state that it writes to (when it computes a new sum) and reads from (when it returns the last computed sum).

In Azure Service Fabric, the first service is called a stateless service. The second service is called a stateful service.

## Storing service state
State can be either externalized or co-located with the code that is manipulating the state. Externalization of state is typically done by using an external database or store. In our calculator example, this could be a SQL database in which the current result is stored in a table. Every request to compute the sum performs an update on this row.

State can also be co-located with the code that manipulates this code. Stateful services in Service Fabric can be built using this model. Service Fabric provides the infrastructure to ensure that this state is highly available and fault tolerant in the event of a failure.

## Next steps
For more information on Service Fabric concepts, see the following articles:

- [Availability of Service Fabric services](./service-fabric-availability-services.md)

- [Scalability of Service Fabric services](./service-fabric-concepts-scalability.md)
* [Partitioning Service Fabric services](./service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](./service-fabric-reliable-services-introduction.md)