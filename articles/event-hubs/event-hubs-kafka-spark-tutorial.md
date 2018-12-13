---
title: Csatlakozás az Apache Spark-alkalmazás – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk a Kafka használata az Apache Spark az Azure Event Hubs ismertetése.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 93fdd85d1fd1b91e01d8f38b4890e1b588a5c704
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091237"
---
# <a name="connect-your-apache-spark-application-with-kafka-enabled-azure-event-hubs"></a>Apache Spark-alkalmazás csatlakoztatása a Kafka-kompatibilis Azure Event Hubshoz
Ez az oktatóanyag azt mutatja be, hogyan csatlakoztathat egy Spark-alkalmazást a Kafka-kompatibilis Event Hubshoz, ha valós időben szeretne streamelni. Ez az integráció anélkül engedélyezi a streamelést, hogy módosítani kellene hozzá a protokollügyfeleket, vagy saját Kafka- vagy Zookeeper-fürtöket kellene futtatnia. Az oktatóanyaghoz az Apache Spark 2.4-es vagy újabb, illetve az Apache Kafka 2.0-s vagy újabb verziójára lesz szükség.

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * A Spark futtatása
> * Olvasás a Kafkához készült Event Hubsból
> * Írás a Kafkához készült Event Hubsba

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekikezdene az oktatóanyagnak, győződjön meg arról, hogy rendelkezik a következőkkel:
-   Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).
-   [Apache Spark 2.4-es verziója](https://spark.apache.org/downloads.html)
-   [Apache Kafka 2.0-s verziója]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> A Spark-Kafka adapter frissítve lett, hogy a Spark 2.4-es verziójától kezdődően támogassa a Kafka 2.0-s verzióját. A Spark korábbi kiadásaiban az adapter támogatta ugyan a Kafka 0.10-es és újabb verzióit, de elsősorban a Kafka 0.10-es verziójának API-jaira támaszkodott. Mivel a Kafkához készült Event Hubs nem támogatja a Kafka 0.10-es verzióját, az ökoszisztémája nem támogatja a 2.4-es előtti Spark-verziók Spark-Kafka adaptereit.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. Az Event Hubs Kafka-végpont beszerzésével kapcsolatban olvassa el a [Kafka-kompatibilis eseményközpont létrehozásáról](event-hubs-create.md) szóló cikket. Szerezze be az Event Hubs kapcsolati sztringjét és teljes tartománynevét (FQDN) későbbi használatra. Útmutatásért lásd az [Event Hubs kapcsolati sztring lekérésével](event-hubs-get-connection-string.md) foglalkozó témakört. 

## <a name="clone-the-example-project"></a>A példaprojekt klónozása
Klónozza az Azure Event Hubs-adattárat, és keresse meg a `tutorials/spark` almappát:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Olvasás a Kafkához készült Event Hubsból
Valamennyit módosítani kell a konfiguráció beállításain, hogy megkezdhesse a beolvasást a Kafkához készült Event Hubsból. Frissítse a **BOOTSTRAP_SERVERS** és az **EH_SASL** elemet a névtér részleteivel, és már meg is kezdheti a streamelést az Event Hubs segítségével, ugyanúgy, ahogy a Kafkával tenné. A teljes mintakódért tekintse meg a sparkConsumer.scala fájlt a GitHubon. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Írás a Kafkához készült Event Hubsba
Is írhat az Event Hubs Kafka írt azonos módon. Ne felejtse el frissíteni a konfigurációt, és módosítani a **BOOTSTRAP_SERVERS** és az **EH_SASL** elemet az Event Hubs-névtér adataival.  A teljes mintakódért tekintse meg a sparkProducer.scala fájlt a GitHubon. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan streamelhet a Spark-Kafka összekötő és a Kafkához készült Event Hubs használatával. A következő lépéseket végezte el: 

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * A Spark futtatása
> * Olvasás a Kafkához készült Event Hubsból
> * Írás a Kafkához készült Event Hubsba

Az Event Hubsszal és a Kafkához készült Event Hubsszal kapcsolatos további információkért tekintse át a következő témaköröket:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Apache Kafkához készült Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy Kafka-kompatibilis eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Az Apache Flink csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy Kafka-kompatibilis eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [Az Akka Streams csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
