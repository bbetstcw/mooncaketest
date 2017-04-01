---
title: Azure Storage options for R Server on HDInsight | Azure
description: Learn about the different storage options available to users with R Server on HDInsight
services: HDInsight
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
wacn.date: ''
ms.author: jeffstok

---
# Azure Storage options for R Server on HDInsight
Microsoft R Server on HDInsight has access to both Azure Blob, as a means of persisting data, code, result objects from analysis, and so on.

When you create a Hadoop cluster in HDInsight, you specify either an Azure Storage account. A specific storage container from that account holds the file system for the cluster you create (for example, the Hadoop Distributed File System). For performance purposes, the HDInsight cluster is created in the same data center as the primary storage account that you specify. For more information, see [Use Azure Blob storage with HDInsight](hdinsight-hadoop-use-blob-storage.md "Use Azure Blob storage with HDInsight").   

## Use multiple Azure Blob storage accounts
If necessary, you can access multiple Azure storage accounts or containers with your HDI cluster. To do so, you need to specify the additional storage accounts in the UI when you create the cluster, and then follow these steps to use them in R.  

1. Create an HDInsight cluster with a storage account name of **storage1** and a default container called **container1**.
2. Specify an additional storage account called **storage2**.  
3. Copy the mycsv.csv file to the /share directory, and perform analysis on that file.  

        hadoop fs -mkdir /share
        hadoop fs -copyFromLocal myscsv.scv /share  

4. In R code, set the name node to **default,** and set your directory and file to process.  

        myNameNode <- "default"
        myPort <- 0

    Location of the data:  

        bigDataDirRoot <- "/share"  

    Define Spark compute context:

        mySparkCluster <- RxSpark(consoleOutput=TRUE)

    Set compute context:

        rxSetComputeContext(mySparkCluster)

    Define the Hadoop Distributed File System (HDFS) file system:

        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    Specify the input file to analyze in HDFS:

        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

All of the directory and file references point to the storage account wasbs://container1@storage1.blob.core.chinacloudapi.cn. This is the **default storage account** that's associated with the HDInsight cluster.

Now, suppose you want to process a file called mySpecial.csv that's located in the  /private directory of **container2** in **storage2**.

In your R code, point the name node reference to the **storage2** storage account.

````
myNameNode <- "wasbs://container2@storage2.blob.core.chinacloudapi.cn"
myPort <- 0
````

Location of the data:

````
bigDataDirRoot <- "/private"
````

Define Spark compute context:

````
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)
````

Set compute context:

````
rxSetComputeContext(mySparkCluster)
````

Define HDFS file system:

````
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)
````

Specify the input file to analyze in HDFS:

````
inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
````

All of the directory and file references now point to the storage account wasbs://container2@storage2.blob.core.chinacloudapi.cn. This is the **Name Node** that you've specified.

Note that you will have to configure the /user/RevoShare/<SSH username> directory on **storage2** as follows:

````
hadoop fs -mkdir wasbs://container2@storage2.blob.core.chinacloudapi.cn/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.chinacloudapi.cn/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.chinacloudapi.cn/user/RevoShare/<RDP username>
````

## Use Azure Files on the edge node
There is also a convenient data storage option for use on the edge node called [Azure Files](../storage/storage-how-to-use-files-linux.md "Azure Files"). It enables you to mount an Azure Storage file share to the Linux file system. This can be handy for storing data files, R scripts, and result objects that might be needed later when it makes sense to use the native file system on the edge node rather than HDFS.

A major benefit of Azure Files is that the file shares can be mounted and used by any system that has a supported OS such as Windows or Linux. For example, it can be used by another HDInsight cluster that you or someone on your team has, by an Azure VM, or even by an on-premises system.

## Next steps
Now that you understand the Azure storage options, use the following links to discover other ways of working with R Server on HDInsight.

* [Overview of R Server on HDInsight](hdinsight-hadoop-r-server-overview.md)
* [Get started with R server on Hadoop](hdinsight-hadoop-r-server-get-started.md)
* [Add RStudio Server to HDInsight (if not added during cluster creation)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Compute context options for R Server on HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)