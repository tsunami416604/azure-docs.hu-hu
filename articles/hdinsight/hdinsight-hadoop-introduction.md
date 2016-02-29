<properties
    pageTitle="雲端中的 Hadoop 是什麼？ HDInsight 簡介 |Microsoft Azure"
    description="雲端中的 Hadoop 是什麼？HDInsight 如何管理 Hadoop？ 簡介 Hadoop 元件與巨量資料分析。"
    keywords="big data analysis,introduction to hadoop,what is hadoop,hadoop in the cloud"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/02/2015"
   ms.author="cgronlun"/>


# 雲端中的 Hadoop 是什麼？ 新引進 HDInsight 的 Hadoop 元件可用於執行巨量資料分析

取得 Hadoop、其生態系統以及 Azure HDInsight 中的巨量資料的簡介：HDInsight 中的 Hadoop 是什麼、Hadoop 元件有哪些、常見術語及巨量資料分析的案例？ 此外也能進一步了解如何在 HDInsight 中使用雲端 Hadoop 的 Hadoop 教學課程、文件及資源。

## HDInsight 中的雲端 Hadoop 是什麼?

Azure HDInsight 在雲端部署和佈建受管理的 Apache Hadoop 叢集，藉此提供軟體架構來管理、分析和報告具高可靠性和可用性的巨量資料。 HDInsight 會使用 **Hortonworks Data Platform (HDP)** Hadoop 散發。 Hadoop 通常是指元件的整個 Hadoop 生態系統中的元件，包括 Apache HBase、Apache Spark 和 Apache Storm，以及 Hadoop 範疇內的其他技術。 請參閱 [HDInsight 上的 Hadoop 生態系統概觀](#overview) 如下如需詳細資訊。


## 什麼是巨量資料？
巨量資料是收集量快速增加、收集速度愈來愈快，並以各種非結構化格式與可變語義內容持續成長的資料。

巨量資料是指任何極為龐大的數位資訊，包括 Twitter 摘要中的文字、來自工業設備的感應器資訊，以及客戶在線上型錄瀏覽和購物的相關資訊。 巨量資料可以屬於過去 (亦即儲存的資料) 或即時 (亦即直接從來源串流而來)。

為能讓巨量資料轉化可以讓您制定出相應動作的情報或剖析資訊，您不僅須收集相關的資料，還須提出正確的問題，而這些資料還要能夠存取、清除、分析，然後以有用的形式呈現。 這就是 HDInsight 中的 Hadoop 上巨量資料的用途。


## <a name="overview"></a>HDInsight 上的 Hadoop 生態系統概觀

HDInsight 是實作在 Microsoft Azure 上的雲端服務，可快速擴充 Apache Hadoop 技術堆疊，是巨量資料分析的全方位解決方案。 其包含 Apache Spark、HBase、Storm、Pig、Hive、Sqoop、Oozie、Ambari 等實作。 HDInsight 同時結合了商業智慧 (BI) 工具，例如 Excel、SQL Server Analysis Services 和 SQL Server Reporting Services。

### Linux 上的叢集

Azure HDInsight 部署和佈建定域機組中的 Hadoop 叢集上 **Linux**。 如需詳細資訊，請參閱下表。

類別 | Linux 上的 Hadoop
---------| -------------------
**叢集作業系統** | Ubuntu 12.04 長期支援 (LTS)
**叢集類型** | Hadoop、Spark、HBase、Storm
**Deployment** | Azure 入口網站、Azure CLI、Azure PowerShell
**叢集 UI** | Ambari
**遠端存取** | 安全殼層 (SSH)、REST API、ODBC、JDBC



### Hadoop、HBase、Spark、Storm 及自訂叢集

HDInsight 提供 Apache Hadoop、 Spark、HBase 或 Storm 的叢集設定。 或者，您可以[使用指令碼動作自訂叢集](hdinsight-hadoop-customize-cluster-linux.md)。

* **Hadoop** (「 查詢 」 工作負載): 提供可靠的資料儲存 [HDFS](#HDFS), ，以及提供簡單 [MapReduce](#mapreduce) 程式設計模型，以處理和分析平行的資料。

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: 平行處理架構，可支援記憶體中處理，大幅提升巨量資料分析的應用程式的效能 (SQL)、 資料流處理資料，Spark 運作和機器學習。 請參閱 [概觀: 什麼是 HDInsight 中 Apache Spark?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** ("NoSQL"工作負載): 建置於 Hadoop 提供隨機存取功能和強大一致性，針對大量非結構化和半結構化資料-可能是數十億的資料列上的 apache NoSQL 資料庫乘以數十億的資料行。 請參閱 [HDInsight 上的 HBase 概觀](hdinsight-hbase-overview.md)。

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** ("Stream"工作負載): 分散式、 即時的運算系統，可快速處理大量資料流的資料。 Storm 以受管理叢集的形式在 HDInsight 中提供。 請參閱 [分析即時感應器資料使用 Storm 和 Hadoop](hdinsight-storm-sensor-data-analysis.md)。

#### 範例自訂指令碼

指令碼動作是在叢集佈建期間執行的指令碼，可用於在叢集上安裝其他元件。 在 Linux 的叢集上，這些都是 Bash 指令碼。

下列指令碼範例由 HDInsight 小組提供：

* [色調](hdinsight-hadoop-hue-linux.md): 一組用來與叢集互動的 web 應用程式。 僅限於 Linux 叢集。

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): 圖形處理模型項目或人員之間的關聯性。

* [R](hdinsight-hadoop-r-scripts-linux.md): 開放原始碼語言和用於統計計算機器學習中使用的環境。

* [Solr](hdinsight-hadoop-solr-install-linux.md): 允許的資料進行全文檢索搜尋的企業級搜尋平台。

如需開發您自己的指令碼動作的資訊，請參閱 [使用 HDInsight 進行指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。

## Hadoop 元件與公用程式是什麼？

HDInsight 叢集包含下列元件及公用程式。

* **[Ambari](#ambari)**: 叢集佈建、 管理和監視。

* **[Avro](#avro)** (Microsoft.NET Library for Avro): Microsoft.NET 環境的資料序列化。

* **[Hive & HCatalog](#hive)**: 結構化查詢語言 (SQL) 的類似查詢，以及資料表和儲存體管理層。

* **[Mahout](#mahout)**: 機器學習。

* **[MapReduce 和 YARN](#mapreduce)**: 分散式處理和資源管理。

* **[Oozie](#oozie)**: 工作流程管理。

* **[Phoenix](#phoenix)**: 對 HBase 的關聯式資料庫層。

* **[Pig](#pig)**: 簡單撰寫指令碼的 MapReduce 轉換。

* **[Sqoop](#sqoop)**: 資料匯入和匯出。

* **[Tez](#tez)**: 讓資料密集程序有效率地大規模執行。

* **[ZooKeeper](#zookeeper)**: 協調的分散式系統中的程序。

> [AZURE.NOTE] 如需特定元件及版本資訊，請參閱 [的 HDInsight 所提供的 Hadoop 叢集版本的新功能?][component-versioning]

###<a name="ambari"></a>Ambari

Apache Ambari 可用來佈建、管理及監視 Apache Hadoop 叢集。 其中包含一組直接易懂的操作員工具和健全的 API 集，可消除 Hadoop 的複雜性，並簡化叢集作業。 以 Linux 為基礎的 HDInsight 叢集同時提供 Ambari Web UI 和 Ambari REST API，而 Windows 叢集提供 REST API 的子集。

請參閱 [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md) (僅限 Linux)、 [監視 Hadoop 叢集的 HDInsight 使用 Ambari API](hdinsight-monitor-use-ambari-api.md), ，和 <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API 參考</a>。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)

Microsoft .NET Library for Avro 針對 Microsoft .NET 環境的序列化，實作 Apache Avro 壓縮二進位資料交換格式。 它採用 <a target="_blank" href="http://www.json.org/">JavaScript 物件標記法 (JSON)</a> 來定義不限語言的結構描述，支援語言互通性，這表示以某種語言序列化的資料可供另一種語言讀取。 格式的詳細的資訊可在 < 目標 = _ 「 空白 」 href ="http://avro.apache.org/docs/current/spec.html"> Apache Avro 規格</a>。
Avro 檔案格式支援分散式 MapReduce 程式設計模型。 檔案「可分割」，這表示您可以從檔案中的任何一點開始讀取特定區塊。 若要了解作法，請參閱 [序列化資料，使用 Microsoft.NET Library for Avro](hdinsight-dotnet-avro-serialization.md)。


### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) 是分散式檔案系統，加上 MapReduce 和 YARN，成為 Hadoop 生態系統的核心。 HDFS 是 HDInsight 上的 Hadoop 叢集的標準檔案系統。

### <a name="hive"></a>Hive 和 HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> 是以 Hadoop 為基礎的資料倉儲軟體，可讓您使用一種稱為 HiveQL 的 SQL 式語言，以查詢和管理分散式儲存體中的大型資料集。 Hive 和 Pig 一樣，是以 MapReduce 為基礎的抽象。 Hive 會在執行時將查詢轉換為一系列的 MapReduce 工作。 Hive 在概念上比 Pig 更接近關聯式資料庫管理系統，因此，適用於更具結構化的資料。 對於非結構化資料，Pig 是較好的選擇。 請參閱 [搭配使用 Hive 與 HDInsight 中 Hadoop](hdinsight-use-hive.md)。

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> 為使用者提供資料的關聯式檢視中的 Hadoop 的資料表和儲存體管理層。 在 HCatalog 中，您可以讀寫可撰寫 Hive SerDe (序列化程式-還原序列化程式) 之任何格式的檔案。

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> 是 Hadoop 上執行的機器學習演算法的可擴充程式庫。 機器學習應用程式採用統計學的原理，教導系統從資料中學習，根據過去的結果來研判未來的行為。 請參閱 [Hadoop 上使用 Mahout 產生電影推薦](hdinsight-mahout.md)。

### <a name="mapreduce"></a>MapReduce 和 YARN
Hadoop MapReduce 是一套軟體架構，可讓您撰寫應用程式來平行處理巨量資料集。 MapReduce 工作可分割大型資料集，並將資料組織成機碼值組以便於處理。

Apache YARN 是新一代的 MapReduce (MapReduce 2.0，簡稱為 MRv2)，可將 JobTracker 的兩項主要工作 (資源管理和工作排程/監視) 分割成個別實體。

如需 MapReduce 的詳細資訊，請參閱 <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> Hadoop wiki。 若要深入了解 YARN，請參閱 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> 是可管理 Hadoop 工作的工作流程協調系統。 它可與 Hadoop 堆疊相整合，並支援 MapReduce、Pig、Hive 和 Sqoop 的 Hadoop 工作。 它也可用來排程系統的特定工作，例如 Java 程式或 Shell 指令碼。 請參閱 [搭配使用以時間為基礎的 Oozie 協調器與 Hadoop](hdinsight-use-oozie-coordinator-time.md)。

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> 是對 HBase 的關聯式資料庫層。 Phoenix 包含 JDBC 驅動程式，可讓使用者直接查詢和管理 SQL 資料表。 Phoenix 會將查詢和其他陳述式轉譯為原生 NoSQL API 呼叫，而不是使用 MapReduce。因此能更快速地在 NoSQL 存放區上套用。 請參閱 [使用 Phoenix 和 SQuirreL 搭配 HBase 叢集](hdinsight-hbase-phoenix-squirrel.md)。


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> 是一個高階平台，可讓您在非常大的資料集上，使用稱為 Pig Latin 的簡單指令碼語言來執行複雜的 MapReduce 轉換。 Pig 會將 Pig Latin 指令碼轉換成可在 Hadoop 內執行。 您可以建立使用者定義函數 (UDF) 來延伸 Pig Latin。 請參閱 [使用 Pig 和 Hadoop 來分析 Apache 記錄檔](hdinsight-use-pig.md)。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> 是在 Hadoop 與 SQL 之類的關聯式資料庫或其他結構化資料儲存之間盡可能以最高效率傳輸大量資料的工具。 請參閱 [搭配 Hadoop 使用 Sqoop](hdinsight-use-sqoop.md)。

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> 是以 Hadoop YARN 為建置基礎的應用程式架構，可執行一般資料處理的複雜非循環圖。 它是更有彈性且功能更強大的 MapReduce 架構後繼版本，可讓資料密集程序 (例如 Hive) 更有效率地大規模執行。 請參閱 [」 使用 Apache Tez 以提升效能 > 中使用 Hive 和 HiveQL](hdinsight-use-hive.md#usetez)。


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> 協調大型分散式系統中的程序，利用資料暫存器 (znode) 的共用階層式命名空間。 Znodes 包含協調程序所需的少量中繼資訊：狀態、位置、組態等。

## <a name="advantage"></a>Hadoop 在雲端中的優點

在 Azure 雲端生態系統內，HDInsight 中的 Hadoop 提供許多優點，包括：

* 自動佈建 Hadoop 叢集。 HDInsight 叢集比手動設定 Hadoop 叢集更容易建立。 如需詳細資訊，請參閱 [在 HDInsight 中佈建 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

* 最先進的 Hadoop 元件。 如需詳細資訊，請參閱 [的 HDInsight 所提供的 Hadoop 叢集版本的新功能?][component-versioning]。

* 叢集的高可用性和可靠性。 請參閱 [HDInsight 叢集的可用性和可靠性](hdinsight-high-availability-linux.md) 如需詳細資訊。

* 有效率又符合經濟效益的資料存放區，採用 Azure Blob 儲存體，是一個 Hadoop 相容選項。 請參閱 [HDInsight 中的 Hadoop 搭配使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md) 如需詳細資訊。

* 與其他 Azure 服務整合，包括 [Web 應用程式](../documentation/services/app-service/web/) 和 [SQL Database](../documentation/services/sql-database/)。

* 進入成本低。 啟動 [免費試用版](/pricing/free-trial/), ，或請參閱 < [HDInsight 定價詳細資料](/pricing/details/hdinsight/)。


若要進一步了解 HDInsight 中的 Hadoop 優點，請參閱  [適用於 HDInsight 的 Azure 功能頁面][marketing-page]。



## <a id="resources"></a>深入了解巨量資料分析、 Hadoop 和 HDInsight 的資源

得利於將 Hadoop 引進雲端中，以致能夠分析下列資源的巨量資料。


### HDInsight 的 Hadoop 文件

* [HDInsight 文件](http://azure.microsoft.com/documentation/services/hdinsight/): Azure HDInsight 的文件頁面的文章、 視訊和其他資源的連結。

* [開始使用 HDInsight 在 Linux 上](hdinsight-hadoop-linux-tutorial-get-started.md): 佈建在 Linux 上的 HDInsight Hadoop 叢集和執行範例 Hive 查詢的快速入門教學課程。

* [開始使用以 Linux 為基礎的 Storm on HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md): 佈建 Storm on HDInsight 叢集和執行範例 Storm 拓撲的快速入門教學課程。

* [佈建 Linux 上的 HDInsight](hdinsight-hadoop-provision-linux-clusters.md): 了解如何佈建 HDInsight Hadoop 叢集，透過 Azure 入口網站、 Azure CLI 或 Azure PowerShell 在 Linux 上的。

* [在 Linux 上使用 HDInsight](hdinsight-hadoop-linux-information.md): 取得一些快速提示工作之 Hadoop Linux 叢集在 Azure 上佈建。

* [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md): 了解如何監視和管理以 Linux 為基礎的 Hadoop HDInsight 叢集上使用 Ambari Web 或 Ambari REST API。


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: 進一步了解 Apache Hadoop 軟體程式庫，可讓電腦叢集大型資料集分散式處理的架構。

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: 進一步了解 Hadoop 分散式檔案系統中，使用 Hadoop 應用程式的主要儲存系統的設計與架構。

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce 教學課程</a>: 進一步了解撰寫 Hadoop 應用程式快速處理大型運算節點叢集上大量的資料平行程式設計架構。

### Azure 上的 SQL Database

* [Azure SQL Database](/documentation/services/sql-database/): 文件、 教學課程和影片 SQL 資料庫。

* [在 Azure 入口網站上的 SQL 資料庫](sql-database-manage-portal.md): 管理 SQL 資料庫的定域機組中套輕量型且容易使用的資料庫管理工具。

* [SQL Database 的大膽](http://msftdbprodsamples.codeplex.com/releases/view/37304): SQL Database 範例資料庫的下載頁面。

### Microsoft 商業智慧 (適用於 Windows 上的 HDInsight)

熟悉的商業智慧 (BI) 工具 (例如 Excel、PowerPivot、SQL Server Analysis Services 及 SQL Server Reporting Services) 可整合使用 Power Query 增益集或 Microsoft Hive ODBC Driver 的 HDInsight 來擷取、分析和報告資料。

這些 BI 工具可協助您分析巨量資料：

* [將 Excel 連接到 Hadoop 中使用 Power Query](hdinsight-connect-excel-power-query.md): 了解如何將 Excel 連接到儲存 HDInsight 叢集相關聯，使用 Microsoft Power Query for Excel 的資料的 Azure 儲存體帳戶。

* [將 Excel 連接到 Hadoop 中使用 Microsoft Hive ODBC 驅動程式](hdinsight-connect-excel-hive-ODBC-driver.md): 了解如何使用 Microsoft Hive ODBC 驅動程式從 HDInsight 匯入資料。

* [Microsoft 雲端平台](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): 了解 Power BI for Office 365、 下載 SQL Server 試用版，以及設定 SharePoint Server 2013 和 SQL Server BI。

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">深入了解 SQL Server Analysis Services</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">了解 SQL Server Reporting Services</a>.


### 試用巨量資料分析的 Hadoop 解決方案 (適用於 Windows 上的 HDInsight)

在組織中使用巨量資料分析以深入了解您的業務。 這裡有一些範例：

* [分析 HVAC 感應器資料](hdinsight-hive-analyze-sensor-data.md): 了解如何使用 Hive 與 HDInsight (Hadoop) 分析感應器資料，然後在 Microsoft Excel 中的將資料視覺化。 在此範例中，您將使用 Hive 來處理 HVAC 系統所產生的歷史資料，以查明哪些系統無法可靠地維持規定的溫度。

* [搭配 HDInsight 使用 Hive 來分析網站記錄](hdinsight-hive-analyze-website-log.md): 了解如何使用 HDInsight 中的 HiveQL 來分析網站記錄，以深入的造訪次數，一天之中來自外部網站，以及使用者遭遇網站錯誤的摘要。

* [分析感應器資料中的使用 Storm 和 HDInsight (Hadoop) 中的 HBase 來即時](hdinsight-storm-sensor-data-analysis.md): 了解如何建置解決方案，在 HDInsight 中使用的 Storm 叢集感應器資料從 Azure 事件中心，然後顯示處理過的感應器資料以近乎即時的 web 型儀表板的資訊。


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

