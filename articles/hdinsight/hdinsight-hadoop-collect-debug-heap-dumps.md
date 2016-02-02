<properties
    pageTitle="利用堆積傾印偵錯和分析與 Hadoop 服務 |Microsoft Azure"
    description="自動為 Hadoop 服務收集堆積傾印，並放在 Azure Blob 儲存體帳戶內以供偵錯和分析。"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/12/2015"
    ms.author="jgao"/>



# 收集 Blob 儲存體中的堆積傾印以偵錯和分析 Hadoop 服務

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

堆積傾印含有應用程式的記憶體，包括變數值的快照集 
在已建立傾印。 所以也非常有助於診斷發生的問題 
在執行階段。 堆積傾印可以自動為 Hadoop 服務收集並放置於 
Azure Blob 儲存體帳戶的使用者在 HDInsightHeapDumps /。

必須啟用個別服務的各種服務的堆積傾印集合 
叢集。 叢集的這項功能預設為關閉。 可以是這些堆積傾印 
大，因此最好也能監視 Blob 儲存體帳戶會被儲存 
一旦啟用的集合。
> [AZURE.NOTE] 本文的資訊僅適用於以 Windows 為基礎的 HDInsight。 
在以 Linux 為基礎的 HDInsight 上的資訊，請參閱 [啟用堆積傾印 Hadoop 服務 
以 Linux 為基礎的 HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## 堆積傾印的合格服務

您可以啟用下列服務的堆積傾印：

*  **hcatalog** - tempelton
*  **hive** - hiveserver2、metastore、derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager、nodemanager、timelineserver
*  **hdfs** - datanode、secondarynamenode、namenode

## 可啟用堆積傾印的組態元素

若要開啟服務的堆積傾印，您必須設定適當的組態項目 
在該服務的區段中，指定由 **service_name**。

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

值 **service_name** 可以是任何前面列出的服務: 
tempelton、 hiveserver2、 metastore、 derbyserver、 jobhistoryserver、 resourcemanager、 nodemanager、 timelineserver、 datanode、 secondarynamenode、 或 namenode。

## 使用 Azure PowerShell 啟用

例如，若要使用 Azure PowerShell 來開啟 jobhistoryserver 的堆積傾印，您必須執行下列作業：

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
    
    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## 使用 .NET SDK 啟用

例如，若要使用 Azure HDInsight .NET SDK 來開啟 jobhistoryserver 的堆積傾印，您必須執行下列作業：

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));
    
    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));



