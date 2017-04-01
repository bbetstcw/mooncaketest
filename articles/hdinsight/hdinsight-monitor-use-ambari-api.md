---
title: Monitor Hadoop clusters in HDInsight using the Ambari API | Azure
description: Use the Apache Ambari APIs for creating, managing, and monitoring Hadoop clusters. Intuitive operator tools and APIs hide the complexity of Hadoop.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard

ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
wacn.date: ''
ms.author: jgao

---
# Monitor Hadoop clusters in HDInsight using the Ambari API
Learn how to monitor HDInsight clusters by using Ambari APIs.

> [!NOTE]
> The information in this article is primarily for Windows-based HDInsight clusters, which provide a read-only version of the Ambari REST API. For Linux-based clusters, see [Manage Hadoop clusters using Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## What is Ambari?
[Apache Ambari][ambari-home] is used for provisioning, managing, and monitoring Apache Hadoop clusters. It includes an intuitive collection of operator tools and a robust set of APIs that hide the complexity of Hadoop, simplifying the operation of clusters. For more information about the APIs, see [Ambari API Reference][ambari-api-reference]. 

HDInsight currently supports only the Ambari monitoring feature. Ambari API 1.0 is supported by HDInsight version 3.0 and 2.1 clusters. This article covers accessing Ambari APIs on HDInsight version 3.1 and 2.1 clusters. The key difference between the two is that some of the components have changed with the introduction of new capabilities (such as the Job History Server). 

**Prerequisites**

Before you begin this tutorial, you must have the following items:

* **A workstation with Azure PowerShell**.
* (Optional) [cURL][curl]. To install it, see [cURL Releases and Downloads][curl-download].

    > [!NOTE]
    > When use the cURL command in Windows, use double-quotation marks instead of single-quotation marks for the option values.
    > 
    > 
* **An Azure HDInsight cluster**. For instructions about cluster provisioning, see [Get started using HDInsight][hdinsight-get-started] or [Provision HDInsight clusters][hdinsight-provision]. You need the following data to go through the tutorial:

    | Cluster property | Azure PowerShell variable name | Value | Description |
    | --- | --- | --- | --- |
    |   HDInsight cluster name |$clusterName | |The name of your HDInsight cluster. |
    |   Cluster username |$clusterUsername | |Cluster user name specified when the cluster was created. |
    |   Cluster password |$clusterPassword | |Cluster user password. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## Jump-start
There are several ways to use Ambari to monitor HDInsight clusters.

**Use Azure PowerShell**

The following Azure PowerShell script gets the MapReduce job tracker information *in an HDInsight 3.1 cluster.*  The key difference is that we pull these details from the YARN service (rather than MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.cn:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.cn/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

The following PowerShell script gets the MapReduce job tracker information *in an HDInsight 2.1 cluster*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.cn:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.cn/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

The output is:

![Jobtracker Output][img-jobtracker-output]

**Use cURL**

The following example gets cluster information by using cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.cn:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.cn

The output is:

    {"href":"https://hdi0211v2.azurehdinsight.cn/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.cn/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.cn","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.cn/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.cn/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.cn","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.cn/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.cn/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.cn","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.cn/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.cn/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.cn",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.cn/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.cn/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.cn",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.cn"}}]}

**For the 10/8/2014 release**:

When using the Ambari endpoint, "https://{clusterDns}.azurehdinsight.cn/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.cn/services/{servicename}/components/{componentname}", the *host_name* field returns the fully qualified domain name (FQDN) of the node instead of the host name. Before the 10/8/2014 release, this example returned simply "**headnode0**". After the 10/8/2014 release, you get the FQDN "**headnode0.{ClusterDNS}.azurehdinsight.cn**", as shown in the previous example. This change was required to facilitate scenarios where multiple cluster types (such as HBase and Hadoop) can be deployed in one virtual network (VNET). This happens, for example, when using HBase as a back-end platform for Hadoop.

## Ambari monitoring APIs
The following table lists some of the most common Ambari monitoring API calls. For more information about the API, see [Ambari API Reference][ambari-api-reference].

| Monitor API call | URI | Description |
| --- | --- | --- |
| Get clusters |`/api/v1/clusters` | |
| Get cluster info. |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn` |clusters, services, hosts |
| Get services |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/services` |Services include: hdfs, mapreduce |
| Get services info. |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/services/<ServiceName>` | |
| Get service components |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Get component info. |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, host-components, metrics |
| Get hosts |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/hosts` |headnode0, workernode0 |
| Get host info. |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/hosts/<HostName>` | |
| Get host components |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Get host component info. |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, component, host, metrics |
| Get configurations |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/configurations` |Config types: core-site, hdfs-site, mapred-site, hive-site |
| Get configuration info. |`/api/v1/clusters/<ClusterName>.azurehdinsight.cn/configurations?type=<ConfigType>&tag=<VersionName>` |Config types: core-site, hdfs-site, mapred-site, hive-site |

## Next Steps
Now you have learned how to use Ambari monitoring API calls. To learn more, see:

* [Manage HDInsight clusters using the Azure portal preview][hdinsight-admin-portal]
* [Manage HDInsight clusters using Azure PowerShell][hdinsight-admin-powershell]
* [Manage HDInsight clusters using command-line interface][hdinsight-admin-cli]
* [HDInsight documentation][hdinsight-documentation]
* [Get started with HDInsight][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: https://docs.microsoft.com/powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/zh-cn/library/dn425048.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /azure/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png