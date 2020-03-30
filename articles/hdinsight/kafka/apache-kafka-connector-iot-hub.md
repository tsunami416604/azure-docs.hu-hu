---
title: Az Apache Kafka használata a HDInsighton az Azure IoT Hubbal
description: Ismerje meg, hogyan használhatja az Apache Kafka-t a HDInsight-on az Azure IoT Hubbal. A Kafka Connect Azure IoT Hub projekt forrás- és fogadóösszekötőt biztosít a Kafka számára. A forrásösszekötő adatokat olvashat az IoT Hubról, és a fogadóösszekötő az IoT Hubba ír.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 48a72b5ba3819712b9e1d2536ae2dd3a06eaf3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238819"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Az Apache Kafka használata a HDInsighton az Azure IoT Hubbal

Ismerje meg, hogyan használhatja az [Apache Kafka Connect Azure IoT](https://github.com/Azure/toketi-kafka-connect-iothub) Hub-összekötőt az Apache Kafka közötti adatok áthelyezéséhez a HDInsight és az Azure IoT Hub szolgáltatásban. Ebben a dokumentumban megtudhatja, hogyan futtathatja az IoT Hub-összekötőt a fürt egy peremhálózati csomópontjáról.

A Kafka Connect API lehetővé teszi olyan összekötők megvalósítását, amelyek folyamatosan adatokat adnak le a Kafkába, vagy adatokat aDnak le a Kafkából egy másik rendszerbe. Az [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) egy olyan összekötő, amely adatokat szed le az Azure IoT Hubról a Kafkába. A Kafka adatokat is leküldéses az IoT Hub.

Amikor lehúzaz IoT Hub, __egy forrásösszekötőt__ használ. Az IoT Hub lenyomásakor egy __fogadóösszekötőt__ használ. Az IoT Hub-összekötő biztosítja a forrás- és a fogadóösszekötőket is.

Az alábbi ábrán az Azure IoT Hub és a Kafka közötti adatfolyam látható a HDInsight-on az összekötő használatakor.

![Az IoT Hubról a Kafka-ba az összekötőn keresztül áramló adatokat megjelenítő kép](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

A Connect API-val kapcsolatos [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)további információkért lásd: .

## <a name="prerequisites"></a>Előfeltételek

* Apache Kafka-fürt a HDInsighton. További információkért lásd [a Kafka on HDInsight használatába bevezető](apache-kafka-get-started.md) dokumentumot.

* A Kafka-fürt egyik peremcsomópontja. További információt a [Peremhálózati csomópontok használata HDInsight-dokumentummal](../hdinsight-apps-use-edge-node.md) című témakörben talál.

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

* Egy Azure IoT Hub és az eszköz. Ebben a cikkben fontolja meg a [Connect Raspberry Pi online szimulátor az Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started)használatával.

* [Scala épít szerszám](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Az összekötő megépítése

1. Töltse le az összekötő [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) forrását a helyi környezetbe.

2. A parancssorból keresse `toketi-kafka-connect-iothub-master` meg a könyvtárat. Ezután a következő paranccsal hozlétre és csomagolja a projektet:

    ```cmd
    sbt assembly
    ```

    A build néhány percet vesz igénybe. A parancs létrehoz `kafka-connect-iothub-assembly_2.11-0.7.0.jar` egy `toketi-kafka-connect-iothub-master\target\scala-2.11` fájlt, amelyet a projekt könyvtárában neveztek el.

## <a name="install-the-connector"></a>A csatlakozó telepítése

1. Töltse fel a .jar fájlt a Kafka szélén a HDInsight-fürtön. Az alábbi parancs szerkesztése a fürt tényleges nevének cseréjével. `CLUSTERNAME` Az SSH felhasználói fiók alapértelmezett értékeit és a [peremhálózati csomópont](../hdinsight-apps-use-edge-node.md#access-an-edge-node) nevét az alábbiakban használjuk, szükség szerint módosítva.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Miután a fájlmásolás befejeződött, ssh használatával csatlakozzon a peremcsomóponthoz:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az összekötő Kafka `libs` könyvtárba való telepítéséhez használja a következő parancsot:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Tartsa aktívan az SSH-kapcsolatot a további lépésekhez.

## <a name="configure-apache-kafka"></a>Az Apache Kafka konfigurálása

Az SSH-kapcsolatról a peremhálózati csomópontra az alábbi lépésekkel konfigurálhatja a Kafka-t az összekötő önálló módban történő futtatásához:

1. Jelszóváltozó beállítása. Cserélje le a JELSZÓT a fürt bejelentkezési jelszavára, majd írja be a következő parancsot:

    ```bash
    export password='PASSWORD'
    ```

1. Telepítse a [jq](https://stedolan.github.io/jq/) segédprogramot. A jq megkönnyíti az Ambari-lekérdezésekből visszaadott JSON-dokumentumok feldolgozását. Írja be a következő parancsot:

    ```bash
    sudo apt -y install jq
    ```

1. Szerezd meg a Kafka brókerek címét. Lehet, hogy sok brókerek a fürtben, de csak akkor kell hivatkozni egy vagy két. Két brókerállomás címének levételéhez használja a következő parancsot:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Másolja az értékeket későbbi használatra. A visszaadott érték az alábbi szöveghez hasonló:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Az Apache Zookeeper-csomópontok címének beszerezése. A fürtben több Zookeeper-csomópont található, de csak egy vagy kettőre kell hivatkoznia. Használja a következő parancsot, hogy `KAFKAZKHOSTS`tárolja a címeket a változóban:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Ha az összekötőt önálló módban `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` futtatja, a fájl a Kafka brókerekkel való kommunikációra szolgál. A `connect-standalone.properties` fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. A következő módosításokat kell elkészíteni:

    |Aktuális érték |Új érték | Megjegyzés |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Cserélje `localhost:9092` le az értéket az előző lépés brókerállomásaira|Konfigurálja az önálló konfigurációt a peremhálózati csomópont hoz a Kafka brókerek.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Ez a módosítás lehetővé teszi, hogy tesztelje a konzol gyártó tartalmazza Kafka. Előfordulhat, hogy más gyártók és fogyasztók számára különböző feldolgozókra van szükség. Az egyéb konverterértékek [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)használatáról a témakörben talál további információt.|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Ugyanaz, mint fent.|
    |N/A|`consumer.max.poll.records=10`|Hozzáadás a fájl végéhez. Ez a módosítás megakadályozza az időtúllépésre a fogadó összekötő korlátozza, hogy 10 rekordok egy időben. További információ: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. A fájl mentéséhez használja a __Ctrl + X__, __Y__, majd az __Enter billentyűt.__

1. Az összekötő által használt témakörök létrehozásához használja a következő parancsokat:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Annak ellenőrzéséhez, hogy a `iotin` témakörök és `iotout` a témakörök léteznek-e, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    A `iotin` témakör az IoT Hubtól érkező üzenetek fogadására szolgál. A `iotout` témakör az IoT Hubba küldött üzenetek küldésére szolgál.

## <a name="get-iot-hub-connection-information"></a>Az IoT Hub-kapcsolat adatainak beszereznie

Az összekötő által használt IoT-központ adatainak lekéréséhez kövesse az alábbi lépéseket:

1. Az Event Hub-kompatibilis végpont és az Event Hub-kompatibilis végpont neve az IoT hub. Az információk beszerezéséhez használja az alábbi módszerek egyikét:

   * __Az [Azure Portalon](https://portal.azure.com/)__ az alábbi lépéseket kell követnie:

     1. Nyissa meg az IoT Hubot, és válassza __a Végpontok__lehetőséget.
     2. A __beépített végpontok közül__válassza az __Események__lehetőséget.
     3. A __Tulajdonságok forrásból__másolja a következő mezők értékét:

         * __Az Eseményközponttal kompatibilis név__
         * __Az Eseményközponttal kompatibilis végpont__
         * __Partíciók__

        > [!IMPORTANT]  
        > A portál végpontértéke tartalmazhat további szöveget, amely ebben a példában nem szükséges. Bontsa ki az `sb://<randomnamespace>.servicebus.windows.net/`ennek a mintának megfelelő szöveget.

   * __Az [Azure CLI-ből](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ használja a következő parancsot:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Cserélje `myhubname` le az IoT hub nevét. A válasz hasonló a következő szöveghez:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. A __megosztott hozzáférési szabályzat__ és a __kulcs__beszerezni. Ebben a példában használja a __szolgáltatáskulcsot.__ Az információk beszerezéséhez használja az alábbi módszerek egyikét:

    * __Az [Azure Portalon](https://portal.azure.com/)__ az alábbi lépéseket kell követnie:

        1. Válassza __a Megosztott hozzáférési házirendek__lehetőséget, majd a __service__lehetőséget.
        2. Másolja az __Elsődleges kulcs__ értékét.
        3. Másolja a __kapcsolati karakterláncot - elsődleges kulcs__ értéke.

    * __Az [Azure CLI-ből](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ használja a következő parancsot:

        1. Az elsődleges kulcs értékének levételéhez használja a következő parancsot:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Cserélje `myhubname` le az IoT hub nevét. A válasz a hub `service` házirendjének elsődleges kulcsa.

        2. A házirend kapcsolati `service` karakterláncának lekérnie, használja a következő parancsot:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Cserélje `myhubname` le az IoT hub nevét. A válasz a házirend `service` kapcsolati karakterlánca.

## <a name="configure-the-source-connection"></a>A forráskapcsolat konfigurálása

A forrás konfigurálásához az IoT Hub, hajtsa végre a következő műveleteket egy SSH-kapcsolat a peremhálózati csomópont:

1. Hozzon létre `connect-iot-source.properties` egy másolatot a fájlról a `/usr/hdp/current/kafka-broker/config/` könyvtárban. A fájl toketi-kafka-connect-iothub projektből való letöltéséhez használja a következő parancsot:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. A `connect-iot-source.properties` fájl szerkesztéséhez és az IoT hub adatainak hozzáadásához használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. A szerkesztőben keresse meg és módosítsa a következő bejegyzéseket:

    |Aktuális érték |Szerkesztés|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Cserélje le a `PLACEHOLDER` elemet a `iotin` kérdésre. Az IoT hubról érkező `iotin` üzenetek a témakörben találhatók.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Cserélje `PLACEHOLDER` le az Event Hub-kompatibilis nevet.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Cserélje `PLACEHOLDER` le az Event Hub-kompatibilis végpontra.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Cserélje le a `PLACEHOLDER` elemet a `service` kérdésre.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Cserélje `PLACEHOLDER` le a házirend `service` elsődleges kulcsára.|
    |`IotHub.Partitions=PLACEHOLDER`|Cserélje `PLACEHOLDER` le az előző lépésekből származó partíciók számával.|
    |`IotHub.StartTime=PLACEHOLDER`|Csere `PLACEHOLDER` UTC dátumra. Ez a dátum az, amikor az összekötő elkezdi ellenőrizni az üzeneteket. A dátumformátum `yyyy-mm-ddThh:mm:ssZ`a .|
    |`BatchSize=100`|Cserélje le a `100` elemet a `5` kérdésre. Ez a módosítás hatására az összekötő üzeneteket olvas fel a Kafka, ha öt új üzenet van az IoT hubban.|

    Egy példa konfiguráció, lásd: [Kafka Connect Source Connector for Azure IoT Hub.](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)

1. A módosítások mentéséhez használja __a Ctrl + X__, __Y__, majd az __Enter billentyűt.__

Az összekötő forrásának konfigurálásáról [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)további információt a.

## <a name="configure-the-sink-connection"></a>A fogadókapcsolat konfigurálása

A fogadókapcsolat konfigurálásához az IoT Hubbal való együttműködésre hajtsa végre a következő műveleteket egy SSH-kapcsolatból a peremhálózati csomópontra:

1. Hozzon létre `connect-iothub-sink.properties` egy másolatot a fájlról a `/usr/hdp/current/kafka-broker/config/` könyvtárban. A fájl toketi-kafka-connect-iothub projektből való letöltéséhez használja a következő parancsot:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. A `connect-iothub-sink.properties` fájl szerkesztéséhez és az IoT hub adatainak hozzáadásához használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. A szerkesztőben keresse meg és módosítsa a következő bejegyzéseket:

    |Aktuális érték |Szerkesztés|
    |---|---|
    |`topics=PLACEHOLDER`|Cserélje le a `PLACEHOLDER` elemet a `iotout` kérdésre. A témakörbe `iotout` írt üzeneteket a továbbítódnak az IoT hubra.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Cserélje `PLACEHOLDER` le a `service` házirend kapcsolati karakterláncára.|

    Egy példa konfiguráció, lásd: [Kafka Connect Sink Connector for Azure IoT Hub.](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)

1. A módosítások mentéséhez használja __a Ctrl + X__, __Y__, majd az __Enter billentyűt.__

Az összekötő fogadójának konfigurálásáról [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)további információt a.

## <a name="start-the-source-connector"></a>A forrásösszekötő indítása

1. A forrásösszekötő elindításához használja a következő parancsot egy SSH-kapcsolatból a peremhálózati csomópontra:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Az összekötő indítása után üzeneteket küldhet az IoT hubazon az eszköz(ök) számára. Miközben az összekötő beolvassa az Üzeneteket az IoT hubról, és tárolja őket a Kafka-témakörben, naplózza az adatokat a konzolra:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Az összekötő indításakor több figyelmeztetés is megjelenhet. Ezek a figyelmeztetések nem okoznak problémát az IoT hubról érkező üzenetek fogadásával.

1. Állítsa le a csatlakozót néhány perc múlva **a Ctrl + C** kétszeri használatával. A csatlakozó leállítása néhány percet vesz igénybe.

## <a name="start-the-sink-connector"></a>A mosogatóösszekötő indítása

Az SSH-kapcsolatról a peremhálózati csomópontra a következő paranccsal indítsa el a fogadóösszekötőt önálló módban:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Az összekötő futása közben a következő szöveghez hasonló információk jelennek meg:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Az összekötő indításakor több figyelmeztetést is tapasztalhat. Ezek biztonságosan figyelmen kívül hagyhatók.

## <a name="send-messages"></a>Üzenetek küldése

Ha üzeneteket szeretne küldeni az összekötőn keresztül, kövesse az alábbi lépéseket:

1. Nyisson *meg egy második* SSH-munkamenetet a Kafka-fürthöz:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Szerezd meg a címet a Kafka brókerek az új ssh ülésén. Cserélje le a JELSZÓT a fürt bejelentkezési jelszavára, majd írja be a következő parancsot:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Ha üzeneteket `iotout` szeretne küldeni a témakörnek, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Ez a parancs nem tér vissza a normál Bash parancssorba. Ehelyett billentyűzetbevitelt küld `iotout` a témakörnek.

1. Ha üzenetet szeretne küldeni a készülékre, illessze be a `kafka-console-producer`JSON-dokumentumot az SSH munkamenetbe a számára.

    > [!IMPORTANT]  
    > A `"deviceId"` bejegyzés értékét az eszköz azonosítójára kell beállítania. A következő példában az `myDeviceId`eszköz neve:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    A JSON-dokumentum sémáját részletesebben ismertetjük a következő helyen: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Ha a szimulált Raspberry Pi eszközt használja, és az fut, az eszköz a következő üzenetet naplózza:

    ```output
    Receive message: Turn On
    ```

    Küldje el újra a JSON-dokumentumot, de módosítsa a `"message"` bejegyzés értékét. Az új értéket az eszköz naplózza.

A mosogatóösszekötő használatáról további [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)információt a .

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan használhatja az Apache Kafka Connect API-t az IoT Kafka-összekötő indításához a HDInsighton. Az alábbi hivatkozásoksegítségével további módszereket fedezhet fel a Kafka használatára:

* [Az Apache Spark használata az Apache Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Storm használata az Apache Kafkával a HDInsighton](../hdinsight-apache-storm-with-kafka.md)
