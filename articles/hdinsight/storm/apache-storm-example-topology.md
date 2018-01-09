---
title: "Példa Apache Storm-topológiák a hdinsight platformon |} Microsoft Docs"
description: "Példa Storm-topológiák listája létrehozott, és többek között az alapvető C# és a Java-topológiákat, és működik-e az Event Hubs HDInsight alatt futó Apache Storm tesztelték."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: c6c1a1483191e654c245eb3f05ee9e8406510b08
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Példa Storm-topológiák és a HDInsight alatt futó Apache Storm összetevők

A következő példák létrehozása és használata a HDInsight alatt futó Apache Storm a Microsoft által kezelt listája látható. Ezek a példák fedik témakörei alapvető C# és a Java-topológiákat, például az Event Hubs, a Cosmos DB, az SQL-adatbázis, a HBase a HDInsight és az Azure Storage Azure-szolgáltatások használatával hozzon létre. Néhány példa is bemutatják, hogyan használható az Azure-, vagy még nem a Microsofttól technológiái, például a SignalR és Socket.IO.

| Leírás | Azt mutatja be | Nyelvi/keretrendszer |
|:--- |:--- |:--- |
| [Azure Data Lake Store az Apache Storm írása](apache-storm-write-data-lake-store.md) |Azure Data Lake Store írása |Java |
| [Event Hub Spout és Bolt adatforrás](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Az Event Hub Spout és Bolt forrása |Java |
| [Java-alapú topológiák fejlesztése hdinsighton futó Apache stormra][5797064f] |Maven |Java |
| [Visual Studio használatával HDInsight alatt futó Apache Storm a C#-topológiák fejlesztése][16fce2d1] |HDInsight Tools for Visual Studio |C#, Java |
| [Az Azure Event Hubs (C#) futó Storm eseményeinek][844d1d81] |Event Hubs |C# és Java |
| [Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |Java |
| [Az Event Hubs a HDInsight alatt futó Storm használatával vehicle érzékelő adatok feldolgozása][246ee964] |Event Hubs, Cosmos DB, Azure Storage-Blob (WASB) |C#, Java |
| [Kinyerési, átalakítási és betöltési (ETL) az Azure Event Hubs a HBase a HDInsight alatt futó Storm használatával][b4b68194] |Az Event Hubs, HBase |C# |
| [C# Storm topology sablonprojekt Azure HDInsight alatt futó Storm-szolgáltatásokhoz való munkához][ce0c02a2] |Event Hubs, Cosmos DB SQL adatbázis, HBase, SignalR |C#, Java |
| [Méretezhetőség referenciaalapok Azure Event hubs a HDInsight alatt futó Storm használatával olvasásra][d6c540e3] |Üzenet átviteli, az Event Hubs SQL-adatbázis |C#, Java |
| [Python használata a HDInsight alatt futó Storm](apache-storm-develop-python-topology.md) |Python-összetevők a fluxus topológia |Python |
| [Kafka használata a HDInsight alatt futó Storm](../hdinsight-apache-storm-with-kafka.md) | Apache Storm olvasását és írását Apache Kafka | Java |

### <a name="next-steps"></a>További lépések

* [Bevezetés a HDInsight-alapú Apache Storm használatába][2b8c3488]
* [Megtudhatja, hogyan helyezheti üzembe és felügyelheti a HDInsight alatt futó Storm Storm-topológiák][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Ismerje meg, hozzon létre egy Storm on HDInsight-fürt, és a Storm irányítópultjának használatáról példa-topológia központi telepítéséhez."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Megtudhatja, hogyan helyezheti üzembe és felügyelheti a web-alapú Storm irányítópultjának és Storm felhasználói felületén vagy a HDInsight Tools for Visual Studio használatával topológiákat."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Megtudhatja, hogyan hozhat létre C# Storm-topológiák a HDInsight Tools for Visual Studio használatával."
[5797064f]:apache-storm-develop-java-topology.md "Ismerje meg a Storm-topológiák létrehozása a Java, Maven, használatával hozzon létre egy alapszintű wordcount-topológiával."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Megtudhatja, hogyan olvashatja és írhatja az adatokat az Azure Event Hubs Storm a HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Ismerje meg, hogyan használható a Storm-topológia Azure Event hubs olvashatja, olvassa el a dokumentumok a Azure Cosmos-Adatbázisból az adatok hivatkozó és adatok mentése az Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Számos topológiában olvasásakor az Azure Event Hubs és a HDInsight alatt futó Apache Storm használatával SQL-adatbázishoz való tárolását átviteli szemlélteti."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Megtudhatja, hogyan adatokat olvasni az Azure Event Hubs, összesített & átalakíthatja az adatokat, majd tárolja el azt a HDInsight HBase."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "A projekt tartalmaz a spoutokkal kapcsolatban, szögek és kommunikál a különböző Azure-szolgáltatásokhoz hasonlóan az Event Hubs, a Cosmos-adatbázis és az SQL-adatbázis topológiák sablonok."

