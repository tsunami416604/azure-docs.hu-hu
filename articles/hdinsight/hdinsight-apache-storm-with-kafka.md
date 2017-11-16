---
title: "Apache Kafka használata a HDInsight - Azure alatt futó Storm |} Microsoft Docs"
description: "Apache Kafka HDInsight alatt futó Apache Storm együtt települ. Megtudhatja, hogyan Kafka írni, és majd olvasni, a Storm megadott KafkaBolt és KafkaSpout összetevővel. Is megtudhatja, hogyan határozza meg, és küldje el a Storm-topológiák fluxus keretében segítségével."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 50a22877241c77ccb1a7df24ab7df006094a439f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>Apache Kafka használata a HDInsight alatt futó Storm

Útmutató: az olvasási és írási Apache Kafka alatt futó Apache Storm használatával. Ez a példa is mutatja be a HDFS-kompatibilis fájlrendszer használják a HDInsight a Storm-topológia adatainak mentése.

> [!NOTE]
> A jelen dokumentumban leírt lépések, amely a HDInsight alatt futó Storm, mind a HDInsight-fürt Kafka tartalmazza az Azure erőforrás-csoport létrehozása. Ezeken a fürtökön is egy Azure virtuális hálózatot, amely lehetővé teszi a Storm-fürt közvetlenül kommunikálni a Kafka belül található a fürtön.
> 
> Amikor elkészült, a jelen dokumentumban leírt lépések, ne felejtse el a felesleges költségek elkerülése érdekében a fürtök törlése.

## <a name="get-the-code"></a>A kód letöltése

Ebben a dokumentumban bemutatott példában kódja megtalálható [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Ez a projekt fordítása, a következő konfigurációs a fejlesztési környezet szüksége:

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) vagy újabb verzióját. HDInsight 3.5-ös vagy újabb rendszer szükséges Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Egy SSH-ügyfél (van szüksége a `ssh` és `scp` parancsok) - információkért lásd: [az SSH a Hdinsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

* Egy szövegszerkesztőben, vagy IDE.

Az alábbi környezeti változókat a fejlesztő munkaállomás Java és a JDK telepítésekor lehet beállítani. Azonban ellenőrizni kell, hogy léteznek, illetve a rendszer a megfelelő értékeket tartalmaz.

* `JAVA_HOME`-a a JDK mappáját kell mutatnia.
* `PATH`-a következő elérési utakat kell tartalmaznia:
  
    * `JAVA_HOME`(vagy ezzel egyenértékű elérési).
    * `JAVA_HOME\bin`(vagy ezzel egyenértékű elérési).
    * A mappát, ahová a Maven telepítve van.

## <a name="create-the-clusters"></a>A fürtök létrehozása

A HDInsight Apache Kafka nem férhet hozzá a Kafka brókerek a nyilvános interneten keresztül. A Kafka fürt csomópontja azonos Azure virtuális hálózaton, amelyeket a kiszolgálóhoz csatlakozik Kafka kell lennie. Ehhez a példához a Kafka és a Storm-fürtök egy Azure virtuális hálózat található. Az alábbi ábra bemutatja, hogyan kommunikációs a fürtök között zajló kommunikációról:

![A Storm és Kafka fürtök egy Azure virtuális hálózati diagramja](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Az interneten keresztül elérhető egyéb szolgáltatások, például az SSH és az Ambari a fürtön. A hdinsight eszközzel elérhető nyilvános portokon további információkért lásd: [portok és a HDInsight által használt URI-azonosítók](hdinsight-hadoop-port-settings-for-services.md).

Létrehozhat egy Azure virtuális hálózatra, Kafka, és a Storm-fürtök manuális, célszerűbb Azure Resource Manager sablonnal. Az alábbi lépések segítségével telepíthet egy Azure virtuális hálózatot, Kafka, és a Storm-fürtök az Azure-előfizetéshez.

1. A következő gomb segítségével jelentkezzen be az Azure-ba, és nyissa meg a sablon az Azure portálon.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Az Azure Resource Manager sablon itt található: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v2.json**. Létrehozza a következőket:
    
    * Azure-erőforráscsoportot
    * Azure Virtual Network
    * Azure Storage-fiók
    * A HDInsight 3.6 (három munkavégző csomópontokhoz) verzió Kafka
    * Verzió (három munkavégző csomópontokhoz) 3.6 HDInsight alatt futó Storm

  > [!WARNING]
  > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. Ez a sablon a három munkavégző csomópontokhoz tartalmaz Kafka fürtöt hoz létre.

2. Az alábbi útmutató segítségével a feltöltik a **egyéni telepítési** szakasz:
   
    ![HDInsight egyéni központi telepítés](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Erőforráscsoport**: hozzon létre egy csoportot, vagy válasszon egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürthöz.
   
    * **Hely**: Adjon meg egy földrajzilag Önhöz legközelebb eső helyet.

    * **Fürt neve kiinduló**: ezt az értéket használja, a Storm alapnevét és Kafka fürtök. Például írja be **hdi** hoz létre egy Storm-fürt nevű **storm-hdi** és nevű Kafka fürt **kafka-hdi**.
   
    * **A fürt bejelentkezési felhasználónevét**: A rendszergazda felhasználóneve a Storm és Kafka fürt.
   
    * **A fürt bejelentkezési jelszó**: a Storm és Kafka fürt rendszergazdai jelszóval.
    
    * **SSH-felhasználónév**: az SSH-felhasználó a Storm és Kafka fürtök létrehozása.
    
    * **SSH-jelszónak**: a Storm és Kafka fürt SSH-felhasználó jelszavát.

3. Olvassa el a **feltételek és kikötések**, majd válassza ki **elfogadom a feltételeket és a fenti feltételek**.

4. Végül ellenőrizze **rögzítés az irányítópulton** majd **beszerzési**. A fürt létrehozása nagyjából 20 percet vesz igénybe.

Az erőforrások létrehozása után, az erőforráscsoport szakasz jelenik meg.

![A virtuális hálózat és a fürt erőforrás csoport szakasz](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Figyelje meg, hogy a HDInsight-fürtök neve **storm-BASENAME** és **kafka-BASENAME**, ahol BASENAME a sablonhoz megadott név. Ezeket a neveket a későbbi lépésekben használja, a fürtök történő csatlakozás során.

## <a name="understanding-the-code"></a>A kód ismertetése

A projekt két topológiát tartalmaz:

* **KafkaWriter**: által meghatározott a **writer.yaml** fájl, ez a topológia ír véletlenszerű mondat Kafka az Apache Storm megadott KafkaBolt használatával.

    Ez a topológia használ egy egyéni **SentenceSpout** összetevő véletlenszerű mondat létrehozásához.

* **KafkaReader**: által meghatározott a **reader.yaml** fájl, ez a topológia Kafka az Apache Storm megadott KafkaSpout segítségével olvassa be az adatokat, majd az adatokat naplózza az stdout.

    Ez a topológia a Storm HdfsBolt adatokat írni az alapértelmezett tároló a Storm-fürt használja.
### <a name="flux"></a>Fluxus

A topológia meghatározása [fluxus](https://storm.apache.org/releases/1.1.0/flux.html). Fluxus bemutatott Storm 0.10.x, és lehetővé teszi a topológia konfigurációjával kód külön. A fluxus keretrendszert használó topológia esetén a topológia egy YAM fájlban definiálva van. A YAM fájl a topológia része lehet. A topológia elküldésekor használt önálló fájl is lehet. Fluxus futásidőben, ebben a példában használt változók behelyettesítését is támogatja.

Az alábbi topológiák futási időben vannak megadva a következő paraméterek:

* `${kafka.topic}`: A Kafka témakör, amely a topológiák olvasására vagy írására a neve.

* `${kafka.broker.hosts}`: A gazdagépek a Kafka brókerek futtatható. A broker információknak az alkalmazása által a KafkaBolt Kafka írásakor.

* `${kafka.zookeeper.hosts}`: A gazdagépeken futó Zookeeper a Kafka fürtben.

Fluxus topológiák további információkért lásd: [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="download-and-compile-the-project"></a>Töltse le és a projekt lefordítása

1. A fejlesztési környezetben, töltse le a projektet a [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), nyisson meg egy parancssori és abba a mappába, hogy a projekt letöltött.

2. Az a **hdinsight-storm-java-kafka** directory, a projekt lefordítása és központi telepítési csomagot hozhat létre a következő paranccsal:

  ```bash
  mvn clean package
  ```

    A csomag folyamat létrehoz egy fájlt nevű `KafkaTopology-1.0-SNAPSHOT.jar` a a `target` könyvtár.

3. A következő parancsok segítségével másolja a csomagot a Storm on HDInsight-fürt. Cserélje le **felhasználónév** rendelkező a fürthöz az SSH-felhasználónév. Cserélje le **BASENAME** a fürt létrehozásakor használt alap névvel.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Amikor a rendszer kéri, írja be a jelszót, a fürt létrehozásakor használt.

## <a name="configure-the-topology"></a>A topológia konfigurálása

1. Az alábbi módszerek valamelyikével felderítése a Kafka broker gazdagépek:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > A Bash példa feltételezi, hogy `$CLUSTERNAME` a HDInsight-fürt nevét tartalmazza. Azt is feltételezi, hogy [jq](https://stedolan.github.io/jq/) telepítve van. Amikor a rendszer kéri, adja meg a fürt bejelentkezési fiókjának jelszavát.

    A visszaadott érték az alábbihoz hasonló:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Előfordulhat, hogy a fürt legalább két broker gazdagépek, amíg nem kell minden gazdagép ügyfelek teljes listáját tartalmazzák. Egy vagy két is elegendő.

2. Az alábbi módszerek valamelyikével felderítése a Kafka Zookeeper gazdagépek:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > A Bash példa feltételezi, hogy `$CLUSTERNAME` a HDInsight-fürt nevét tartalmazza. Azt is feltételezi, hogy [jq](https://stedolan.github.io/jq/) telepítve van. Amikor a rendszer kéri, adja meg a fürt bejelentkezési fiókjának jelszavát.

    A visszaadott érték az alábbihoz hasonló:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Miközben kettőnél több Zookeeper csomópontok, nem kell minden gazdagép ügyfelek teljes listáját tartalmazzák. Egy vagy két is elegendő.

    Mentse ezt az értéket, a rendszer később.

3. Szerkessze a `dev.properties` fájlt a projekt gyökérkönyvtárában található. A Zookeeper és a gazdagép-adatokat hozzáadni a fájlban található egyező sor. A következő példa a minta értékeit az előző lépések segítségével konfigurálható:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Mentse a `dev.properties` fájlt, és a következő parancs használatával töltse fel azt a Storm-fürt:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
    ```

    Cserélje le **felhasználónév** rendelkező a fürthöz az SSH-felhasználónév. Cserélje le **BASENAME** a fürt létrehozásakor használt alap névvel.

## <a name="start-the-writer"></a>Az író indítása

1. A következő segítségével csatlakozzon a Storm fürthöz SSH használatával. Cserélje le **felhasználónév** az a fürt létrehozásakor használt SSH-felhasználónév. Cserélje le **BASENAME** a fürt létrehozásakor használt alap névvel.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    Amikor a rendszer kéri, írja be a jelszót, a fürt létrehozásakor használt.
   
    További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az SSH-kapcsolat létrehozásához használja a topológia Kafka témakör alkalmazás a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Cserélje le `$KAFKAZKHOSTS` a Zookeeper a gazdagép az előző szakaszban lekért információt.

2. Az a Storm fürthöz SSH-kapcsolat alkalmazás az író topológia indítsa el a következő parancsot:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Ezzel a paranccsal használt paraméterek a következők:

    * `org.apache.storm.flux.Flux`: A fluxus használja a konfigurálására és futtatására ebben a topológiában.

    * `--remote`: Küldje el a Nimbus topológiát. A topológia van elosztva a fürt munkavégző csomópontjaihoz.

    * `-R /writer.yaml`: Használja a `writer.yaml` fájl a topológiát. `-R`azt jelzi, hogy az erőforrás szerepel-e a jar-fájlra. A jar gyökerébe ezért van `/writer.yaml` elérési útja azt.

    * `--filter`: Bejegyzések feltöltése a `writer.yaml` topológiáját szereplő értékek a `dev.properties` fájlt. Például értékének a `kafka.topic` bejegyzés a fájlban cserélje le azt a `${kafka.topic}` topológia meghatározása bejegyzést.

5. Ha a topológia elindult, a következő paranccsal ellenőrizheti, hogy azt az írás a Kafka témakör:

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    Cserélje le `$KAFKAZKHOSTS` a Zookeeper a gazdagép az előző szakaszban lekért információt.

    Ez a parancs Kafka rendszerrel szállított parancsfájlt használ a figyelheti a témakör. Néhány percet, miután kell kezdenie véletlen a témakörbe írt mondatokat visszaadó. A kimenet a következő példához hasonló:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Használja a Ctrl + c leállítja a parancsfájlt.

## <a name="start-the-reader"></a>Indítsa el az olvasó

1. Az SSH-munkamenetet a Storm fürthöz alkalmazás az olvasó topológia indítsa el a következő parancsot:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Miután elindul a topológia, nyissa meg a Storm felhasználói felülete. Ez a webes felhasználói felület helye `https://storm-BASENAME.azurehdinsight.net/stormui`. Cserélje le __BASENAME__ a fürt létrehozásakor használt alap névvel. 

    Amikor a rendszer kéri, a rendszergazdai bejelentkezési név használata (alapértelmezés szerint `admin`) és a fürt létrehozásakor használt jelszót. Az alábbi képen hasonló webes lap jelenik meg:

    ![A Storm felhasználói felülete](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. A Storm felhasználói felülete, válassza ki a __kafka-olvasó__ hivatkozásra a __topológia összegzése__ kapcsolatos információk megjelenítéséhez a szakasz a __kafka-olvasó__ topológia.

    ![Topológia a Storm webes felhasználói felület összefoglaló szakasza](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Információt szeretne megjeleníteni a naplózó-bolt összetevő példánya, válassza ki a __naplózó-bolt__ hivatkozásra a __Boltokhoz (mindig)__ szakasz.

    ![A boltokhoz szakaszban naplózó-bolt-hivatkozás](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. Az a __végrehajtója__ területen válassza ki a tartalmaz egy hivatkozást a __Port__ oszlop naplózási kapcsolatos információk megjelenítéséhez ezt a példányt.

    ![Végrehajtója hivatkozás](./media/hdinsight-apache-storm-with-kafka/executors.png)

    A napló tartalmazza a Kafka témakör adatsorból beolvasott adatok naplózása. A naplóban szereplő információk hasonlít a következő szöveget:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>A topológia leállítása

A Storm fürthöz SSH-munkamenetet a Storm-topológiák leállításához alkalmazás a következő parancsokat:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

A jelen dokumentumban leírt lépések az azonos Azure erőforráscsoport mindkét fürtöket létrehozni, mert az erőforráscsoportot az Azure portálon törölheti. Az erőforráscsoport törlése eltávolítja a jelen dokumentum a következő által létrehozott összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a HDInsight alatt futó Storm használható további példa topológiák [példa Storm-topológiák és összetevők](storm/apache-storm-example-topology.md).

A telepítését és megfigyelését a HDInsight Linux-alapú topológiák további információkért lásd: [központi telepítése és kezelése a Linux-alapú HDInsight alatt futó Apache Storm-topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)