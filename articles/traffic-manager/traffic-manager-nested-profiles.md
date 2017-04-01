---
title: Nested Traffic Manager Profiles | Azure
description: This article explains the 'Nested Profiles' feature of Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''

ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
wacn.date: ''
ms.author: kumud
---

# Nested Traffic Manager profiles

Traffic Manager includes a range of traffic-routing methods that allow you to control how Traffic Manager chooses which endpoint should receive traffic from each end user. For more information, see [Traffic Manager traffic-routing methods](traffic-manager-routing-methods.md).

Each Traffic Manager profile specifies a single traffic-routing method. However, there are scenarios that require more sophisticated traffic routing than the routing provided by a single Traffic Manager profile. You can nest Traffic Manager profiles to combine the benefits of more than one traffic-routing method. Nested profiles allow you to override the default Traffic Manager behavior to support larger and more complex application deployments.

The following examples illustrate how to use nested Traffic Manager profiles in various scenarios.

## Example 1: Combining 'Performance' and 'Weighted' traffic routing

Suppose that you deployed an application in the following Azure regions: China North and China East. You use Traffic Manager's 'Performance' traffic-routing method to distribute traffic to the region closest to the user.

![Single Traffic Manager profile][1]

Now, suppose you wish to test an update to your service before rolling it out more widely. You want to use the 'weighted' traffic-routing method to direct a small percentage of traffic to your test deployment. You set up the test deployment alongside the existing production deployment in China North.

You cannot combine both 'Weighted' and 'Performance traffic-routing in a single profile. To support this scenario, you create a Traffic Manager profile using the two China East site 1 endpoints and the 'Weighted' traffic-routing method. Next, you add this 'child' profile as an endpoint to the 'parent' profile. The parent profile still uses the Performance traffic-routing method and contains the other global deployments as endpoints.

The following diagram illustrates this example:

![Nested Traffic Manager profiles][2]

In this configuration, traffic directed via the parent profile distributes traffic across regions normally. Within China East site 1, the nested profile distributes traffic to the production and test endpoints according to the weights assigned.

When the parent profile uses the 'Performance' traffic-routing method, each endpoint must be assigned a location. The location is assigned when you configure the endpoint. Choose the Azure region closest to your deployment. The Azure regions are the location values supported by the Internet Latency Table. For more information, see [Traffic Manager 'Performance' traffic-routing method](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## Example 2: Endpoint monitoring in Nested Profiles

Traffic Manager actively monitors the health of each service endpoint. If an endpoint is unhealthy, Traffic Manager directs users to alternative endpoints to preserve the availability of your service. This endpoint monitoring and failover behavior applies to all traffic-routing methods. For more information, see [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). Endpoint monitoring works differently for nested profiles. With nested profiles, the parent profile doesn't perform health checks on the child directly. Instead, the health of the child profile's endpoints is used to calculate the overall health of the child profile. This health information is propagated up the nested profile hierarchy. The parent profile uses this aggregated health to determine whether to direct traffic to the child profile. See the [FAQ](#faq) section of this article for full details on health monitoring of nested profiles.

Returning to the previous example, suppose the production deployment in China North fails. By default, the 'child' profile directs all traffic to the test deployment. If the test deployment also fails, the parent profile determines that the child profile should not receive traffic since all child endpoints are unhealthy. Then, the parent profile distributes traffic to the other regions.

![Nested Profile failover (default behavior)][3]

You might be happy with this arrangement. Or you might be concerned that all traffic for China East site 1 is now going to the test deployment instead of a limited subset traffic. Regardless of the health of the test deployment, you want to fail over to the other regions when the production deployment in China East site 1 fails. To enable this failover, you can specify the 'MinChildEndpoints' parameter when configuring the child profile as an endpoint in the parent profile. The parameter determines the minimum number of available endpoints in the child profile. The default value is '1'. For this scenario, you set the MinChildEndpoints value to 2. Below this threshold, the parent profile considers the entire child profile to be unavailable and directs traffic to the other endpoints.

The following figure illustrates this configuration:

![Nested Profile failover with 'MinChildEndpoints' = 2][4]

> [!NOTE]
> The 'Priority' traffic-routing method distributes all traffic to a single endpoint. Thus there is little purpose in a MinChildEndpoints setting other than '1' for a child profile.

## Example 3: Prioritized failover regions in 'Performance' traffic routing

The default behavior for the 'Performance' traffic-routing method is designed to avoid over-loading the next nearest endpoint and causing a cascading series of failures. When an endpoint fails, all traffic that would have been directed to that endpoint is evenly distributed to the other endpoints across all regions.

!['Performance' traffic routing with default failover][5]

However, suppose you prefer the China East site 1 traffic failover to China North, and only direct traffic to other regions when both endpoints are unavailable. You can create this solution using a child profile with the 'Priority' traffic-routing method.

!['Performance' traffic routing with preferential failover][6]

Since the China East site 1 endpoint has higher priority than the China North endpoint, all traffic is sent to the China East site 1 endpoint when both endpoints are online. If China East site 1 fails, its traffic is directed to China North. With the nested profile, traffic is directed to China East only when both China East site 1 and China East site 2 North fail.

You can repeat this pattern for all regions. Replace all three endpoints in the parent profile with three child profiles, each providing a prioritized failover sequence.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a> Example 4: Controlling 'Performance' traffic routing between multiple endpoints in the same region

Suppose the 'Performance' traffic-routing method is used in a profile that has more than one endpoint in a particular region. By default, traffic directed to that region is distributed evenly across all available endpoints in that region.

!['Performance' traffic routing in-region traffic distribution (default behavior)][7]

Instead of adding multiple endpoints in China North, those endpoints are enclosed in a separate child profile. The child profile is added to the parent as the only endpoint in China North. The settings on the child profile can control the traffic distribution with China North by enabling priority-based or weighted traffic routing within that region.

!['Performance' traffic routing with custom in-region traffic distribution][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a> Example 5: Per-endpoint monitoring settings

Suppose you are using Traffic Manager to smoothly migrate traffic from a legacy on-premises web site to a new Cloud-based version hosted in Azure. For the legacy site, you want to use the home page URI to monitor site health. But for the new Cloud-based version, you are implementing a custom monitoring page (path '/monitor.aspx') that includes additional checks.

![Traffic Manager endpoint monitoring (default behavior)][9]

The monitoring settings in a Traffic Manager profile apply to all endpoints within a single profile. With nested profiles, you use a different child profile per site to define different monitoring settings.

![Traffic Manager endpoint monitoring with per-endpoint settings][10]

## Next steps

Learn more about [how Traffic Manager works](traffic-manager-how-traffic-manager-works.md)

Learn how to [create a Traffic Manager profile](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png