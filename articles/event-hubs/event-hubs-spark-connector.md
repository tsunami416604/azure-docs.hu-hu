---
title: Integráció az Apache Sparkkal – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan integrálható az Apache Spark strukturált streamelési az Event Hubs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311682"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Az Apache Spark integrálása az Azure Event Hubs-szal

Az Azure Event Hubs zökkenőmentesen integrálható [az Apache Sparkkal,](https://spark.apache.org/) hogy lehetővé tegye az elosztott streamelt alkalmazások készítését. Ez az integráció támogatja a [Spark Core,](https://spark.apache.org/docs/latest/rdd-programming-guide.html) [spark streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)és strukturált [streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Az Apache Spark Eseményközpontok összekötője elérhető a [GitHubon.](https://github.com/Azure/azure-event-hubs-spark) Ez a könyvtár a [Maven központi adattár](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)maveni projektjeiben is használható.

Ez a cikk bemutatja, hogyan hozhat létre folyamatos alkalmazást az [Azure Databricks](https://azure.microsoft.com/services/databricks/)alkalmazásban. Bár ez a cikk az Azure Databricks szolgáltatást használja, a Spark-fürtök a [HDInsight](../hdinsight/spark/apache-spark-overview.md)segítségével is elérhetők.

Ebben a cikkben szereplő példa két Scala-jegyzetfüzetet használ: az egyik et egy eseményközpontból való események streamelésére, a másikat pedig az események visszaküldésére.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik ilyen, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy Eseményközpont-példány. Ha nem rendelkezik ilyen, [hozzon létre egyet](event-hubs-create.md).
* Egy [Azure Databricks-példány.](https://azure.microsoft.com/services/databricks/) Ha nem rendelkezik ilyen, [hozzon létre egyet](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Tár létrehozása a maven koordinátáival:](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package) `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Események streamelése az eseményközpontból a következő kód használatával:

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
A következő kód eseményeket küld az eseményközpontba a Spark kötegapi-k. Streamelési lekérdezést is írhat, hogy eseményeket küldjön az eseményközpontba:

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

Most már tudja, hogyan állíthat be egy méretezhető, hibatűrő adatfolyamot az Apache Spark Eseményközpont-összekötő használatával. További információ az Event Hubs strukturált streameléssel és Spark Streaming del való használatáról az alábbi hivatkozásokon:

* [Strukturált streamelés + Azure-eseményközpontok integrációs útmutatója](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming + Eseményközpontok integrációs útmutatója](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
