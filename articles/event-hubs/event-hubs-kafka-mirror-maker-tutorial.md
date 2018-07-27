---
title: Az Azure Event hubs szolgáltatással Apache Kafka MirrorMaker használata a Kafka-ökoszisztéma |} A Microsoft Docs
description: Kafka MirrorMaker használatával Kafka-fürt, az Event Hubs tükrözéséhez.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 86fb1a49d8eabca0a260bf9e10d16d88dadf2c34
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282840"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Az Event Hubs-beli Apache kafka Kafka MirrorMaker használata

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs).

A modern felhőalapú, méretezhető alkalmazások megfontolandó egy rendszer azon képessége, frissítésére, javítására és infrastruktúra módosítása nélkül szolgáltatás megszakítása. Ez az oktatóanyag bemutatja, hogyan Kafka-kompatibilis eseményközpont, és a Kafka Mirrormakerrel integrálható egy meglévő Kafka-folyamatot az Azure-bA "tükrözési" bemeneti stream Kafka az Event Hubs szolgáltatás által. 

Az Azure Event Hubs Kafka végpont lehetővé teszi, hogy csatlakozhat az Azure Event Hubsba, a Kafka (vagyis a Kafka ügyfelek) protokoll használatával. Minimális változtatásokat, így a Kafka-alkalmazások, az Azure Event Hubsba csatlakozhat, és az Azure-ökoszisztéma előnyeit élvezheti. A Kafka engedélyezve van az Event Hubs jelenleg a Kafka-verziók 1.0-s és újabb verziók támogatja.

Ez a példa bemutatja, hogyan egy Kafka-közvetítő egy Kafka engedélyezett eseményközpont Kafka MirrorMaker használatával tükrözéséhez.

   ![A Kafka Mirrormakerrel az Event hubs szolgáltatással](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](http://maven.apache.org/download.cgi) és [telepítése](http://maven.apache.org/install.html) a Maven bináris archívum
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Event Hubs-névtér küldeni és fogadni az összes Event Hubs szolgáltatás szükséges. Lásd: [létrehozott Kafka engedélyezve van az Event Hubs](event-hubs-create.md) Event Hubs Kafka végpont beszerzésével kapcsolatban. Ellenőrizze, hogy az Event Hubs kapcsolati karakterláncot a későbbi használatra.

## <a name="clone-the-example-project"></a>A példa-projekt klónozása

Most, hogy a Kafka engedélyezve van az Event Hubs kapcsolati karakterláncot, az Azure Event Hubs-tárház klónozásához, és keresse meg a `mirror-maker` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka-fürt beállítása

Használja a [Kafka a rövid útmutató](https://kafka.apache.org/quickstart) a kívánt beállításokat a fürt létrehozása (vagy egy meglévő Kafka-fürtöt használ).

## <a name="kafka-mirrormaker"></a>A Kafka Mirrormakerrel

A Kafka Mirrormakerrel lehetővé teszi, hogy a "tükrözési" datového proudu. A megadott forrás- és a Kafka-fürtök, Mirrormakerrel biztosítja, hogy a forrás- és -fürtök által fogadott bármely a kiindulási fürt küldött üzenetek. Ez a példa bemutatja, hogyan egy forrás és a cél a Kafka-kompatibilis eseményközpont Kafka-fürt tükrözéséhez. Ez a forgatókönyv segítségével adatokat küldeni egy meglévő Kafka-folyamatot az Event Hubs az adatok áramlását megszakítása nélkül. 

Részletes információkat a Kafka Mirrormakerrel, tekintse meg a [Kafka tükrözés/Mirrormakerrel útmutató](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

### <a name="configuration"></a>Konfiguráció

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

### <a name="run-mirrormaker"></a>Futtassa a MirrorMaker

A Kafka Mirrormakerrel parancsprogrammal a legfelső szintű Kafka directory újonnan frissített konfigurációs fájlokat használja. Ügyeljen arra, hogy a konfigurációs fájlok másolja át a legfelső szintű könyvtár a Kafka, vagy az alábbi parancsban az útvonalak frissítése.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Győződjön meg arról, hogy eseményeket elérni próbált a Kafka-kompatibilis eseményközpontot, tekintse meg a bejövő adatokat, az a [az Azure portal](https://azure.microsoft.com/features/azure-portal/), vagy az event hubs egy fogyasztó futtatásához.

A MirrorMaker fut a forrás-Kafka-fürt bármely esemény mindkét a Kafka-fürt által fogadott és a tükrözött Kafka event hub-szolgáltatás engedélyezve van. A MirrorMaker és a egy Event Hubs Kafka-végpontot, Kafka meglévő adatcsatornáinak áttelepítheti a a felügyelt Azure Event Hubs szolgáltatás módosítása a meglévő fürtből, és minden folyamatban lévő adatokat a folyamat megszakítása nélkül.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [További információ az Event Hubs, Kafka for](event-hubs-for-kafka-ecosystem-overview.md)
* Tudjon meg többet [Mirrormakerrel](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) stream eseményekre a Kafkából kafka helyszíni engedélyezve van az event hubs szolgáltatás a felhőben.
* Ismerje meg, hogyan streamelése Kafka az engedélyezett az Event Hubs használatával [Kafka natív alkalmazások](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), vagy [Akka Streamek](event-hubs-kafka-akka-streams-tutorial.md).
