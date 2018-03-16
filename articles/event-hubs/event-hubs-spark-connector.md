---
title: "Apache Spark integrálása az Azure Event Hubs |} Microsoft Docs"
description: "Az Apache Spark adatfolyamként strukturált az Event Hubs integrálása"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija;sethm
ms.openlocfilehash: 3b44c7e7387eceb2d9ea0b2c214b13a82869110f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Apache Spark integrálása az Azure Event Hubs

Az Azure Event Hubs zökkenőmentesen integrálható a [Apache Spark](https://spark.apache.org/) épület-végpontok közötti annak elosztott alkalmazások könnyen adatfolyamként történő továbbítását. Ez az integráció támogatja [Spark mag](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html), [strukturált Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Az Apache Spark on az Event Hubs-összekötő érhető el a [GitHub](https://github.com/Azure/azure-event-hubs-spark). Ebben a könyvtárban is használható a Maven-projektek a [Maven központi tárházban](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

A cikkből megtudhatja, hogyan végezheti el a folyamatos alkalmazás [Azure Databrick](https://azure.microsoft.com/services/databricks/). Amíg ez a cikk használ [Azure Databricks](https://azure.microsoft.com/services/databricks/), Spark-fürtjei is elérhetők a [HDInsight](../hdinsight/spark/apache-spark-overview.md).

A következő példában két Scala notebookok: egyet az adatfolyamként történő eseményközpontban, és küldje el az események vissza azt egy másik származó események.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik ilyennel, [ingyenes fiók létrehozása](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Az Event Hubs példánya. Ha még nem rendelkezik ilyennel, [létrehozásához](event-hubs-create.md)
* Egy [Azure Databricks](https://azure.microsoft.com/services/databricks/) példány. Ha még nem rendelkezik ilyennel, [létrehozásához](../azure-databricks/quickstart-create-databricks-workspace-portal.md)
* [Hozzon létre egy könyvtár használatával maven koordináta](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

A következő kódot használja a notebook adatfolyam események az eseményközpontból.

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

// Select the body column and cast it to a string.
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
Az alábbi példakód események küldése az eseményközpont a Spark kötegelt API-k. Események küldése az event hubs egy adatfolyam-továbbítási lekérdezést is írhat.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta, hogyan működik az Event Hubs-összekötő épület valós idejű, hiba hibatűrő streamelési megoldások. További tudnivalók a strukturált Streamelésre és az Event Hubs integrált összekötő ezeket a hivatkozásokat követve:

* [Strukturált Streaming + Azure Event Hubs integrációs útmutatója](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming + Event Hubs integrációs útmutatója](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
