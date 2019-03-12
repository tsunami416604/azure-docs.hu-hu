---
title: Az Apache Kafka Mirrormakerrel – használja az Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk ismertetése Kafka MirrorMaker használata a Kafka-fürt AzureEvent hubs tükrözéséhez.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: a0f22e7940a2be442bdf836c0e76e29bad2e9b36
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769923"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Az Event Hubs-beli Apache kafka Kafka MirrorMaker használata

Ez az oktatóanyag bemutatja, hogyan egy Kafka-közvetítő egy Kafka engedélyezett eseményközpont Kafka MirrorMaker használatával tükrözéséhez.

   ![A Kafka Mirrormakerrel az Event hubs szolgáltatással](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker).


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Kafka-fürt beállítása
> * A Kafka Mirrormakerrel konfigurálása
> * Futtassa a Kafka Mirrormakerrel

## <a name="introduction"></a>Bevezetés
A modern felhőalapú, méretezhető alkalmazások megfontolandó egy rendszer azon képessége, frissítésére, javítására és infrastruktúra módosítása nélkül szolgáltatás megszakítása. Ez az oktatóanyag bemutatja, hogyan Kafka-kompatibilis eseményközpont, és a Kafka Mirrormakerrel integrálható egy meglévő Kafka-folyamatot az Azure-bA "tükrözési" bemeneti stream Kafka az Event Hubs szolgáltatás által. 

Az Azure Event Hubs Kafka végpont lehetővé teszi, hogy csatlakozhat az Azure Event Hubsba, a Kafka (vagyis a Kafka ügyfelek) protokoll használatával. Minimális változtatásokat, így a Kafka-alkalmazások, az Azure Event Hubsba csatlakozhat, és az Azure-ökoszisztéma előnyeit élvezheti. A Kafka engedélyezve van az Event Hubs jelenleg a Kafka-verziók 1.0-s és újabb verziók támogatja.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](https://maven.apache.org/download.cgi) és [telepítése](https://maven.apache.org/install.html) a Maven bináris archívum
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. Az Event Hubs Kafka-végpont beszerzésével kapcsolatban olvassa el a [Kafka-kompatibilis eseményközpont létrehozásáról](event-hubs-create.md) szóló cikket. Ellenőrizze, hogy az Event Hubs kapcsolati karakterláncot a későbbi használatra.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy a Kafka engedélyezve van az Event Hubs kapcsolati karakterláncot, az Azure Event Hubs, Kafka-tárház klónozása, és keresse meg a `mirror-maker` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka-fürt beállítása

Használja a [Kafka a rövid útmutató](https://kafka.apache.org/quickstart) a kívánt beállításokat a fürt létrehozása (vagy egy meglévő Kafka-fürtöt használ).

## <a name="configure-kafka-mirrormaker"></a>A Kafka Mirrormakerrel konfigurálása

A Kafka Mirrormakerrel lehetővé teszi, hogy a "tükrözési" datového proudu. A megadott forrás- és a Kafka-fürtök, Mirrormakerrel biztosítja, hogy a forrás- és -fürtök által fogadott bármely a kiindulási fürt küldött üzenetek. Ez a példa bemutatja, hogyan egy forrás és a cél a Kafka-kompatibilis eseményközpont Kafka-fürt tükrözéséhez. Ez a forgatókönyv segítségével adatokat küldeni egy meglévő Kafka-folyamatot az Event Hubs az adatok áramlását megszakítása nélkül. 

Részletes információkat a Kafka Mirrormakerrel, tekintse meg a [Kafka tükrözés/Mirrormakerrel útmutató](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Kafka Mirrormakerrel konfigurálásához adjon meg a fogyasztói/forrása a Kafka-fürt és a egy Kafka-kompatibilis event hubs, az előállítói és a cél.

#### <a name="consumer-configuration"></a>Felhasználói konfiguráció

A felhasználói konfigurációs fájljának frissítése `source-kafka.config`, amely arra utasítja a MirrorMaker a Kafka-fürt forrás tulajdonságait.

##### <a name="source-kafkaconfig"></a>forrás-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Gyártó konfiguráció

Most már a gyártó konfigurációs fájl frissítésével `mirror-eventhub.config`, amely tájékoztatja a MirrorMaker adatokat lehet küldeni az ismétlődő (vagy "tükrözött") az Event Hubs szolgáltatás. Pontosabban, módosítsa `bootstrap.servers` és `sasl.jaas.config` az Event Hubs Kafka végpontjára mutató. Az Event Hubs szolgáltatás (SASL) biztonságos kommunikációt, ami valósítja meg az utolsó három tulajdonságok beállítása a következő konfiguráció szükséges: 

##### <a name="mirror-eventhubconfig"></a>tükrözött-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Futtassa a Kafka Mirrormakerrel

A Kafka Mirrormakerrel parancsprogrammal a legfelső szintű Kafka directory újonnan frissített konfigurációs fájlokat használja. Ügyeljen arra, hogy a konfigurációs fájlok másolja át a legfelső szintű könyvtár a Kafka, vagy az alábbi parancsban az útvonalak frissítése.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Győződjön meg arról, hogy eseményeket elérni próbált a Kafka-kompatibilis eseményközpontot, tekintse meg a bejövő adatokat, az a [az Azure portal](https://azure.microsoft.com/features/azure-portal/), vagy az event hubs egy fogyasztó futtatásához.

A MirrorMaker fut a forrás-Kafka-fürt bármely esemény mindkét a Kafka-fürt által fogadott és a tükrözött Kafka event hub-szolgáltatás engedélyezve van. A MirrorMaker és a egy Event Hubs Kafka-végpontot, Kafka meglévő adatcsatornáinak áttelepítheti a a felügyelt Azure Event Hubs szolgáltatás módosítása a meglévő fürtből, és minden folyamatban lévő adatokat a folyamat megszakítása nélkül.

## <a name="next-steps"></a>További lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Kafka-fürt beállítása
> * A Kafka Mirrormakerrel konfigurálása
> * Futtassa a Kafka Mirrormakerrel

Az Event Hubsszal és a Kafkához készült Event Hubsszal kapcsolatos további információkért tekintse át a következő témaköröket:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Apache Kafkához készült Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Az Apache Spark csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy Kafka-kompatibilis eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [Az Akka Streams csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
