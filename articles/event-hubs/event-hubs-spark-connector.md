---
title: Integráció a Apache Spark-Azure Event Hubstal | Microsoft Docs
description: Ez a cikk bemutatja, hogyan integrálható a Apache Spark a strukturált adatfolyamok Event Hubs való engedélyezéséhez.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4c4fd74e9123e1310be297a15090433d365d24cf
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311682"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Apache Spark integrálása az Azure-Event Hubs

Az Azure Event Hubs zökkenőmentesen integrálható [Apache Sparkekkel](https://spark.apache.org/) , így lehetővé teszi az elosztott streaming-alkalmazások létrehozását. Ez az integráció a [Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html), a [Spark streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)és a [Structured streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)használatát támogatja. A Apache Spark Event Hubs-összekötője elérhető a [githubon](https://github.com/Azure/azure-event-hubs-spark). Ez a könyvtár a [Maven központi tárházból](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)származó Maven-projektekben is használható.

Ez a cikk bemutatja, hogyan hozhat létre folyamatos alkalmazást a [Azure Databricksban](https://azure.microsoft.com/services/databricks/). Ez a cikk Azure Databrickst használ, a Spark-fürtök [HDInsight](../hdinsight/spark/apache-spark-overview.md)is elérhetők.

A jelen cikkben szereplő példa két Scala jegyzetfüzetet használ: egyet az Event hub eseményeinek közvetítéséhez, egy másikat pedig az események visszaküldéséhez.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy Event Hubs példány. Ha még nem rendelkezik ilyennel, [hozzon létre egyet](event-hubs-create.md).
* Egy [Azure Databricks](https://azure.microsoft.com/services/databricks/) példány. Ha még nem rendelkezik ilyennel, [hozzon létre egyet](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Hozzon létre egy könyvtárat a Maven koordinátákkal](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Stream-események az Event hub használatával a következő kóddal:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
A következő kód eseményeket küld az Event hub-nak a Spark batch API-kkal. Az Event hub eseményeinek küldéséhez is írhat adatfolyam-lekérdezést:

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Következő lépések

Most már tudja, hogyan állíthat be méretezhető, hibatűrő adatfolyamot a Apache Spark Event Hubs-összekötője segítségével. További információ a Event Hubs strukturált streaming és Spark streaming használatával történő használatáról a következő hivatkozásokat követve:

* [Strukturált streaming + Azure Event Hubs integrációs útmutató](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark streaming + Event Hubs integrációs útmutató](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
