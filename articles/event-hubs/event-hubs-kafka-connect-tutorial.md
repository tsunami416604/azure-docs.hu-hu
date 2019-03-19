---
title: Az Apache Kafka Connect – Azure Event Hubs integrálása |} A Microsoft Docs
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
ms.openlocfilehash: 2ed4432aec9b833efe6b521b4452177088d21d70
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119411"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Az Apache Kafka Connect-támogatás és az Azure Event Hubs integrálása (előzetes verzió)
Az üzleti igények növekedésével arra is egyre nagyobb igény jelentkezik, hogy a rendszer képes legyen különböző külső források és fogadók betöltésére. Az [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) által biztosított keretrendszer egy Kafka-fürtön keresztül képes csatlakozni és adatokat importálni/exportálni olyan külső rendszerekből, mint a MySQL, a HDFS és különböző fájlrendszerek. Ez az oktatóanyag azt mutatja be, hogyan használható a Kafka Connect keretrendszere a Kafka-kompatibilis Event Hubs szolgáltatással.

Végigvezeti a Kafka Connect Kafka-kompatibilis Azure-eseményközponttal való integrálásának, valamint az alapszintű FileStreamSource és FileStreamSink összekötők üzembe helyezésének lépésein. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Bár ezeket az összekötőket nem éles környezetben való használatra szánták, egy olyan teljes körű Kafka Connect-forgatókönyvet mutatnak be, amelyben az Azure Event Hubs Kafka-közvetítőként működik.

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * A Kafka Connect konfigurálása az Event Hubshoz
> * A Kafka Connect futtatása
> * Összekötők létrehozása

## <a name="prerequisites"></a>Előfeltételek
A bemutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka-példány (1.1.1-es verzió, 2.11-es Scala verzió), amely a [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1) webhelyről tölthető le
- Az [Apache Kafkához készült Event Hubsot](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview) ismertető cikket is mindenképpen olvassa át.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. Az Event Hubs Kafka-végpont beszerzésével kapcsolatban olvassa el a [Kafka-kompatibilis eseményközpont létrehozásáról](event-hubs-create.md) szóló cikket. Szerezze be az Event Hubs kapcsolati sztringjét és teljes tartománynevét (FQDN) későbbi használatra. Útmutatásért lásd az [Event Hubs kapcsolati sztring lekérésével](event-hubs-get-connection-string.md) foglalkozó témakört. 

## <a name="clone-the-example-project"></a>A példaprojekt klónozása
Klónozza az Azure Event Hubs-adattárat, és keresse meg a tutorials/connect almappát: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>A Kafka Connect konfigurálása az Event Hubshoz
A Kafka Connect teljesítményének a Kafkából az Event Hubsba való átirányításához minimális újrakonfigurálásra van szükség.  Az alábbi `connect-distributed.properties` példa bemutatja, hogyan konfigurálhatja a Connectet úgy, hogy az Event Hubs Kafka-végpontjával hitelesítsen és kommunikáljon:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>A Kafka Connect futtatása

Ebben a lépésben helyileg el fog indítani egy Kafka Connect-feldolgozót elosztott módban, és az Event Hubsot fogja használni a fürtállapot fenntartásához.

1. Mentse helyileg a fenti `connect-distributed.properties` fájlt.  Ne felejtse el lecserélni a zárójelbe foglalt értékeket.
2. Keresse meg a Kafka-példány helyét a számítógépén.
4. Futtassa az `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` parancsot.  A Connect-feldolgozó REST API akkor áll készen az interakcióra, amikor meglátja az `'INFO Finished starting connectors and tasks'` szöveget. 

> [!NOTE]
> Az Event Hubs lehetővé teszi a Kafka-ügyfelek számára a témakörök automatikus létrehozását. A névtérből az Azure Portalon gyorsan ki lehet deríteni, hogy a Connect-feldolgozó belső témakörei automatikusan jöttek létre.

### <a name="create-connectors"></a>Összekötők létrehozása
Ez a szakasz végigvezeti a FileStreamSource és a FileStreamSink összekötő elindításán. 

1. Hozzon létre egy könyvtárat a bemeneti és kimeneti adatfájloknak.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Hozzon létre két fájlt: egyet a kiindulási adatokkal, amelyből a FileStreamSource összekötő olvas, és egy másikat, amelybe a FileStreamSink összekötő ír.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Hozzon létre egy FileStreamSource összekötőt.  Ne felejtse el lecserélni a kapcsos zárójelbe zárt kifejezést a kezdőkönyvtár elérési útjára.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    A fenti parancs futtatása után a `connect-quickstart` eseményközpontnak meg kell jelennie az Event Hubs-példányban.
4. Ellenőrizze a forrásösszekötő állapotát.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Igény szerint a [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases) is ellenőrizheti, hogy beérkeztek-e az események a `connect-quickstart` témakörbe.

5. Hozzon létre egy FileStreamSink összekötőt.  Most se felejtse el lecserélni a kapcsos zárójelbe zárt kifejezést a kezdőkönyvtár elérési útjára.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Ellenőrizze a fogadó összekötő állapotát.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Ellenőrizze, hogy az adatok replikálva lettek-e a fájlok között, és hogy a két fájl adatai azonosak-e.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Felesleges tartalmak törlése
A Kafka Connect olyan eseményközpont-témaköröket hoz létre a konfigurációk, eltolások és állapotok tárolására, amelyek a Connect-fürt leállítása után is megmaradnak. Ha nem kívánja megőrizni ezeket, javasoljuk, hogy törölje a témaköröket. A bemutató során létrehozott `connect-quickstart` eseményközpontot is érdemes törölni.

## <a name="next-steps"></a>További lépések

Az Event Hubsszal és a Kafkához készült Event Hubsszal kapcsolatos további információkért tekintse át a következő témaköröket:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Apache Kafkához készült Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy Kafka-kompatibilis eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Az Apache Spark csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [Az Akka Streams csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)