---
title: Példa Apache Storm-topológiákra az Azure HDInsightban
description: Az Apache Storm segítségével a HDInsighton létrehozott és tesztelt Storm-topológiák listája, beleértve az alapvető C# és Java topológiákat, valamint az Event Hubs-szal való munkát.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 302ba583f11b15be98832316b1ea05c7f9be931f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530663"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Példa Apache Storm topológiákra és összetevőkre az Apache Storm hoz a HDInsighton

Az alábbi lista a Microsoft által a HDInsighton az [Apache Storm](https://storm.apache.org/) alkalmazáshoz létrehozott és karbantartott példákat tartalmazza. Ezek a példák számos témakört tartalmaznak, az alapvető C# és Java topológiák létrehozásától az Azure-szolgáltatásokkal, például az Event Hubs, a Cosmos DB, az SQL Database, az [Apache HBase](https://hbase.apache.org/) hdinsight-on és az Azure Storage használatával való együttműködésig. Néhány példa azt is bemutatja, hogyan működik a nem Azure-on kívüli, vagy akár nem a Microsoft-technológiák, például a SignalR és Socket.IO.

| Leírás | Útmutató ehhez: | Nyelv/keretrendszer |
|:--- |:--- |:--- |
| [Írás az Azure Data Lake Storage szolgáltatásba az Apache Storm szolgáltatásból](apache-storm-write-data-lake-store.md) |Írás az Azure Data Lake Storage szolgáltatásba |Java |
| [Event Hub spout és Bolt forrás](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Az Event Hub spout és a Bolt forrása |Java |
| [Java-alapú topológiák fejlesztése apache stormhoz a HDInsighton][5797064f] |Maven |Java |
| [C# topológiák fejlesztése apache stormhoz a HDInsighton a Visual Studio használatával][16fce2d1] |HDInsight-eszközök a Visual Studio számára |C#, Jáva |
| [Események feldolgozása az Azure Event Hubs-ból az Apache Storm segítségével a HDInsighton (C#)][844d1d81] |Event Hubs |C# és Java |
| [Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) |Event Hubs |Java |
| [Az Event Hubs járműérzékelőadatainak feldolgozása az Apache Storm használatával a HDInsighton][246ee964] |Eseményközpontok, Cosmos DB, Azure Storage Blob (WASB) |C#, Jáva |
| [Kibontás, átalakítás és betöltés (ETL) az Azure Event Hubs-ból az Apache HBase-be az Apache Storm használatával a HDInsighton][b4b68194] |Eseményközpontok, HBase |C# |
| [C/ Storm-topológiasablon-topológiaprojekt az Apache Storm Azure-szolgáltatásaival való munkához a HDInsighton][ce0c02a2] |Eseményközpontok, Cosmos DB, SQL Database, HBase, SignalR |C#, Jáva |
| [Méretezhetőségi teljesítménymutatók az Azure Event Hubs-ból való olvasáshoz az Apache Storm használatával a HDInsighton][d6c540e3] |Üzenetátviteli rendszer, Eseményközpontok, SQL-adatbázis |C#, Jáva |
| [Az Apache Kafka használata apache stormtal a HDInsighton](../hdinsight-apache-storm-with-kafka.md) | Apache Storm olvasás és írás apache Kafka | Java |

> [!WARNING]  
> A listában szereplő C# példákat eredetileg a Windows-alapú HDInsight segítségével hozták létre és tesztelték, és előfordulhat, hogy nem működnek megfelelően a Linux-alapú HDInsight-fürtökkel. A Linux-alapú fürtök a Monót használják a .NET-kód futtatásához, és kompatibilitási problémákat okozhatnak a példában használt keretrendszerekkel és csomagokkal.
>
> A Linux az egyetlen operációs rendszer, amelyet a HDInsight 3.4-es vagy újabb verziójában használnak.

## <a name="python-only"></a>Csak Python

Lásd: [Python használata az Apache Storm hdinsight](apache-storm-develop-python-topology.md) egy példa a Flux-topológia Python-összetevők.

## <a name="next-steps"></a>Következő lépések

* [Apache Storm-topológia létrehozása és figyelése az Azure HDInsightban](./apache-storm-quickstart.md)
* [Ismerje meg, hogyan telepítheti és kezelheti az Apache Storm topológiákat az Apache Storm segítségével a HDInsighton][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Megtudhatja, hogy miként telepítheti és kezelheti a topológiákat a webalapú Apache Storm Dashboard és Storm UI vagy a HDInsight Tools for Visual Studio használatával."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Ismerje meg, hogyan hozhat létre C# Storm topológiákat a HDInsight-eszközök a Visual Studio számára használatával."
[5797064f]:apache-storm-develop-java-topology.md "Ismerje meg, hogyan hozhat létre Storm topológiákat Java-ban a Maven használatával egy alapvető szószám-topológia létrehozásával."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Megtudhatja, hogyan olvashatés írhat adatokat az Azure Event Hubs-ból a Storm segítségével a HDInsight-on."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Megtudhatja, hogy a Storm-topológia használatával hogyan olvashat üzeneteket az Azure Event Hubs-ból, hogyan olvashat dokumentumokat az Azure Cosmos DB-ből az adatok hivatkozásához és az Adatok Azure Storage-ba való mentéséhez."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Számos topológia az átviteli érték bemutatására az Azure Event Hubs-ból való olvasásés az SQL-adatbázisba való tárolás során az Apache Storm használatával a HDInsight-on."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Megtudhatja, hogyan olvashat adatokat az Azure Event Hubs-ból, hogyan összesíthet & alakíthat át az adatokat, majd tárolhatja azokat a HDInsight HBase szolgáltatásában."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ez a projekt spoutok, csavarok és topológiák sablonokat tartalmaz a különböző Azure-szolgáltatásokkal, például az Event Hubs, a Cosmos DB és az SQL Database használatával."
