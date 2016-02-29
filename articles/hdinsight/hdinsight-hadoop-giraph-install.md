<properties
    pageTitle="在 HDInsight 的 Hadoop 叢集上安裝和使用 Giraph | Microsoft Azure"
    description="了解如何使用 Giraph 自訂 HDInsight 叢集，以及如何使用 Giraph。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/03/2015"
    ms.author="nitinme"/>

# 在 HDInsight 安裝和使用使用 Giraph


了解如何使用指令碼動作來自訂以 Windows 為基礎的 HDInsight 叢集，以及如何使用 Giraph 來處理大型圖形。 如需使用 Giraph 與以 Linux 為基礎的叢集，請參閱 [HDInsight Hadoop 叢集 (Linux) 上的安裝 Giraph](hdinsight-hadoop-giraph-install-linux.md)。
 
您也可以使用任何類型的 Azure HDInsight (Hadoop、 Storm、 HBase、 Spark) 叢集上安裝 Giraph *指令碼動作*。 在 HDInsight 叢集上安裝 Giraph 的範例指令碼是可以從唯讀的 Azure 儲存體 blob 位於 [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)。 範例指令碼只能與 HDInsight 叢集版本 3.1 搭配使用。 如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。

**相關文章**

- [在 HDInsight Hadoop 叢集上安裝 Giraph (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md): 建立 HDInsight 叢集的一般資訊。
- [自訂 HDInsight 叢集使用指令碼動作][hdinsight-cluster-customize]: 自訂 HDInsight 叢集使用指令碼動作的一般資訊。
- [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions.md)。

## 什麼是 Giraph？

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> 可讓您使用 Hadoop 執行圖形處理，而且可以搭配使用 Azure HDInsight。 圖形可以模型化物件之間的關聯，例如大型網路 (像是網際網路) 上的路由器之間的連線，或社交網路上的人際關係 (有時稱為社交圖形)。 圖形處理可讓您分析圖形中物件之間的關聯，例如：

- 根據目前的人際關係找出可能的朋友。
- 識別網路中兩台電腦之間的最短路線。
- 計算網頁的頁面排名。


## 使用入口網站安裝 Giraph

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [在 HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install-v1.md)

1. 開始建立叢集使用 **自訂建立** 選項述 [建立 Hadoop 叢集的 HDInsight](hdinsight-provision-clusters.md#portal)。
2. 在 **指令碼動作** 頁面的精靈中，按一下 [ **加入指令碼動作** 提供有關指令碼動作，詳細資料，如下所示:

    ![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Use Script Action to customize a cluster")

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作的名稱。 例如， <b>安裝 Giraph</b>.</td></tr>
        <tr><td>指令碼 URI</td>
            <td>指定為自訂叢集叫用的指令碼統一資源識別項 (URI)。 例如， <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>節點類型</td>
            <td>指定執行自訂指令碼的節點。 您可以選擇 <b>所有節點</b>， <b>僅限前端節點</b>或 <b>僅限背景工作節點</b>.
        <tr><td>參數</td>
            <td>如果指令碼要求，請指定參數。 要安裝 Giraph 的指令碼不需要任何參數，因此可以讓此處空白。</td></tr>
    </table>

    您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。 加入指令碼之後，請按一下核取記號以開始建立叢集。

## 使用 Giraph

我們使用 SimpleShortestPathsComputation 範例來示範基本 <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> 實作，用於尋找圖表中物件之間最短的路徑。 請使用下列步驟來上傳範例資料及範例 jar，使用 SimpleShortestPathsComputation 範例執行工作，然後檢視結果。

1. 將範例資料檔案上傳至 Azure Blob 儲存體。 在本機工作站上，建立名為的新檔案 **tiny_graph.txt**。 應該包含下列幾行：

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    將 tiny_graph.txt 檔案上傳至 HDInsight 叢集的主要儲存體。 如需有關如何將資料上傳的指示，請參閱 [hdinsight 的資料上傳](hdinsight-upload-data.md)。

    此資料描述有向圖形，請使用格式中的物件之間的關聯性 [source\_id、 source\_value、 [[dest\_id]，[edge\_value...等]...]]。 每一行代表之間的關聯性 **source\_id** 物件和一或多個 **dest\_id** 物件。  **Edge\_value** (或權數) 可以視為強度或距離之間的連線 **source_id** 和 **dest\_id**。

    如果使用值 (或權數) 當做物件之間的距離繪製出來，上述資料可能如下圖所示：

    ![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. 執行 SimpleShortestPathsComputation 範例。 使用 tiny_graph.txt 檔案做為輸入，即可使用下列 Azure PowerShell Cmdlet 來執行此範例。 這需要您已安裝並設定 [PowerShell][powershell-install]。

        $clusterName = "clustername"
        # Giraph examples jar
        $jarFile = "wasb:///example/jars/giraph-examples.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                        "-ca", "mapred.job.tracker=headnodehost:9010",
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                        "-vip", "wasb:///example/data/tiny_graph.txt",
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                        "-op",  "wasb:///example/output/shortestpaths",
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
          -JarFile $jarFile
          -ClassName "org.apache.giraph.GiraphRunner"
          -Arguments $jobArguments

        # Run the job, write output to the Azure PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    在上述範例中，取代 **clustername** 您已安裝 Giraph 的 HDInsight 叢集的名稱。

5. 檢視結果。 當工作完成之後時，結果將會儲存在兩個輸出檔中 __wasb: / 範例/out/shotestpaths__ 資料夾。 這些檔案稱為 __一部分-m-00001__ 和 __一部分-m-00002__。 執行下列步驟來下載和檢視輸出：

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name
        $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the Storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

    這會建立 __範例/輸出/shortestpaths__ 工作站，與兩個輸出檔案下載至該位置上目前的目錄中的目錄結構。

    使用 __Cat__ cmdlet 顯示檔案的內容:

        Cat example/output/shortestpaths/part*

    輸出應該類似下列所示：


        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    The SimpleShortestPathComputation example is hard coded to start with object ID 1 and find the shortest path to other objects. So the output should be read as `destination_id distance`, where distance is the value (or weight) of the edges traveled between object ID 1 and the target ID.

    Visualizing this, you can verify the results by traveling the shortest paths between ID 1 and all other objects. Note that the shortest path between ID 1 and ID 4 is 5. This is the total distance between <span style="color:orange">ID 1 and 3</span>, and then <span style="color:red">ID 3 and 4</span>.

    ![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## 使用 Aure PowerShell 安裝 Giraph

請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)。  此範例示範如何使用 Azure PowerShell 安裝 Spark。 您需要自訂指令碼，使用 [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)。

## 使用 .NET SDK 安裝 Giraph

請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)。 此範例示範如何使用 .NET SDK 安裝 Spark。 您需要自訂指令碼，使用 [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)。


## 另請參閱

- [在 HDInsight Hadoop 叢集上安裝 Giraph (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md): 建立 HDInsight 叢集的一般資訊。
- [自訂 HDInsight 叢集使用指令碼動作][hdinsight-cluster-customize]: 自訂 HDInsight 叢集使用指令碼動作的一般資訊。
- [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions.md)。
- [安裝和使用 Spark HDInsight 叢集上][hdinsight-install-spark]: 有關安裝 Spark 的指令碼動作範例。
- [HDInsight 叢集上安裝 R][hdinsight-install-r]: 指令碼動作範例需安裝。
- [HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install.md): 有關安裝 Solr 的指令碼動作範例。



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

