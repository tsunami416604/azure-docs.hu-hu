<properties
    pageTitle="在 HDInsight 中使用 R 來自訂叢集| Microsoft Azure"
    description="了解如何使用指令碼動作安裝 R，以及在 HDInsight 叢集上使用 R。"
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
    ms.date="10/02/2015"
    ms.author="jgao"/>

# 在 HDInsight Hadoop 叢集上安裝和使用 R


了解如何使用指令碼動作來以 R 自訂以 Windows 為基礎的 HDInsight 叢集，以及如何在 HDInsight 叢集上使用 R。 如需使用 R 與以 Linux 為基礎的叢集，請參閱 [安裝和使用 R 在 HDinsight Hadoop 叢集 (Linux)](hdinsight-hadoop-r-scripts-linux.md)。
 
您也可以使用任何類型的 Azure HDInsight (Hadoop、 Storm、 HBase、 Spark) 叢集上安裝 R *指令碼動作*。 在 HDInsight 叢集上安裝 R 的範例指令碼是可以從唯讀的 Azure 儲存體 blob 位於 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)。 

**相關文章**

- [在 HDInsight Hadoop 叢集上安裝和使用 R (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md)︰ 建立 HDInsight 叢集的一般資訊
- [自訂 HDInsight 叢集使用指令碼動作][hdinsight-cluster-customize]︰ 自訂 HDInsight 叢集使用指令碼動作的一般資訊
- [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions.md)

## 什麼是 R？

<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a> 是一個用於統計計算的開放原始碼語言和環境。 R 提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。 它也提供廣泛的圖形功能。 R 是廣泛各種不同領域中，大多數專業統計人員和科學家慣用的程式設計環境。

R 與 Azure Blob 儲存體 (WASB) 相容，因此便可在 HDInsight 上使用 R 來處理儲存在該處的資料。  

## 安裝 R

A [的範例指令碼](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) HDInsight 上安裝 R 叢集是可以從 Azure 儲存體中的唯讀 blob。 本節提供有關如何在使用 Azure 入口網站建立叢集時使用範例指令碼的指示。

> [AZURE.NOTE] 搭配 HDInsight 叢集 3.1 版引進了範例指令碼。 如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](../hdinsight-component-versioning/)。

1. 當您從入口網站建立 HDInsight 叢集時，按一下 [ **選擇性組態**, ，然後按一下 [ **指令碼動作**。
2. 在 **指令碼動作** 頁面上，輸入下列值 ︰

    ![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Use Script Action to customize a cluster")

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作名稱，例如 <b>安裝 R</b>。</td></tr>
        <tr><td>指令碼 URI</td>
            <td>指定為了自訂叢集，比方說，所叫用的指令碼的 URI <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>節點類型</td>
            <td>指定執行自訂指令碼的節點。 您可以選擇 <b>所有節點</b>, ，<b>僅限前端節點</b>, ，或 <b>背景工作節點</b> 只。
        <tr><td>參數</td>
            <td>如果指令碼要求，請指定參數。 不過，用來安裝 R 的指令碼不需要任何參數，因此可以讓此處空白。</td></tr>
    </table>

    您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。 加入指令碼之後，請按一下核取記號以開始建立叢集。

您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，在 HDInsight 上使用指令碼安裝 R。 本文稍後會提供這些程序的指示。

## 執行 R 指令碼
本節說明如何使用 HDInsight 在 Hadoop 叢集上執行 R 指令碼。

1. **建立遠端桌面連線到叢集**︰ 從入口網站中，為您建立已安裝，的叢集啟用遠端桌面，然後連接到叢集。 如需指示，請參閱[使用 RDP 連線到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)。

2. **開啟 R 主控台**: R 安裝會連結放置 R 主控台在前端節點的桌面上。 按一下該連結以開啟 R 主控台。

3. **執行 R 指令碼**: R 指令碼可以直接從 R 主控台執行貼上，選取它，然後再按 ENTER。 以下是一個簡單的範例指令碼，此指令碼會產生數字 1 到 100，然後將它們乘以 2。

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

前兩行會呼叫與 R 一起安裝的 RHadoop 程式庫。最後一行會將結果列印到主控台。 輸出應該會看起來如下：

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## 使用 Azure PowerShell 安裝 R

請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)。  此範例示範如何使用 Azure PowerShell 安裝 Spark。 您需要自訂指令碼，使用 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)。

## 使用 .NET SDK 安裝 R

請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)。 此範例示範如何使用 .NET SDK 安裝 Spark。 您需要自訂指令碼，使用 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11)。


## 另請參閱

- [在 HDInsight Hadoop 叢集上安裝和使用 R (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md)︰ 建立 HDInsight 叢集的一般資訊
- [自訂 HDInsight 叢集使用指令碼動作][hdinsight-cluster-customize]︰ 自訂 HDInsight 叢集使用指令碼動作的一般資訊
- [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions.md)
- [安裝和使用 Spark HDInsight 叢集上][hdinsight-install-spark]︰ 有關安裝 Spark 的指令碼動作範例
- [HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install.md)︰ 有關安裝 Giraph 的指令碼動作範例
- [HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install-linux.md)︰ 有關安裝 Solr 的指令碼動作範例。

[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md


