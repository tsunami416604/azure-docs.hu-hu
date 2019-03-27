---
title: Például Azure HDInsight az Apache Storm-topológiák
description: Storm-topológiapéldák listája létrehozott és tesztelni az Apache Storm on HDInsight többek között az alapszintű C# és Java-topológiákat, és az Event hubs szolgáltatással működik.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 42780eae0a85ec8a2ca7e9d6ca1149f6530c25e1
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448976"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Az Apache Storm-topológiapéldák és Apache Storm on HDInsight összetevők

Az alábbiakban a létrehozott és a segítségével a Microsoft által karbantartott példáinak felsorolását [Apache Storm](https://storm.apache.org/) a HDInsight. Ezekben a példákban terjed ki a különböző témakörökben, hozzon létre alapszintű C# és Java-topológiákat, de az Azure-szolgáltatások, például az Event Hubs, a Cosmos DB, az SQL Database- [Apache HBase](https://hbase.apache.org/) a HDInsight és az Azure Storage. Néhány példa is bemutatják, hogyan használható a nem Azure-beli, vagy még nem Microsoft-technológiák, például a SignalR és a Socket.IO kódtár.

| Leírás | Azt ismerteti | Nyelv és keretrendszer |
|:--- |:--- |:--- |
| [Az Apache Storm írni az Azure Data Lake Storage](apache-storm-write-data-lake-store.md) |Az Azure Data Lake Storage írása |Java |
| [Event Hub Spout vagy Bolt forrásból](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Az Event Hub Spout és Bolt forrása |Java |
| [Java-alapú topológiák fejlesztése a HDInsight az Apache stormmal][5797064f] |Maven |Java |
| [Az Apache Storm on HDInsight Visual Studio használatával C#-topológiák fejlesztése][16fce2d1] |HDInsight Tools for Visual Studio |C#, Java |
| [Dolgozza fel az Azure Event hubs Eseményközpontokból a HDInsight-alapú Apache Storm (C#)][844d1d81] |Event Hubs |C# és Java |
| [Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |Java |
| [Járműérzékelő-adatok feldolgozása az Event hubs Eseményközpontokból a HDInsight Apache Storm használatával][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [A kinyerési, átalakítási és betöltési (ETL) az Azure Event Hubsból az Apache HBase, Apache Storm használata a HDInsight][b4b68194] |Az Event Hubs, HBase |C# |
| [Sablon C# Storm-topológia projektjét, Apache Storm on HDInsight az Azure-szolgáltatások használata][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase, SignalR |C#, Java |
| [Méretezhetőség referenciaalapokhoz képest történő olvasáshoz az Azure Event hubs Eseményközpontokból a HDInsight Apache Storm használatával][d6c540e3] |Üzeneteinek átviteli sebessége, az Event Hubs, az SQL Database |C#, Java |
| [A Python használata a HDInsight az Apache Stormmal](apache-storm-develop-python-topology.md) |Python-összetevők fluxus topológiákkal |Python |
| [Az Apache Kafka használata az Apache Storm on HDInsight](../hdinsight-apache-storm-with-kafka.md) | Az Apache Storm az Apache Kafka írásakor vagy olvasásakor | Java |

> [!WARNING]  
> A C# példák ebben a listában is eredetileg létrehozta és tesztelte a Windows-alapú HDInsight és a május nem működik megfelelően a Linux-alapú HDInsight-fürtökkel. Linux-alapú fürtök Mono segítségével futtathat egy .NET-kódot, és előfordulhat, hogy rendelkezik a keretrendszerek és a csomagokat, a példában használt kompatibilitási problémákat.
>
> Linux az egyetlen operációs rendszer használt a HDInsight 3.4-es vagy újabb verzió.

### <a name="next-steps"></a>További lépések

* [Bevezetés a HDInsight-alapú Apache Storm használatába][2b8c3488]
* [Ismerje meg, hogyan helyezheti üzembe és felügyelheti az Apache Storm on HDInsight az Apache Storm-topológiák][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Ismerje meg, hogyan hozzon létre egy Apache Storm HDInsight-fürtön, és a Storm irányítópultjának használatával például topológiák üzembe helyezése."
[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Ismerje meg, hogyan helyezheti üzembe és kezelhetők a topológiák a web-alapú Apache Storm irányítópultjának és a Storm felhasználói felületének vagy a HDInsight Tools for Visual Studio használatával."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Ismerje meg, hogyan hozhat létre a HDInsight Tools for Visual Studio használatával C# Storm-topológiák."
[5797064f]:apache-storm-develop-java-topology.md "Útmutató a Storm-topológiák létrehozása javában mavennel, hozzon létre egy alapszintű wordcount topológiát."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Ismerje meg, hogyan olvasási és írási adatokat az Azure Event Hubsból a Storm, a HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Ismerje meg, hogyan használja a Storm-topológia az Azure Event hubs szolgáltatástól érkező üzenetek olvasásához, olvassa el a dokumentumokat az Azure Cosmos DB az adatok hivatkozó és adatok mentése az Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Átviteli sebesség szemlélteti az Azure Event Hubsból olvasása vagy tárolását az SQL Database a HDInsight Apache Storm használatával számos topológiában."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Ismerje meg, hogyan lehet adatokat olvasni az Azure Event Hubs, összesített és átalakíthatja az adatokat, majd tárolja el azt a HDInsight-alapú HBase."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ez a projekt tartalmazza spoutok, boltok és topológiák kommunikál a különböző Azure-szolgáltatás például az Event Hubs, a Cosmos DB és az SQL Database-sablonokat."

