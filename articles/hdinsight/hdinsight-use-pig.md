<properties
   pageTitle="在 HDInsight 中使用 Hadoop Pig | Microsoft Azure"
   description="了解如何在 HDInsight 上搭配 Hadoop 使用 Pig。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/06/2015"
   ms.author="larryfr"/>


# 搭配使用 Pig 與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

若要建立 *MapReduce* 解決方案，Pig 是 Java 的替代選項，而且也已包含在 Azure HDInsight 中。

在本文中，您將了解如何搭配 HDInsight 使用 Pig。

## 

在 Hadoop 中使用 MapReduce 處理資料的其中一項挑戰是，只藉由使用 map 和 reduce 函數實作處理邏輯。 如果是複雜的處理，您經常必須將處理分成多個鏈結在一起的 MapReduce 作業，才能獲得想要的結果。

Pig 不會強迫您只使用 map 和 reduce 函數，反而會允許您將處理定義為一系列轉換，使資料流過以產生所需的輸出。

Pig Latin 語言可讓您從原始輸入描述資料流 (經過一或多個轉換後) 以產生所需的輸出。 Pig Latin 程式遵循此一般模式：

- **載入**：從檔案系統讀取要處理的資料
- **轉換**：處理資料
- **傾印或儲存**：將資料輸出至畫面，或儲存資料以供處理

Pig Latin 也支援使用者定義函數 (UDF)，此函數讓您可用叫用外部元件，這些元件會實作很難以 Pig Latin 模型化的邏輯。



如需搭配 Pig 使用 UDF 的範例，請參閱以下文件：

* 

* 

* 

## 

此範例使用 *log4j* 範例檔案，其儲存在 Blob 儲存容器中的 **/example/data/sample.log**。

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在上一個範例中，記錄層級是「錯誤」。
> [AZURE.NOTE] 

範例資料會儲存在 Azure Blob 儲存體中，供 HDInsight 做為 Hadoop 叢集的預設檔案系統使用。 HDInsight 可使用 **wasb** 字首存取儲存在 Blob 中的檔案。 例如，若要存取 sample.log 檔案，您應使用下列語法：

    wasb:///example/data/sample.log

由於 WASB 是 HDInsight 的預設儲存體，因此您也可以從 Pig Latin 使用 **/example/data/sample.log** 存取此檔案。
> [AZURE.NOTE] 


## 

以下 Pig Latin 工作會從 HDInsight 叢集的預設儲存體載入 **sample.log** 檔案。 然後該工作會執行一系列轉換，進而產生輸入資料中每個記錄層級出現次數的計數。 這些結果會傾印至 STDOUT。

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

下圖詳細說明了每個轉換對資料執行的動作。

![轉換的圖形化表示][image-hdi-pig-data-transformation]

## 

HDInsight 可以使用各種方法執行 Pig Latin 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| 如果您想要...，請**使用此方法**| ...一個**互動式**殼層| ...**批次**處理| ...搭配此**叢集作業系統**| ...從此**用戶端作業系統**|
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| | ✔| ✔| Linux| Linux、Unix、Mac OS X 或 Windows|
| | &nbsp;| ✔| Linux 或 Windows| Linux、Unix、Mac OS X 或 Windows|
| | &nbsp;| ✔| Linux 或 Windows| Windows (目前)|
| | &nbsp;| ✔| Linux 或 Windows| Windows|
| | ✔| ✔| Windows| Windows|


## 使用內部部署 SQL Server Integration Services 在 Azure HDInsight 叢集執行 Pig 工作

您也可以使用 SQL Server Integration Services (SSIS) 來執行 Pig 工作。 適用於 SSIS 的 Azure Feature Pack 中提供下列元件可搭配 HDInsight 上的 Pig 工作使用。


- 
- 





## 

現在您已學會如何搭配 HDInsight 使用 Pig，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* 
* 
* 


[check]: ./media/hdinsight-use-pig/hdi.checkmark.png 
[apachepig-home]: http://pig.apache.org/ 
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html 
[curl]: http://curl.haxx.se/ 
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx 
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx 
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx 
[hdinsight-storage]: ../hdinsight-use-blob-storage.md 
[hdinsight-upload-data]: hdinsight-upload-data.md 
[hdinsight-get-started]: ../hdinsight-get-started.md 
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md 
[hdinsight-use-hive]: hdinsight-use-hive.md 
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md 
[hdinsight-provision]: hdinsight-provision-clusters.md 
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk 
[powershell-install-configure]: ../install-configure-powershell.md 
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx 
[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png 
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif 
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png 
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png 

