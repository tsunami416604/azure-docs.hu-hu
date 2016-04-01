<properties 
    pageTitle="使用 Hive 和 Hadoop 進行網站記錄分析 | Microsoft Azure" 
    description="了解如何使用 HDInsight 上的 Hive 來分析網站記錄。 您將使用記錄檔做為 HDInsight 資料表的輸入，然後使用 HiveQL 來查詢資料。" 
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
    ms.date="11/29/2015" 
    ms.author="nitinme"/>

# 使用 HDInsight 上的 Hive 分析網站的記錄

了解如何使用 HDInsight 上的 HiveQL 來分析網站的記錄。 網站記錄分析可用於根據類似活動來區隔對象、依人口統計將網站造訪者分類，以及找出他們檢視的內容、他們來自的網站等。

在此範例中，您將使用 HDInsight 叢集來分析網站記錄檔，以深入了解一天內從外部網站的網站造訪頻率。 也將產生使用者遇到的網站錯誤摘要。 您將了解如何：

- 連接至包含網站記錄檔的 Azure Blob 儲存體。
- 建立 Hive 資料表以查詢這些記錄。
- 建立 Hive 查詢以分析資料。
- 使用 Microsoft Excel 連接到 HDInsight (使用開放式資料庫連接，ODBC) 擷取已分析的資料。

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##必要條件

- 您必須已在 Azure HDInsight 上佈建 Hadoop 叢集。 如需指示，請參閱 [佈建 HDInsight 叢集][hdinsight-provision]。 
- 您必須已安裝 Microsoft Excel 2013 或 Excel 2010。
- 您必須擁有 [Microsoft Hive ODBC 驅動程式](http://www.microsoft.com/download/details.aspx?id=40886) 從 Hive 將資料匯入到 Excel。


##執行範例

1. 從 [Azure 入口網站](https://ms.portal.azure.com/), ，從 [開始面板] （如果您釘選叢集中那里），按一下您要執行範例的叢集] 磚。

2. [叢集] 分頁中，從下 **快速連結**, ，按一下 [ **叢集儀表板**, ，然後從 **叢集儀表板** 刀鋒視窗中，按一下 [ **HDInsight 叢集儀表板**。 或者，您也可以使用下列 URL 直接開啟儀表板：

        https://<clustername>.azurehdinsight.net
    
    出現提示時，使用您佈建叢集時所用的系統管理員使用者名稱和密碼來進行驗證。
  
2. 從開啟的網頁中，按一下 [ **Getting Started Gallery** ] 索引標籤，然後在 **範例** 類別中，按一下 [ **網站記錄分析** 範例。

3. 依照網頁上提供的指示完成範例。

##後續步驟
嘗試下列範例 ︰ [分析感應器資料搭配使用 Hive 與 HDInsight](hdinsight-hive-analyze-sensor-data.md)。


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 

