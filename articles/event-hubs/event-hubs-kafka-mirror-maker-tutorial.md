---
title: Az Apache Kafka MirrorMaker használata – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk akta aKafka MirrorMaker használatával tükrözegy Kafka-fürtaz AzureEvent Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: fb041ec0d3cd474cca12d5ad55b733337566b9cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632787"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>A Kafka MirrorMaker használata Az Apache Kafka eseményelosztóival

Ez az oktatóanyag bemutatja, hogyan tükrözegy Kafka bróker egy eseményközpontban a Kafka MirrorMaker használatával.

   ![Kafka MirrorMaker eseményközpontokkal](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker).


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Kafka-fürt beállítása
> * Kafka MirrorMaker konfigurálása
> * Kafka MirrorMaker futtatása

## <a name="introduction"></a>Introduction (Bevezetés)
A modern felhőalapú alkalmazások egyik fő szempontja az infrastruktúra frissítésének, javításának és módosításának lehetősége a szolgáltatás megszakítása nélkül. Ez az oktatóanyag bemutatja, hogy egy eseményközpont és a Kafka MirrorMaker hogyan integrálhat egy meglévő Kafka-folyamatot az Azure-ba a Kafka bemeneti adatfolyam "tükrözésével" az Event Hubs szolgáltatásban. 

Az Azure Event Hubs Kafka-végpont lehetővé teszi, hogy az Azure Event Hubs-hoz a Kafka protokoll (azaz a Kafka-ügyfelek) használatával csatlakozzon. A Kafka-alkalmazások minimális módosításával csatlakozhat az Azure Event Hubs-hoz, és élvezheti az Azure-ökoszisztéma előnyeit. Az Event Hubs jelenleg a Kafka 1.0-s és újabb verzióit támogatja.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java Fejlesztő készlet (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és telepítése
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. A névtér és az eseményközpont létrehozásáról az [Eseményközpont létrehozása](event-hubs-create.md) című témakörben talál útmutatást. Győződjön meg arról, hogy másolja az Event Hubs kapcsolati karakterláncot későbbi használatra.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy rendelkezik egy Event Hubs kapcsolati karakterlánccal, klónozza a Kafka-tárház Azure-eseményközpontokat, és keresse meg az `mirror-maker` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka-fürt beállítása

A [Kafka rövid útmutató segítségével](https://kafka.apache.org/quickstart) állítsa be a fürt a kívánt beállításokat (vagy egy meglévő Kafka-fürt).

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker konfigurálása

Kafka MirrorMaker lehetővé teszi a "tükrözés" egy patak. Adott forrás- és célkafoka-fürtök, MirrorMaker biztosítja, hogy a forrásfürtbe küldött üzeneteket a forrás- és a célfürtök is megkapják. Ez a példa bemutatja, hogyan tükrözheti a forrás Kafka-fürt egy cél eseményközponttal. Ez a forgatókönyv egy meglévő Kafka-folyamatból az adatforgalom megszakítása nélkül történő küldésére használható. 

További részletes információk a Kafka MirrorMaker, lásd a [Kafka tükrözés / MirrorMaker útmutató](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

A Kafka MirrorMaker konfigurálásához adjon neki egy Kafka-fürtöt fogyasztóként/forrásként, és egy eseményközpontot gyártóként/célként.

#### <a name="consumer-configuration"></a>Fogyasztói konfiguráció

Frissítse a fogyasztói `source-kafka.config`konfigurációs fájlt, amely közli a MirrorMakerrel a forrás Kafka-fürt tulajdonságait.

##### <a name="source-kafkaconfig"></a>forrás-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Gyártói konfiguráció

Most frissítse a `mirror-eventhub.config`gyártó konfigurációs fájlját, amely megmondja a MirrorMakernek, hogy küldje el a duplikált (vagy "tükrözött") adatokat az Event Hubs szolgáltatásnak. Pontosabban `bootstrap.servers` módosítsa, `sasl.jaas.config` és mutasson az Event Hubs Kafka végpontra. Az Event Hubs szolgáltatás biztonságos (SASL) kommunikációt igényel, amely et úgy érik el, hogy az utolsó három tulajdonságot a következő konfigurációban állítja be: 

##### <a name="mirror-eventhubconfig"></a>tükör-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker futtatása

Futtassa a Kafka MirrorMaker parancsfájlt a gyökér Kafka könyvtárból az újonnan frissített konfigurációs fájlok használatával. Győződjön meg arról, hogy a konfigurációs fájlokat a gyökér Kafka könyvtárba másolja, vagy frissíti az elérési útjukat a következő parancsban.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Annak ellenőrzéséhez, hogy az események elérik-e az eseményközpontot, tekintse meg a bejövő támadások statisztikáit az [Azure Portalon,](https://azure.microsoft.com/features/azure-portal/)vagy futtasson egy fogyasztót az eseményközponton.

A MirrorMaker futásával a Kafka-fürtforrásra küldött eseményeket a Kafka-fürt és a tükrözött eseményközpont is megkapja. A MirrorMaker és egy Event Hubs Kafka-végpont használatával egy meglévő Kafka-folyamat ot telepíthet át a felügyelt Azure Event Hubs szolgáltatásba a meglévő fürt módosítása vagy a folyamatban lévő adatfolyam megszakítása nélkül.

## <a name="samples"></a>Példák
Tekintse meg a következő mintákat a GitHubon:

- [Mintakód az oktatóanyaghoz a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Az Azure Event Hubs Kafka MirrorMaker egy Azure Container-példányon fut](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Kafka eseményközpontokról, olvassa el az alábbi cikkeket:  

- [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka-adatfolyamok csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [Az Apache Kafka fejlesztői útmutatója az Azure Event Hubs-hoz](apache-kafka-developer-guide.md)