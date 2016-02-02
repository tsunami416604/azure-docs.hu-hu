<properties
 pageTitle="HDInsight 上的 Apache Storm 拓撲 | Microsoft Azure"
 description="利用 Apache Storm on HDInsight 建立和測試的範例 Storm 拓撲清單 (包含基本 basic C# 和 Java 拓撲)，以及使用事件中樞。"
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
 ms.date="12/04/2015"
 ms.author="larryfr"/>


# HDInsight 上 Apache Storm 的範例 Storm 拓撲及元件

以下是 Microsoft 所建立和維護的範例清單，可搭配 Apache Storm on HDInsight 使用。 這些範例涵蓋各種主題，從建立基本 C# 和 Java 拓撲以使用 Azure 服務，例如事件中心、DocumentDB、Power BI、SQL Database、HBase on HDInsight 和 Azure 儲存體。 一些範例也會示範如何使用非 Azure 或甚至非 Microsoft 的技術，例如 SignalR 和 Socket.IO

| 說明| 示範| 語言/架構|
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [事件中心 Spout 和 Bolt 的來源](https://github.com/apache/storm/tree/master/external/storm-eventhubs)| 事件中樞 Spout 和 Bolt 的來源| Java|
| [開發 Apache Storm on HDInsight ][5797064f]| Maven| Java|
| [開發使用 Visual Studio ][16fce2d1]| HDInsight Tools for Visual Studio| C#，Java|
| [在 C# Storm 拓撲 ][ec5a4064]| 多個資料流| C#|
| [決定使用 Storm on HDInsight ][3c86c7c8]| Trident| Java，Trident|
| [處理 Azure 事件中心與 Storm on HDInsight (C#) ][844d1d81]| 事件中樞| C# 和 Java|
| [處理 Azure 事件中心與 Storm on HDInsight (Java) 的事件](hdinsight-storm-develop-java-event-hub-topology.md)| 事件中樞| Java|
| [使用 Power Bi (預覽) 來視覺化 Storm 拓撲 ][94d15238]| Power BI| C#|
| [使用 Storm 和 HDInsight ][ab894747]| 事件中心、HBase、Socket.IO、Web 儀表板| C#、Java、JavaScript、HTML|
| [處理使用 Storm on HDInsight ][246ee964]| 事件中心、DocumentDb、Azure 儲存體 Blob (WASB)| C#，Java|
| [擷取、 轉換及載入 (ETL) 從 Azure 事件中心到 HBase，使用 Storm on HDInsight ][b4b68194]| 事件中心，HBase| C#|
| [使用 Storm on HDInsight ][ce0c02a2]| 事件中心、DocumentDb、SQL Database、HBase、SignalR| C#，Java|
| [從 Azure 事件中心使用 Storm on HDInsight ][d6c540e3]| 訊息輸送量、事件中心、SQL Database| C#，Java|
| [使用 Storm 和 HBase on HDInsight 的事件相互關聯](hdinsight-storm-correlation-topology.md)| HBase| C#|
| [使用 Storm on HDInsight 來使用 Python](hdinsight-storm-develop-python-topology.md)| Python 元件及以 Java 和 Clojure 為基礎的 Storm 拓撲| Python|

## 後續步驟

* [開始使用 Apache Storm on HDInsight ][2b8c3488]

* [了解如何部署和管理 Storm 拓撲，使用 Storm on HDInsight ][6eb0d3b8]


[2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Learn how to create a Storm on HDInsight cluster and use the Storm Dashboard to deploy example topologies."
[6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Learn how to deploy and manage topologies using the web-based Storm Dashboard and Storm UI or the HDInsight Tools for Visual Studio."
[16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Learn how to create C# Storm topologies by using the HDInsight Tools for Visual Studio."
[5797064f]: hdinsight-storm-develop-java-topology.md "Learn how to create Storm topologies in Java, using Maven, by creating a basic wordcount topology."
[94d15238]: hdinsight-storm-power-bi-topology.md "Demonstrates how to write data to Power BI from a C# topology, then create a chart and dashboard from the data."
[ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Demonstrates a basic Storm topology that performs a wordcount, implemented in C#. This also demonstrates how to create multiple data streams within a C# topology."
[844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Learn how to read and write data from Azure Event Hubs with Storm on HDInsight."
[ab894747]: hdinsight-storm-sensor-data-analysis.md "Learn how to use Apache Storm on HDInsight to process sensor data from Azure Event Hubs, visualize it using D3.js, and (optionally,) store it to HBase."
[3c86c7c8]: hdinsight-storm-twitter-trending.md "Learn how to use Trident to create a Storm topology that determines trending topics (based on hashtags,) on Twitter."
[246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Learn how to use a Storm topology to read messages from Azure Event Hubs, read documents from Azure DocumentDB for data referencing and save data to Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Several topologies to demonstrate throughput when reading from Azure Event Hubs and storing to SQL Database using Apache Storm on HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Learn how to read data from Azure Event Hubs, aggregate & transform the data, then store it to HBase on HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "This project contains templates for spouts, bolts and topologies to interact with various Azure services like Event Hubs, DocumentDB, and SQL Database."

