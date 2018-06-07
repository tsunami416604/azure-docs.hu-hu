---
title: Apache Spark integrálása az Azure Event Hubs |} Microsoft Docs
description: Az Apache Spark adatfolyamként strukturált az Event Hubs integrálása
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: sethm
ms.openlocfilehash: 9f1cf75fdea1dd7f5842c2efdaeca663d611065c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626921"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Apache Spark integrálása az Azure Event Hubs

Az Azure Event Hubs zökkenőmentesen integrálható a [Apache Spark](https://spark.apache.org/) épület engedélyezése elosztott adatfolyam-továbbítási alkalmazások. Ez az integráció támogatja [Spark mag](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html), és [strukturált Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Az Apache Spark on az Event Hubs-összekötő érhető el a [GitHub](https://github.com/Azure/azure-event-hubs-spark). Ebben a könyvtárban is használható a Maven-projektek a [Maven központi tárházban](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Ez a cikk ismerteti, hogyan hozhat létre a folyamatos alkalmazást [Azure Databricks](https://azure.microsoft.com/services/databricks/). Ebben a cikkben az Azure Databricks, amíg a Spark-fürtök is elérhetők a [HDInsight](../hdinsight/spark/apache-spark-overview.md).

Ez a cikk a példában két Scala notebookok: egyet az adatfolyamként történő eseményközpontban, és küldje el az események vissza azt egy másik származó események.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik ilyennel, [ingyenes fiók létrehozását](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Az Event Hubs példánya. Ha még nem rendelkezik ilyennel, [hozzon létre egyet](event-hubs-create.md).
* Egy [Azure Databricks](https://azure.microsoft.com/services/databricks/) példány. Ha még nem rendelkezik ilyennel, [hozzon létre egyet](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Hozzon létre egy könyvtár használatával maven-koordináták](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

A következő kódot az eseményközpont adatfolyam események:

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
Az alábbi kód események küldése az eseményközpont a Spark kötegelt API-k. Is írhat az event hubs üzenetküldési adatfolyam lekérdezést:

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

## <a name="next-steps"></a>További lépések

Most már tudja, hogyan állítsa be az Apache Spark on az Event Hubs összekötő segítségével méretezhető, hibatűrő adatfolyam. További információ az Event Hubs strukturált adatfolyam-és Spark Streaming ezeket a hivatkozásokat követve:

* [Strukturált Streaming + Azure Event Hubs integrációs útmutatója](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming + Event Hubs integrációs útmutatója](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
