---
title: Apache Kafka MirrorMaker használata – Azure Event Hubs | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhatja a Kafka-MirrorMaker a Kafka-fürtök AzureEvent-Hubokban való tükrözéséhez.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6d1596cf0a50ed5dcb896896282178b6fc12c1a1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555103"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>A Kafka MirrorMaker használata a Event Hubs for Apache Kafka

Ez az oktatóanyag bemutatja, hogyan tükrözheti a Kafka-közvetítőt egy Kafka-kompatibilis Event hub-ban a Kafka MirrorMaker használatával.

   ![Kafka-MirrorMaker Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker).


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Kafka-fürt beállítása
> * Kafka-MirrorMaker konfigurálása
> * A Kafka MirrorMaker futtatása

## <a name="introduction"></a>Introduction (Bevezetés)
A modern felhőalapú méretezési alkalmazások egyik fő szempontja az infrastruktúra frissítése, javítása és módosítása a szolgáltatás megszakítása nélkül. Ez az oktatóanyag bemutatja, hogyan integrálható a Kafka-kompatibilis Event hub és a Kafka MirrorMaker egy meglévő Kafka-folyamatot az Azure-ba a "tükrözés" a Event Hubs szolgáltatásban található Kafka bemeneti streambe. 

Az Azure Event Hubs Kafka-végpont lehetővé teszi, hogy a Kafka-protokoll (azaz Kafka-ügyfelek) használatával kapcsolódjon az Azure Event Hubshoz. Egy Kafka-alkalmazás minimális módosításaival csatlakozhat az Azure Event Hubshoz, és élvezheti az Azure-ökoszisztéma előnyeit. A Kafka-kompatibilis Event Hubs jelenleg a Kafka 1,0-es és újabb verzióit támogatja.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* Maven bináris Archívum [letöltése](https://maven.apache.org/download.cgi) és [telepítése](https://maven.apache.org/install.html)
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. Az Event Hubs Kafka-végpont beszerzésével kapcsolatban olvassa el a [Kafka-kompatibilis eseményközpont létrehozásáról](event-hubs-create.md) szóló cikket. Ügyeljen arra, hogy későbbi használatra másolja a Event Hubs-kapcsolódási karakterláncot.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy van egy Kafka-kompatibilis Event Hubs a kapcsolódási sztringet, klónozott Azure-Event Hubs a Kafka-tárházhoz, és navigáljon a `mirror-maker` almappába:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka-fürt beállítása

A [Kafka gyors útmutatója](https://kafka.apache.org/quickstart) segítségével beállíthat egy fürtöt a kívánt beállításokkal (vagy használhat egy meglévő Kafka-fürtöt).

## <a name="configure-kafka-mirrormaker"></a>Kafka-MirrorMaker konfigurálása

A Kafka MirrorMaker lehetővé teszi egy adatfolyam "tükrözését". A forrás-és a cél Kafka-fürtök esetében a MirrorMaker biztosítja, hogy a forrás-és a cél fürtök is megkapják a forrásoldali fürtnek küldött üzeneteket. Ebből a példából megtudhatja, hogyan tükrözheti a forrás Kafka-fürtöt egy cél Kafka-kompatibilis Event hub használatával. Ez a forgatókönyv felhasználható egy meglévő Kafka-folyamat adatainak küldésére Event Hubs az adatáramlás megszakítása nélkül. 

A Kafka-MirrorMaker kapcsolatos részletes információkért lásd a [Kafka tükrözési/MirrorMaker útmutatóját](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

A Kafka-MirrorMaker konfigurálásához adjon neki egy Kafka-fürtöt fogyasztóként/forrásként, valamint egy Kafka-kompatibilis Event hub-t a gyártója/céljaként.

#### <a name="consumer-configuration"></a>Fogyasztói konfiguráció

Frissítse a felhasználó konfigurációs fájlját `source-kafka.config`, amely megadja a MirrorMaker a forrás Kafka-fürt tulajdonságait.

##### <a name="source-kafkaconfig"></a>forrás – Kafka. config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producer konfigurációja

Frissítse a `mirror-eventhub.config` gyártói konfigurációs fájlját, amely azt jelzi, hogy a MirrorMaker a duplikált (vagy "tükrözött") adatfájlokat a Event Hubs szolgáltatásnak küldi el. Pontosabban módosítsa `bootstrap.servers` és `sasl.jaas.config`, hogy az Event Hubs Kafka-végpontra mutasson. A Event Hubs szolgáltatásnak biztonságos (SASL) kommunikációra van szüksége, amely a következő konfiguráció utolsó három tulajdonságának beállításával érhető el: 

##### <a name="mirror-eventhubconfig"></a>Mirror-eventhub. config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>A Kafka MirrorMaker futtatása

Futtassa a Kafka MirrorMaker szkriptet a root Kafka-könyvtárból az újonnan frissített konfigurációs fájlok használatával. Győződjön meg arról, hogy a konfigurációs fájlokat másolja a legfelső szintű Kafka-könyvtárba, vagy frissítse az elérési útját a következő parancsban.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Annak ellenőrzéséhez, hogy az események elérik-e a Kafka-kompatibilis Event hub-t, tekintse meg a bejövő statisztikai adatokat a [Azure Portal](https://azure.microsoft.com/features/azure-portal/), vagy futtasson egy fogyasztót az Event hub-on.

Ha a MirrorMaker fut, a rendszer a Kafka-fürtnek küldött összes eseményt fogadja a Kafka-fürt és a tükrözött Kafka-kompatibilis Event hub szolgáltatás között. A MirrorMaker és egy Event Hubs Kafka-végpont használatával áttelepítheti a meglévő Kafka-folyamatokat a felügyelt Azure Event Hubs szolgáltatásba anélkül, hogy módosítaná a meglévő fürtöt, vagy meg kellene szakítania a folyamatban lévő adatfolyamot.

## <a name="samples"></a>Minták
Tekintse meg a következő mintákat a GitHubon:

- [Mintakód ehhez az oktatóanyaghoz a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka-MirrorMaker Azure Container-példányon futtatva](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Következő lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Kafka-fürt beállítása
> * Kafka-MirrorMaker konfigurálása
> * A Kafka MirrorMaker futtatása

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
