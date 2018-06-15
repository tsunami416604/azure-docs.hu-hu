---
title: Apache Kafka használata a HDInsight az Azure IoT Hub |} Microsoft Docs
description: Útmutató Apache Kafka használata a HDInsight az Azure IoT-központot. A Kafka csatlakozás Azure IoT Hub projekt Kafka biztosít a forrás és a fogadó összekötő. A forrás-összekötő segítségével adatokat olvasni az IoT-központ, és a fogadó összekötő írja az IoT-központot.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/15/2018
ms.author: larryfr
ms.openlocfilehash: 33fdb5b099efc40fec94a860b21cda75ced44fe9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267494"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Apache Kafka használata a HDInsight az Azure IoT hubbal

Ismerje meg, hogyan használható a [Kafka csatlakozás Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) összekötő áthelyezni az adatokat a HDInsight Apache Kafka és Azure IoT-központ között. Ebből a dokumentumból megismerheti, hogyan az IoT Hub-összekötő a fürt egy élcsomópontot-ről futtatva.

A Kafka csatlakozás API lehetővé teszi, amely folyamatosan olvasnak be adatokat az Kafka, illetve adatokat küldeni a Kafka másik rendszerbe összekötők végrehajtásához. A [Kafka csatlakozás Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) egy összekötő, amely alapján tölti ki adatokat az Azure IoT Hub Kafka. Azt is is adatokat küldeni a Kafka az IoT hubhoz. 

Amikor húzza az IoT hubból, használhatja a __forrás__ összekötő. Amikor kérdez le, hogy az IoT-központ, használhatja a __fogadó__ összekötő. Az IoT Hub-összekötő a forrás- és fogadó összekötőket biztosít.

Az alábbi ábrán látható a HDInsight az Azure IoT Hub és Kafka közötti adatáramlás, az összekötő használata esetén.

![Az összekötőn keresztül halad az IoT-központ Kafka adatok jelennek meg kép](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Csatlakozás API további információkért lásd: [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* A HDInsight-fürt Kafka. További információkért lásd [a Kafka on HDInsight használatába bevezető](apache-kafka-get-started.md) dokumentumot.

* A Kafka fürt egy élcsomópontot. További információkért lásd: a [peremhálózati csomópontok használata a HDInsight](../hdinsight-apps-use-edge-node.md) dokumentum.

* Az Azure IoT-központ. A jelen oktatóanyag esetében javasolt a [online szimulátor málna Pi csatlakozás Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) dokumentum.

* Egy SSH-ügyfél. A jelen dokumentum lépései SSH használatával csatlakoznak a fürthöz. További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Az összekötő telepítése

1. Töltse le a legújabb kiadása a Kafka csatlakozzon az Azure IoT Hubjának [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Töltse fel a .jar fájlt a HDInsight-fürt Kafka élcsomópont, használja a következő parancsot:

    > [!NOTE]
    > Cserélje le `sshuser` a HDInsight-fürthöz az SSH a felhasználói fiókkal. Cserélje le `new-edgenode` peremhálózati csomópont névvel. Cserélje le `clustername` a fürt nevéhez. Élcsomópont SSH végpontja további információkért tekintse meg a [peremhálózati csomópontok használt HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node) dokumentum.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. A fájl másolása után csatlakozás az élcsomóponthoz SSH használatával:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Az összekötő telepítéséhez be a Kafka `libs` directory, a következő paranccsal:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Ha problémát tapasztal a jelen dokumentumban leírt lépések többi előre elkészített .jar fájl, próbálja meg a csomag forrás felépítése.
>
> Az összekötő létrehozásához rendelkeznie kell egy Scala fejlesztési környezet a [Scala build eszköz](http://www.scala-sbt.org/).
>
> 1. Letöltheti a forráskódot, az összekötő [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) a fejlesztői környezetében.
>
> 2. A parancssorban a projekt könyvtárában a következő paranccsal lehet létrehozni, és a projekt csomag:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Ez a parancs létrehoz egy nevű fájlt `kafka-connect-iothub-assembly_2.11-0.6.jar` a a `target/scala-2.11` könyvtárat a projekthez.

## <a name="configure-kafka"></a>Kafka konfigurálása

Az SSH-kapcsolat az élcsomóponthoz, az az alábbi lépések segítségével konfigurálja az összekötőt futtató önálló módban Kafka:

1. Mentse a fürt nevét egy változót. A változó segítségével könnyebben másolja és illessze be a más parancsok ebben a szakaszban:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Telepítse a `jq` segédprogram. Ezt a segédprogramot megkönnyíti a JSON-dokumentumok Ambari lekérdezések által visszaadott feldolgozásához:

    ```bash
    sudo apt -y install jq
    ```

3. A Kafka címét brókerek beolvasása. Előfordulhatnak sok brókerek a fürtön, de csak hivatkoznia kell egy vagy két. Ahhoz, hogy a cím két broker gazdagépek, a következő paranccsal:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát. A visszaadott érték az alábbi szöveghez hasonló:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. A Zookeeper csomópontok címet kap. A fürt több Zookeeper csomópontok szerepelnek, de csak hivatkoznia kell egy vagy két. Ahhoz, hogy a két Zookeeper csomópontok címét, a következő paranccsal:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Ha az összekötő önálló módban a `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` fájllal a Kafka brókerek kommunikálni. A módosítandó a `connect-standalone.properties` fájlt, a következő paranccsal:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Az önálló konfigurációban, a peremhálózati csomópont található a Kafka brókerek konfigurálásához található kezdődő sort `bootstrap.servers=`. Cserélje le a `localhost:9092` érték az előző lépésben a broker állomással.

    * Módosítsa a `key.converter=` és `value.converter=` sorok a következő értékeket:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Ez a változás tesztelése a konzol termelő Kafka mellékelt használatával teszi lehetővé. Szükség lehet különböző más gyártó és a felhasználó számára. Az egyéb konverter értékek használatával további információkért lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Adja hozzá a sort a fájl, amely tartalmazza a következő szöveg végén:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Ez a változás célja a fogadó Connector időtúllépések, ha egyszerre korlátozza 10 rögzíti. Tovább információ: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

7. A témakörök az összekötő által használt létrehozásához használja a következő parancsokat:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Annak ellenőrzésére, hogy a `iotin` és `iotout` témakörök létezik, a következő paranccsal:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    A `iotin` témakör üzenetek fogadása az IoT-központ szolgál. A `iotout` témakör üzeneteket küldhet az IoT-központ szolgál.

## <a name="get-iot-hub-connection-information"></a>Az IoT-központ kapcsolati adatok

Az összekötő által használt IoT hub információk lekéréséhez használja a következő lépéseket:

1. Az Event Hub-kompatibilis végpont és az IoT hub Event Hub-kompatibilis végpontnév kapják meg. Ezt az információt, használja a következő módszerek egyikét:

    * __Az a [Azure-portálon](https://portal.azure.com/)__, kövesse az alábbi lépéseket:

        1. Az IoT Hub keresse meg és jelölje ki __végpontok__.
        2. A __beépített végpontok__, jelölje be __események__.
        3. A __tulajdonságok__, másolja a következő mezők értékét:

            * __Event Hub-kompatibilis neve__
            * __Event Hub-kompatibilis végpont__
            * __Partíciók__

        > [!IMPORTANT]
        > A végpont értékének a portálról, amely ebben a példában nincs szükség további szöveg tartalmazhat. A szöveg, amely megfelel a mintának `sb://<randomnamespace>.servicebus.windows.net/`.

    * __Az a [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, használja a következő parancsot:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Cserélje le `myhubname` az IoT hub nevét. A válasz a következő szöveg hasonlít:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Beolvasása a __megosztott hozzáférési házirend__ és __kulcs__. Ebben a példában használja a __szolgáltatás__ kulcs. Ezt az információt, használja a következő módszerek egyikét:

    * __Az a [Azure-portálon](https://portal.azure.com/)__, kövesse az alábbi lépéseket:

        1. Válassza ki __megosztott elérési házirendek__, majd válassza ki __szolgáltatás__.
        2. Másolás a __elsődleges kulcs__ érték.
        3. Másolás a __kapcsolati karakterlánc – elsődleges kulcs__ érték.

    * __Az a [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, használja a következő parancsot:

        1. Ahhoz, hogy az elsődleges kulcs értéke, a következő paranccsal:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Cserélje le `myhubname` az IoT hub nevét. Az elsődleges kulcsot a rendszer ezt a választ a `service` a központ házirend.

        2. A kapcsolati karakterláncot lekérni a `service` házirend, használja a következő parancsot:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Cserélje le `myhubname` az IoT hub nevét. A rendszer a választ, a kapcsolati karakterláncot a `service` házirend.

## <a name="configure-the-source-connection"></a>Az adatforrás-kapcsolat konfigurálása

Az IoT Hub használható forrás konfigurálásához hajtsa végre az SSH-kapcsolat a következő műveletek élcsomópont:

1. Másolatot készít a `connect-iot-source.properties` fájlt a `/usr/hdp/current/kafka-broker/config/` könyvtár. Töltse le a fájlt a toketi-kafka-csatlakozás-IOT hubbal projekt, használja a következő parancsot:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. A módosítandó a `connect-iot-source.properties` fájl és az IoT hub-információk hozzáadása, a következő paranccsal:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    -Szerkesztőben keresse meg és módosíthatja az alábbi bejegyzéseket:

    * `Kafka.Topic=PLACEHOLDER`: A csere `PLACEHOLDER` rendelkező `iotin`. Az IoT-központ érkező üzenetek kerülnek be a `iotin` témakör.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`: A csere `PLACEHOLDER` Event Hub-kompatibilis névvel.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: A csere `PLACEHOLDER` az Event Hub-kompatibilis végponttal.
    * `IotHub.Partitions=PLACEHOLDER`: A csere `PLACEHOLDER` az előző lépést a partíciók számát.
    * `IotHub.AccessKeyName=PLACEHOLDER`: A csere `PLACEHOLDER` rendelkező `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`: A csere `PLACEHOLDER` az elsődleges kulccsal rendelkező a `service` házirend.
    * `IotHub.StartType=PLACEHOLDER`: A csere `PLACEHOLDER` az UTC-dátum. Ez a dátum az üzenetek ellenőrzése a connector indításakor. A dátum formátum `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`: A csere `100` rendelkező `5`. A módosítás következtében az összekötő olvashatja be Kafka után az IoT-központ öt új üzeneteket.

    Egy példa konfiguráció, lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. A módosítások mentéséhez használjon __Ctrl + X__, __Y__, majd __Enter__.

Az összekötő forrása konfigurálásáról további információkért lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>A fogadó csatlakozási konfigurálása

A fogadó csatlakozási dolgozni az IoT Hub konfigurálásához hajtsa végre az SSH-kapcsolat a következő műveletek élcsomópont:

1. Másolatot készít a `connect-iothub-sink.properties` fájlt a `/usr/hdp/current/kafka-broker/config/` könyvtár. Töltse le a fájlt a toketi-kafka-csatlakozás-IOT hubbal projekt, használja a következő parancsot:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. A módosítandó a `connect-iothub-sink.properties` fájl és az IoT hub-információk hozzáadása, a következő paranccsal:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    -Szerkesztőben keresse meg és módosíthatja az alábbi bejegyzéseket:

    * `topics=PLACEHOLDER`: A csere `PLACEHOLDER` rendelkező `iotout`. Üzenetek `iotout` a témakör a rendszer továbbítja az IoT-központot.
    * `IotHub.ConnectionString=PLACEHOLDER`: A csere `PLACEHOLDER` együtt a kapcsolati karakterláncot a `service` házirend.

    Egy példa konfiguráció, lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. A módosítások mentéséhez használjon __Ctrl + X__, __Y__, majd __Enter__.

Az összekötő fogadó konfigurálásával kapcsolatos további információkért lásd: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Indítsa el az adatforrás-összekötő

A forrás connector indításához az élcsomóponthoz az SSH-kapcsolat a következő parancs használható:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Miután elindul az összekötőt, üzenetek küldése az IoT-központ az eszközöket a. Az összekötő olvassa be az üzenetet az IoT-központot, és tárolja őket a Kafka témakör, információkat naplózza a konzolhoz:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> Az összekötő indulásakor több figyelmeztetések jelenhetnek meg. Ezek a figyelmeztetések nem üzenetek fogadása az IoT-központ problémákat okozhat.

Az összekötő leállításához használja __Ctrl + C__.

## <a name="start-the-sink-connector"></a>Indítsa el a fogadó összekötő

Az SSH-kapcsolat az élcsomóponthoz a önálló módban indul el, a fogadó összekötő alkalmazás a következő parancsot:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Mivel az összekötő fut, az alábbihoz hasonló információk jelennek meg:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> Azt tapasztalhatja, hogy a program több figyelmeztetés, az összekötő indításakor. Ezek biztonságosan figyelmen kívül hagyhatók.

Az összekötőn keresztül küldéséhez, tegye a következőket:

1. Nyisson meg egy másik, a Kafka fürthöz SSH-munkamenetet:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Az üzenetek küldését a `iotout` témakör, használja a következő parancsot:

    > [!WARNING]
    > Mivel ez egy új SSH-kapcsolat a `$KAFKABROKERS` változó nem tartalmaz adatokat. Állítsa be, a következő módszerek valamelyikével:
    >
    > * Az első három lépésekkel a [konfigurálása Kafka](#configure-kafka) szakasz.
    > * Használjon `echo $KAFKABROKERS` az előző SSH-kapcsolat az értékeket, és akkor cserélje le a `$KAFKABROKERS` az alábbi parancs a tényleges értékeket.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Ez a parancs nem ad vissza, a normál Bash parancssor. Ehelyett a billentyűzetről küldi el a `iotout` témakör.

3. Egy üzenetet küldeni az eszközén, illessze be a JSON-dokumentum az SSH-munkamenetet a `kafka-console-producer`.

    > [!IMPORTANT]
    > Meg kell adni az értékét a `"deviceId"` bejegyzést az eszköz azonosítója. A következő példában az eszköz neve `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    A JSON-dokumentum schema részletesebb magyarázatért ismertetett [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    A szimulált málna Pi-eszközt használ, és fut-e, ha a rendszer az eszköz naplózza a következő üzenetet:

    ```text
    Receive message: Turn On
    ```

    Küldje el újból a JSON-dokumentum, de a értékének módosítása a `"message"` bejegyzés. Az eszköz által naplózott, az új érték.

A fogadó connector használatáról további információk: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulhatta csatlakozás Kafka API-t használó a HDInsight az IoT-Kafka összekötő indítására. Az alábbi hivatkozások segítségével felderíteni a más módon történő együttműködésre Kafka:

* [Az Apache Spark használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)
