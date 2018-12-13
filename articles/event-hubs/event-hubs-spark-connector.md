---
title: Integráció az Apache Spark – Azure Event Hubs |} A Microsoft Docs
description: Integráció az Apache Spark strukturált Streamelés az Event Hubs engedélyezése
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
ms.openlocfilehash: 194898f658c08a3a5a9d8c1d601ea53c817e7649
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076815"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Az Apache Spark integrálása az Azure Event hubs szolgáltatással

Azure Event hubs szolgáltatás zökkenőmentesen integrálódik [Apache Spark](https://spark.apache.org/) épület engedélyezéséhez elosztott streamelési alkalmazásokat. Ez az integráció támogatja [Spark mag](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html), és [strukturált Stream](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Az Apache Spark Event Hubs-összekötő érhető el az [GitHub](https://github.com/Azure/azure-event-hubs-spark). Ebben a könyvtárban is használható a Maven-projektekhez az [Maven központi tárházból](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Ez a cikk bemutatja, hogyan hozhat létre a folyamatos alkalmazást [Azure Databricks](https://azure.microsoft.com/services/databricks/). Bár ebben a cikkben az Azure Databricks, Spark-fürtökön is elérhetők az [HDInsight](../hdinsight/spark/apache-spark-overview.md).

Ez a cikk a példában két Scala-jegyzetfüzetek: egy eseményközpont és a egy másik eseményeket küld vissza a események streamelése az.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Az Event Hubs-példány. Ha nem rendelkezik ilyennel, [hozzon létre egyet](event-hubs-create.md).
* Egy [Azure Databricks](https://azure.microsoft.com/services/databricks/) példány. Ha nem rendelkezik ilyennel, [hozzon létre egyet](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Hozzon létre egy maven-koordináták segítségével kódtár](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Stream-események az eseményközpontból, az alábbi kód használatával:

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
Az alábbi kód küldi az eseményeket az eseményközpontjának a Spark a batch API-k. Események küldése az event hubs egy streamelési lekérdezést is kiírhatja:

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

Most már tudja, hogyan állíthat be egy skálázható, hibatűrő streamet, az Apache Spark Event Hubs-összekötő használatával. További információ az Event Hubs használatával strukturált Stream és a Spark Streaming ezeket a hivatkozásokat követve:

* [Strukturált Streamelés + az Azure Event Hubs integrációs útmutató](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [A Spark Stream + Event Hubs integrációs útmutató](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
