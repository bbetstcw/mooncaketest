---
title: Use the Hive shell in HDInsight (Hadoop) | Azure
description: Learn how to use the Hive shell with a Linux-based HDInsight cluster. You will learn how to connect to the HDInsight cluster using SSh, then use the Hive Shell to interactively run queries.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.assetid: 0e919171-03e6-4f5a-ab4e-3eec3e34c347
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
wacn.date: ''
ms.author: larryfr

---
# Use Hive with Hadoop in HDInsight with SSH
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In this article, you will learn how to use Secure Shell (SSH) to connect to a Hadoop on Azure HDInsight cluster and then interactively submit Hive queries by using the Hive command-line interface (CLI).

> [!IMPORTANT]
> While the Hive command is available on HDInsight clusters, you should consider using Beeline. Beeline is a newer client for working with Hive, and is included with your HDInsight cluster. For more information on using this, see [Use Hive with Hadoop in HDInsight with Beeline](hdinsight-hadoop-use-hive-beeline.md).

## <a id="prereq"></a>Prerequisites
To complete the steps in this article, you will need the following:

* A Linux-based Hadoop on HDInsight cluster.

    > [!IMPORTANT]
    > Linux is the only operating system used on HDInsight version 3.4 or greater. For more information, see [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* An SSH client. Linux, Unix, and Mac OS should come with an SSH client. Windows users must download a client, such as [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a id="ssh"></a>Connect with SSH
Connect to the fully qualified domain name (FQDN) of your HDInsight cluster by using the SSH command. The FQDN will be the name you gave the cluster, then **.azurehdinsight.cn**. For example, the following would connect to a cluster named **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.cn

**If you provided a certificate key for SSH authentication** when you created the HDInsight cluster, you may need to specify the location of the private key on your client system:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.cn

**If you provided a password for SSH authentication** when you created the HDInsight cluster, you will need to provide the password when prompted.

For more information on using SSH with HDInsight, see [Use SSH with Linux-based Hadoop on HDInsight from Linux, OS X, and Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

### PuTTY (Windows-based clients)
Windows does not provide a built-in SSH client. We recommend using **PuTTY**, which can be downloaded from [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

For more information on using PuTTY, see [Use SSH with Linux-based Hadoop on HDInsight from Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="hive"></a>Use the Hive command
1. Once connected, start the Hive CLI by using the following command:

        hive
2. Using the CLI, enter the following statements to create a new table named **log4jLogs** by using the sample data:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    These statements perform the following actions:

    * **DROP TABLE** - Deletes the table and the data file, in case the table already exists.
    * **CREATE EXTERNAL TABLE** - Creates a new 'external' table in Hive. External tables only store the table definition in Hive. The data is left in the original location.
    * **ROW FORMAT** - Tells Hive how the data is formatted. In this case, the fields in each log are separated by a space.
    * **STORED AS TEXTFILE LOCATION** - Tells Hive where the data is stored (the example/data directory), and that it is stored as text.
    * **SELECT** - Selects a count of all rows where column **t4** contains the value **[ERROR]**. This should return a value of **3** as there are three rows that contain this value.
    * **INPUT__FILE__NAME LIKE '%.log'** - Tells Hive that we should only return data from files ending in .log. This restricts the search to the sample.log file that contains the data, and keeps it from returning data from other example data files that do not match the schema we defined.

        > [!NOTE]
        > External tables should be used when you expect the underlying data to be updated by an external source, such as an automated data upload process, or by another MapReduce operation, but always want Hive queries to use the latest data.
        > <p>  
        > Dropping an external table does **not** delete the data, only the table definition.
        > 
        > 
3. Use the following statements to create a new 'internal' table named **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    These statements perform the following actions:

    * **CREATE TABLE IF NOT EXISTS** - Creates a table, if it does not already exist. Since the **EXTERNAL** keyword is not used, this is an internal table, which is stored in the Hive data warehouse and is managed completely by Hive.
    * **STORED AS ORC** - Stores the data in Optimized Row Columnar (ORC) format. This is a highly optimized and efficient format for storing Hive data.
    * **INSERT OVERWRITE ... SELECT** - Selects rows from the **log4jLogs** table that contain **[ERROR]**, then inserts the data into the **errorLogs** table.

        To verify that only rows containing **[ERROR]** in column t4 were stored to the **errorLogs** table, use the following statement to return all the rows from **errorLogs**:

            SELECT * from errorLogs;

        Three rows of data should be returned, all containing **[ERROR]** in column t4.

        > [!NOTE]
        > Unlike external tables, dropping an internal table will delete the underlying data as well.
        > 
        > 

## <a id="summary"></a>Summary
As you can see, the Hive command provides an easy way to interactively run Hive queries on an HDInsight cluster, monitor the job status, and retrieve the output.

## <a id="nextsteps"></a>Next steps
For general information on Hive in HDInsight:

* [Use Hive with Hadoop on HDInsight](hdinsight-use-hive.md)

For information on other ways you can work with Hadoop on HDInsight:

* [Use Pig with Hadoop on HDInsight](hdinsight-use-pig.md)
* [Use MapReduce with Hadoop on HDInsight](hdinsight-use-mapreduce.md)

If you are using Tez with Hive, see the following documents for debugging information:

* [Use the Tez UI on Windows-based HDInsight](hdinsight-debug-tez-ui.md)
* [Use the Ambari Tez view on Linux-based HDInsight](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/zh-cn/library/dn479185.aspx

[azure-purchase-options]: https://www.azure.cn/pricing/overview/
[azure-member-offers]: https://www.azure.cn/pricing/member-offers/
[azure-trial]: https://www.azure.cn/pricing/1rmb-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://zh.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: /azure/hdinsight-connect-excel-power-query/

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/zh-cn/library/ee692792.aspx

[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png