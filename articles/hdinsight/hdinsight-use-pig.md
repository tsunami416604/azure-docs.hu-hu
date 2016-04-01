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

[Apache Pig](http://pig.apache.org/) 是透過所謂的程序性語言建立 Hadoop 程式的平台 *Pig Latin*。 Pig 是 Java 的替代方式來建立 *MapReduce* 解決方案，而且這是包含在 Azure HDInsight。

在本文中，您將了解如何搭配 HDInsight 使用 Pig。

##<a id="why"></a>為何要使用 Pig？

在 Hadoop 中使用 MapReduce 處理資料的其中一項挑戰是，只藉由使用 map 和 reduce 函數實作處理邏輯。 如果是複雜的處理，您經常必須將處理分成多個鏈結在一起的 MapReduce 作業，才能獲得想要的結果。

Pig 不會強迫您只使用 map 和 reduce 函數，反而會允許您將處理定義為一系列轉換，使資料流過以產生所需的輸出。

Pig Latin 語言可讓您從原始輸入描述資料流 (經過一或多個轉換後) 以產生所需的輸出。 Pig Latin 程式遵循此一般模式：

- **負載**︰ 讀取處理從檔案系統的資料
- **轉換**︰ 操作資料
- **傾印或儲存**︰ 資料輸出至螢幕或儲存以供處理

Pig Latin 也支援使用者定義函數 (UDF)，此函數讓您可用叫用外部元件，這些元件會實作很難以 Pig Latin 模型化的邏輯。

如需 Pig Latin 的詳細資訊，請參閱 [Pig Latin 參考手冊 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) 和 [Pig Latin 參考手冊 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html)。

如需搭配 Pig 使用 UDF 的範例，請參閱以下文件：

* [使用 HDInsight 中 Pig DataFu](hdinsight-hadoop-use-pig-datafu-udf.md) -DataFu 是有用的 Udf Apache 所維護的集合

* [在 HDInsight 中使用 Python 搭配 Pig 和 Hive](hdinsight-python.md)

* [搭配使用 C# 與 HDInsight 中的 Hive 和 Pig](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>關於範例資料

這個範例會使用 *log4j* 範例檔案，其儲存在 **/example/data/sample.log** blob 儲存體容器中。 檔案中的每一筆記錄均由一列欄位組成，包括以 `[LOG LEVEL]` 欄位來顯示類型和嚴重性，例如：

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在上一個範例中，記錄層級是「錯誤」。

> [AZURE.NOTE] 您可以使用產生的 log4j 檔案 [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) 記錄工具，然後將該檔案上傳至 blob。 請參閱 [資料上傳至 HDInsight](hdinsight-upload-data.md) 如需相關指示。 如需有關如何搭配 HDInsight 使用 Azure 儲存體中的 blob 的詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。

範例資料會儲存在 Azure Blob 儲存體中，供 HDInsight 做為 Hadoop 叢集的預設檔案系統使用。 HDInsight 可以存取檔案儲存在 blob 中使用 **wasb** 前置詞。 例如，若要存取 sample.log 檔案，您應使用下列語法：

    wasb:///example/data/sample.log

由於 WASB 是 HDInsight 的預設儲存體，您也可以存取該檔案使用 **/example/data/sample.log** 從 Pig Latin。

> [AZURE.NOTE] 語法， **wasb: / /**, ，用來存取儲存在您的 HDInsight 叢集的預設儲存體容器中的檔案。 如果您在佈建叢集，而且您想要存取這些帳戶中儲存檔案時，您可以指定其他儲存體帳戶，您可以藉由指定容器名稱和儲存體帳戶位址，例如存取資料 ︰ **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**。


##<a id="job"></a>關於範例工作

以下 Pig Latin 工作載入 **sample.log** HDInsight 叢集預設儲存體檔案。 然後該工作會執行一系列轉換，進而產生輸入資料中每個記錄層級出現次數的計數。 這些結果會傾印至 STDOUT。

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

下圖詳細說明了每個轉換對資料執行的動作。

![轉換的圖形化表示][image-hdi-pig-data-transformation]

##<a id="run"></a>執行 Pig Latin 工作

HDInsight 可以使用各種方法執行 Pig Latin 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| **使用此** 如果您想要...                                   | ...一個 **互動式** 殼層 | ...**批次** 處理 | ...搭配此 **叢集作業系統** | ...從此這 **用戶端作業系統** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Linux、Unix、Mac OS X 或 Windows        |
| [Curl](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux 或 Windows                          | Linux、Unix、Mac OS X 或 Windows        |
| [.NET SDK for Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows (目前)                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows                                  |
| [遠端桌面](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## 使用內部部署 SQL Server Integration Services 在 Azure HDInsight 叢集執行 Pig 工作

您也可以使用 SQL Server Integration Services (SSIS) 來執行 Pig 工作。 適用於 SSIS 的 Azure Feature Pack 中提供下列元件可搭配 HDInsight 上的 Pig 工作使用。


- [Azure HDInsight Pig 工作][pigtask]
- [Azure 訂用帳戶連接管理員][connectionmanager]


深入了解 Azure Feature Pack ssis [這裡][ssispack]。


##<a id="nextsteps"></a>接續步驟

現在您已學會如何搭配 HDInsight 使用 Pig，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [將資料上傳到 HDInsight][hdinsight-upload-data]
* [搭配 HDInsight 使用 Hive][hdinsight-use-hive]
* [搭配 HDInsight 使用 MapReduce 工作][hdinsight-use-mapreduce]

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

[Powershell-install-configure]: ../install-configure-powershell.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png


