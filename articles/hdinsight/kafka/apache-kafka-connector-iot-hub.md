---
title: Használat Apache Kafka on HDInsight az Azure IoT hubbal
description: Ismerje meg, hogyan használható az Apache Kafka a HDInsight az Azure IoT Hub. A Kafka csatlakoztatása Azure IoT Hub-projekt Kafka biztosít egy forrás és fogadó-összekötőt. A forrás-összekötő tudja olvasni az adatokat az IoT hubról, és a fogadó-összekötő az IoT Hub írja.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 143df8a8c82e84b193bdb48a3d41682fca19156b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52315427"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Használat Apache Kafka on HDInsight az Azure IoT hubbal

Ismerje meg, hogyan használható a [Apache Kafka csatlakoztatása Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) Apache Kafka on HDInsight és az Azure IoT Hub közötti összekötő. Ebből a dokumentumból megismerheti, hogyan az IoT Hub-összekötő a fürt egy élcsomópontot-ről futtatva.

A Kafka-csatlakozás API lehetővé válik, amely folyamatosan Kafka az adatok beolvasását, vagy a Kafkából adatok leküldése egy másik rendszer összekötők. A [Apache Kafka csatlakoztatása Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) egy összekötő, amely lekéri az adatokat az Azure IoT hubról, a Kafka. Azt is küldhet adatokat a Kafkából az IoT hubnak. 

Beolvasás az IoT hubról, amikor egy __forrás__ összekötő. Amikor leküldése az IoT Hub, használhatja a __fogadó__ összekötő. Az IoT Hub-összekötő a forrás- és fogadó összekötők biztosít.

Az alábbi ábrán látható az Azure IoT Hub és a Kafka közötti adatáramlás a HDInsight, az összekötő használatakor.

![A kép átmenő adatokat az IoT hubról kafka az összekötő](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

A csatlakozás API további információkért lásd: [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Egy Kafka on HDInsight-fürt. További információkért lásd [a Kafka on HDInsight használatába bevezető](apache-kafka-get-started.md) dokumentumot.

* A Kafka-fürt az élcsomóponthoz. További információkért lásd: a [élcsomópontok használata a HDInsight](../hdinsight-apps-use-edge-node.md) dokumentumot.

* Az Azure IoT hubra. A jelen oktatóanyag esetében javasolni tudom a [online szimulátor Raspberry Pi csatlakoztatása az Azure IoT hubra](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) dokumentumot.

* Egy SSH-ügyfél. A jelen dokumentum lépései SSH használatával csatlakoznak a fürthöz. További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Az összekötő telepítése

1. Töltse le a legújabb kiadását a Kafka csatlakoztatása az Azure IoT hub [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. A .JAR kiterjesztésű fájlt tölthet fel a Kafka on HDInsight-fürt határcsomópontján, használja a következő parancsot:

    > [!NOTE]
    > Cserélje le `sshuser` a HDInsight-fürthöz az SSH felhasználói fiókkal. Cserélje le `new-edgenode` peremhálózati csomópont nevét. Cserélje le `clustername` a fürt nevére. Az élcsomópont SSH-végpontjának további információkért lásd: a [élcsomópontok használt HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node) dokumentumot.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. A fájlmásolás befejezése után csatlakozzon az élcsomóponthoz SSH-val:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Az összekötő telepítéséhez, a Kafka `libs` könyvtár, használja a következő parancsot:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Ha hibákba ütközne a jelen dokumentumban leírt lépésekben a többi előre elkészített .jar fájl használatakor, próbaképpen az adatforrás-csomagot.
>
> Az összekötők létrehozásához rendelkeznie kell egy Scala fejlesztési környezetet a a [Scala hozhat létre az eszköz](http://www.scala-sbt.org/).
>
> 1. Letöltheti a forráskódot, az összekötő [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) a fejlesztői környezetbe.
>
> 2. Egy parancssorból – a projekt könyvtárában a következő paranccsal hozhat létre, és a csomagot a projekthez:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Ez a parancs létrehoz egy fájlt `kafka-connect-iothub-assembly_2.11-0.6.jar` a a `target/scala-2.11` könyvtárat a projekthez.

## <a name="configure-apache-kafka"></a>Az Apache Kafka konfigurálása

Az élcsomóponthoz SSH-kapcsolatot a következő lépések használatával konfigurálja az összekötőt futtató önálló módban Kafka:

1. Mentse a fürt nevét egy változóban. Egy változó használatával könnyedén másolja és illessze be a többi parancs ebben a szakaszban:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Telepítse a `jq` segédprogramot. Ez a segédprogram megkönnyíti az Ambari lekérdezések által visszaadott JSON-dokumentumok feldolgozása:

    ```bash
    sudo apt -y install jq
    ```

3. A Kafka-címének lekéréséhez közvetítők. Előfordulhat, számos közvetítők a fürtben, de csak hivatkoznia kell egy vagy két. Két közvetítő állomásból-címének lekéréséhez használja a következő parancsot:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát. A visszaadott érték az alábbi szöveghez hasonló:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Az Apache Zookeeper-csomópontok-címének lekéréséhez. Több Zookeeper-csomópontok a fürtben, de csak hivatkoznia kell egy vagy két. Két Zookeeper-csomópontok-címének lekéréséhez használja a következő parancsot:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Ha az összekötőt futtató önálló módban a `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` fájl szolgál, a Kafka-közvetítőkhöz folytatott kommunikációhoz. Szerkesztheti a `connect-standalone.properties` fájlt, használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Az önálló konfigurációban a Kafka-közvetítők található az élcsomópont konfigurálásához, keresse meg a sort, kezdődő `bootstrap.servers=`. Cserélje le a `localhost:9092` a közvetítő gazdagépek az előző lépésben szereplő értéket.

    * Módosítsa a `key.converter=` és `value.converter=` sorokat, a következő értékeket:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Ez a változás lehetővé teszi, hogy tesztelje az konzol előállítói, a Kafka tartalmazza. Szükség lehet különböző más előállítók és fogyasztók számára. A többi converter értékek információkért lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Adjon hozzá egy sort a következő szöveget tartalmazó fájl végén:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Ez a változás, hogy a fogadó Connector időtúllépések megakadályozása, ha egyszerre 10 rekordok korlátozza. Tovább információ: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

7. Az összekötő által használt a témakörök létrehozásához használja a következő parancsokat:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Ellenőrizze, hogy a `iotin` és `iotout` témakörök létezik, a következő paranccsal:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    A `iotin` témakör segítségével üzenetek fogadása az IoT hubról. A `iotout` témakör üzenetek küldése az IoT Hub segítségével.

## <a name="get-iot-hub-connection-information"></a>Az IoT Hub kapcsolati adatok lekéréséhez

Az összekötő által használt IoT hub információk lekéréséhez használja az alábbi lépéseket:

1. Töltse le az Event Hub-kompatibilis végpont és az IoT hub Event Hub-kompatibilis végpont neve. Ezek az információk lekéréséhez használja a következő módszerek egyikét:

    * __Az a [az Azure portal](https://portal.azure.com/)__, kövesse az alábbi lépéseket:

        1. Keresse meg az IoT hubot és jelölje ki __végpontok__.
        2. A __beépített végpontokról__válassza __események__.
        3. A __tulajdonságok__, másolja a következő mezők értékét:

            * __Event Hub-kompatibilis név__
            * __Event Hub-kompatibilis végpont__
            * __Partíciók__

        > [!IMPORTANT]
        > A végpont értékét a Portalról, amely ebben a példában nincs szükség további szöveg tartalmazhat. Csomagolja ki, amely megfelel ennek a mintának a szöveg `sb://<randomnamespace>.servicebus.windows.net/`.

    * __Az a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, használja a következő parancsot:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Cserélje le `myhubname` az IoT hub nevét. A válasz az alábbi szöveghez hasonlít:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Első a __megosztott hozzáférési szabályzat__ és __kulcs__. Ebben a példában használja a __szolgáltatás__ kulcsot. Ezek az információk lekéréséhez használja a következő módszerek egyikét:

    * __Az a [az Azure portal](https://portal.azure.com/)__, kövesse az alábbi lépéseket:

        1. Válassza ki __megosztott elérési házirendek__, majd válassza ki __szolgáltatás__.
        2. Másolás a __elsődleges kulcs__ értéket.
        3. Másolás a __kapcsolati karakterlánc – elsődleges kulcs__ értéket.

    * __Az a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, használja a következő parancsot:

        1. Az elsődleges kulcs-érték lekéréséhez használja a következő parancsot:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Cserélje le `myhubname` az IoT hub nevét. A válasz az elsődleges kulcsot, hogy a `service` szabályzatot ehhez a hubhoz.

        2. Kapcsolati karakterláncára beolvasni a `service` házirend, használja a következő parancsot:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Cserélje le `myhubname` az IoT hub nevét. A válasz a kapcsolati karakterláncát a `service` házirend.

## <a name="configure-the-source-connection"></a>Az adatforrás-kapcsolat konfigurálása

A forrás az IoT hubbal való konfigurálásához hajtsa végre az SSH-kapcsolatot az alábbi műveleteket az élcsomóponthoz:

1. Másolatot készít a `connect-iot-source.properties` fájlt a `/usr/hdp/current/kafka-broker/config/` könyvtár. Töltse le a fájlt a toketi-kafka-connect-iothub-projektjéből, használja a következő parancsot:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Szerkesztheti a `connect-iot-source.properties` fájlt és az IoT hub-információk hozzáadása, használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    A szerkesztőben keresse meg és módosíthatja az alábbi bejegyzéseket:

    * `Kafka.Topic=PLACEHOLDER`: A csere `PLACEHOLDER` a `iotin`. Az IoT hub felől fogadott üzenetek kerülnek a `iotin` témakör.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`: A csere `PLACEHOLDER` Event Hub-kompatibilis névvel.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: A csere `PLACEHOLDER` az Event Hub-kompatibilis végponthoz.
    * `IotHub.Partitions=PLACEHOLDER`: A csere `PLACEHOLDER` az előző lépésekben a partíciók számát.
    * `IotHub.AccessKeyName=PLACEHOLDER`: A csere `PLACEHOLDER` a `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`: A csere `PLACEHOLDER` az elsődleges kulccsal rendelkező a `service` házirend.
    * `IotHub.StartType=PLACEHOLDER`: A csere `PLACEHOLDER` az UTC-dátum. Dátum, az üzenetek ellenőrzése az összekötő indításakor. A dátum formátuma `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`: A csere `100` a `5`. Ez a változás az után, ha öt új üzeneteket az IoT hub üzenetek olvasásához, a Kafka-összekötő okoz.

    Egy példa konfiguráció látható, lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. A módosítások mentéséhez használja __Ctrl + X__, __Y__, majd __Enter__.

Csatlakozó zdrojem konfigurálásával kapcsolatos további információkért lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>A fogadó-kapcsolat konfigurálása

A fogadó csatlakozási az IoT hubbal való konfigurálásához hajtsa végre az SSH-kapcsolatot az alábbi műveleteket az élcsomóponthoz:

1. Másolatot készít a `connect-iothub-sink.properties` fájlt a `/usr/hdp/current/kafka-broker/config/` könyvtár. Töltse le a fájlt a toketi-kafka-connect-iothub-projektjéből, használja a következő parancsot:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Szerkesztheti a `connect-iothub-sink.properties` fájlt és az IoT hub-információk hozzáadása, használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    A szerkesztőben keresse meg és módosíthatja az alábbi bejegyzéseket:

    * `topics=PLACEHOLDER`: A csere `PLACEHOLDER` a `iotout`. Üzenetek `iotout` a témakör a rendszer továbbítja az IoT hubnak.
    * `IotHub.ConnectionString=PLACEHOLDER`: A csere `PLACEHOLDER` kapcsolati karakterláncára az `service` házirend.

    Egy példa konfiguráció látható, lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. A módosítások mentéséhez használja __Ctrl + X__, __Y__, majd __Enter__.

Az összekötő fogadó konfigurálásával kapcsolatos további információkért lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Indítsa el a forrás-összekötő

A forrás-összekötő indításához használja az élcsomóponthoz SSH-kapcsolatot a következő parancsot:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Miután elindult az összekötőt, üzeneteket küldhet az IoT hubhoz a az eszközt. Az összekötő üzeneteket olvas az IoT hubot, és tárolja őket a Kafka-témakörbe, információkat naplózza a konzolhoz:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> Az összekötő indulásakor több figyelmeztetések jelenhetnek meg. Ezeket a figyelmeztetéseket nem okoz problémát az üzenetek fogadása az IoT hubról.

Az összekötő leállításához használja __Ctrl + C__.

## <a name="start-the-sink-connector"></a>Indítsa el a fogadó-összekötő

Alkalmazás a következő parancsot az élcsomóponthoz SSH-kapcsolatot, a fogadó-összekötő indítása önálló módban:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Az összekötő futása az alábbi szöveghez hasonló információt jelenik meg:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> Több figyelmeztetést, Észreveheti, az összekötő indításakor. Ezek biztonságosan figyelmen kívül hagyhatók.

Üzenetküldés az összekötőn keresztül, használja az alábbi lépéseket:

1. Nyisson meg egy másik, a Kafka-fürt az SSH-munkamenetből:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Üzenetek küldése az a `iotout` témakörben, használja a következő parancsot:

    > [!WARNING]
    > Mivel ez egy új SSH-kapcsolatot a `$KAFKABROKERS` információt nem tartalmaz változót. Állítsa be, használja az alábbi módszerek egyikét:
    >
    > * Első három kövesse a [konfigurálása az Apache Kafka](#configure-apache-kafka) szakaszban.
    > * Használat `echo $KAFKABROKERS` az előző SSH-kapcsolatot értékének lekéréséhez, és cserélje a `$KAFKABROKERS` tényleges értéke alapján a következő parancsban.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Ez a parancs nem ad vissza, a normál Bash parancssor. Ehelyett bevitelt a billentyűzetről, elküldi a `iotout` témakör.

3. Egy üzenet küldéséhez az eszközt, JSON-dokumentumok másolja át az SSH-munkamenetet a `kafka-console-producer`.

    > [!IMPORTANT]
    > Be kell értékét a `"deviceId"` bejegyzést az eszköz azonosítója. A következő példában az eszköz neve `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    A séma a JSON-dokumentum részletesebb magyarázatért leírt [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    A szimulált Raspberry Pi-eszközt használ, és fut-e, ha az eszköz által rögzíti a következő üzenetet:

    ```text
    Receive message: Turn On
    ```

    Küldje el újra a JSON-dokumentum, de módosítsa a `"message"` bejegyzés. Az új értéket az eszköz által naplózott.

A fogadó-összekötő használatával további információkért lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtudhatta, hogyan indítsa el az IoT-összekötő Kafka a HDInsight az Apache Kafka-csatlakozás API használatával. Az alábbi hivatkozások segítségével Fedezzen fel más módokat a Kafka használata:

* [Az Apache Kafka on HDInsight az Apache Spark használata](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Kafka on HDInsight az Apache Storm használata](../hdinsight-apache-storm-with-kafka.md)
