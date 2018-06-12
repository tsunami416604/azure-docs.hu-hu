---
title: Használja az Azure Event Hubs Kafka MirrorMaker Kafka ökoszisztéma |} Microsoft Docs
description: Kafka MirrorMaker segítségével az Event Hubs Kafka fürt tükrözik.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 0693fc2fff5735fb2b3c0a9b8f1d3d256746f40d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298321"
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Az Event Hubs Kafka MirrorMaker Kafka ökoszisztéma használ

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs).

Modern felhőalkalmazásokhoz méretezési egy fő szempont azt a képességet, frissítésére, javítására és infrastruktúra módosítsa a szolgáltatás megszakítása nélkül. Ez az oktatóanyag bemutatja, hogyan Kafka-kompatibilis eseményközpont, és Kafka MirrorMaker integrálható a meglévő Kafka adatcsatornáinak Azure "tükrözése" az Event Hubs szolgáltatásban Kafka bemeneti adatfolyam révén. 

Azure Event Hubs Kafka a végpont Azure Event hubs a Kafka protokollal (például Kafka ügyfelek) csatlakoztatását teszi lehetővé. Módosításokkal minimális Kafka alkalmazás, az Azure Event Hubs csatlakozhat, és teszik az az Azure-ökoszisztéma. Kafka engedélyezve van az Event Hubs jelenleg a 1.0-s és újabb verziók Kafka verzióit támogatja.

Ez a példa bemutatja, hogyan mappába történő tükrözésének egy Kafka broker egy Kafka engedélyezett eseményközpont Kafka MirrorMaker használatával.

   ![Az Event Hubs Kafka MirrorMaker](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, győződjön meg arról, hogy:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](http://maven.apache.org/download.cgi) és [telepítése](http://maven.apache.org/install.html) bináris Maven-archívum létrehozása
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Az Event Hubs-névtér létrehozása

Az Event Hubs névtér küldhet és fogadhat minden Event Hubs szolgáltatás szükséges. Lásd: [Eseményközpont létrehozásakor egy Kafka engedélyezve](event-hubs-create.md) Event Hubs Kafka végpont első kapcsolatos utasításokat. Ügyeljen arra, hogy másolja az Event Hubs kapcsolati karakterláncot későbbi használatra.

## <a name="clone-the-example-project"></a>Klónozza a példaprojekt

Most, hogy egy Kafka engedélyezve van-e az Event Hubs kapcsolati karakterláncot, az Azure Event Hubs tárház klónozása, és keresse meg a `mirror-maker` almappa:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka fürt beállítása

Használja a [Kafka gyors üzembe helyezési útmutató](https://kafka.apache.org/quickstart) állítsa be a kívánt beállításokat a fürtben (vagy egy meglévő Kafka-fürtöt használ).

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker lehetővé teszi, hogy a "tükrözési" az adatfolyam. A megadott forrás- és Kafka fürtök, MirrorMaker biztosítja, hogy a forrás fürt küldött üzeneteket fogadja a forrás- és fürtöket. Ez a példa bemutatja, hogyan mappába történő tükrözésének a forrás egy cél Kafka-kompatibilis eseményközpont Kafka fürt. Ez a forgatókönyv segítségével adatokat küldeni egy meglévő Kafka láncból Event Hubs az adatok áramlását megszakítása nélkül. 

További információk a Kafka MirrorMaker, lásd: a [Kafka tükrözés/MirrorMaker útmutató](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

### <a name="configuration"></a>Konfiguráció

Az Kafka MirrorMaker megadásához adjon neki a fogyasztói/forrása Kafka fürt és egy Kafka-kompatibilis eseményközpont, a gyártó vagy a cél.

#### <a name="consumer-configuration"></a>Felhasználói konfiguráció

A fogyasztó konfigurációs fájljának frissítése `source-kafka.config`, amely közli MirrorMaker a forrás Kafka fürt tulajdonságait.

##### <a name="source-kafkaconfig"></a>forrás-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Gyártó konfiguráció

A gyártó konfigurációs fájl frissíteni `mirror-eventhub.config`, amely közli, hogy a duplikált (vagy "tükrözött") adatok küldését az Event Hubs szolgáltatás MirrorMaker. Pontosabban, módosítsa `bootstrap.servers` és `sasl.jaas.config` az Event Hubs Kafka végpontjának mutassanak. Az Event Hubs szolgáltatás (SASL) biztonságos kommunikációt, amely érik el az utolsó három tulajdonságok beállítása a következő konfiguráció szükséges: 

##### <a name="mirror-eventhubconfig"></a>tükrözött-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>MirrorMaker futtatása

Futtassa a Kafka MirrorMaker parancsfájlt a legfelső szintű Kafka directory újonnan frissített konfigurációs fájlokat használja. Ügyeljen arra, hogy a konfigurációs fájlok másolása a legfelső szintű Kafka könyvtár, vagy az elérési utak a következő parancsot a frissítése.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Győződjön meg arról, hogy események elérni próbált Kafka-kompatibilis eseményközpont, tekintse meg a érkező adatokat az a [Azure-portálon](https://azure.microsoft.com/features/azure-portal/), vagy egy végfelhasználói futtatni az eseményközpontba.

Bármely Kafka fürt felé küldött események mindkét Kafka a fürt által fogadott MirrorMaker fut, és a tükrözött Kafka event hub szolgáltatás engedélyezve van. MirrorMaker és az Event Hubs Kafka végpont használatával telepítheti át a meglévő Kafka adatcsatornáinak a felügyelt Azure Event Hubs szolgáltatás a meglévő fürt módosítása, vagy minden folyamatban lévő adatfolyam megszakítása nélkül.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [További tudnivalók az Event Hubs a Kafka ökoszisztéma](event-hubs-for-kafka-ecosystem-overview.md)
* További információ [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) adatfolyam események Kafka a helyszíni Kafka engedélyezve van az event hubs felhő.
* Megtudhatja, hogyan adatfolyamként történő Kafka engedélyezve van az Event Hubs használatával [natív Kafka alkalmazások](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), vagy [Akka adatfolyamok](event-hubs-kafka-akka-streams-tutorial.md).
