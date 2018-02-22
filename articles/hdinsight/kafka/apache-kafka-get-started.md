---
title: "Az Apache Kafka platform használatának első lépései – Azure HDInsight | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre Apache Kafka-fürtöt az Azure HDInsightban. Ismerje meg, hogyan hozhat létre témaköröket, előfizetőket és fogyasztókat."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: 639adb2fdc5a7d76c11397b5027199626a0a4016
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Az HDInsight alatt futó Apache Kafka használatának első lépései

Ebből a cikkből megtudhatja, hogyan hozhat létre és használhat [Apache Kafka](https://kafka.apache.org)-fürtöt az Azure HDInsightban. A Kafka egy, a HDInsighthoz is elérhető, nyílt forráskódú elosztott adatstreamelési platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkcionalitást kínál. A Kafkát gyakran használják az Apache Sparkkal és az Apache Stormmal.

> [!NOTE]
> Jelenleg a Kafka két verziója érhető el a HDInsighttal: a 0.9.0 (HDInsight 3.4) és a 0.10.0 (HDInsight 3.5 és 3.6). A dokumentum lépései azt feltételezik, hogy a Kafkát a HDInsight 3.6-os verzióján használja.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Kafka-fürt létrehozása

Egy Kafka HDInsight-fürtön történő létrehozásához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **+ Erőforrás létrehozása**, **Data + Analytics**, majd a **HDInsight** elemet.
   
    ![HDInsight-fürt létrehozása](./media/apache-kafka-get-started/create-hdinsight.png)

2. Az **Alapvető beállítások** képernyőn adja meg a következő információkat:

    * **Fürt neve**: A HDInsight-fürt neve.
    * **Előfizetés**: Válassza ki a használni kívánt előfizetést.
    * **Fürt bejelentkezési felhasználóneve** és **Fürt bejelentkezési jelszava**: A fürt HTTPS-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Ezekkel a hitelesítő adatokkal érheti el az olyan szolgáltatásokat, mint az Ambari webes felület vagy a REST API.
    * **SSH-felhasználónév**: A fürt SSH-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Alapértelmezés szerint a jelszó megegyezik a fürt bejelentkezési jelszavával.
    * **Erőforráscsoport**: Az az erőforráscsoport, amelyben a fürt létre lesz hozva.
    * **Hely**: Az az Azure-régió, amelyben a fürt létre lesz hozva.

        > [!IMPORTANT]
        > Az adatok Magas rendelkezésre állásának biztosításához javasoljuk egy __három tartalék tartományt__ tartalmazó hely (régió) kiválasztását. További információkért lásd az [Adatok magas rendelkezésre állása](#data-high-availability) szakaszt.
   
 ![Előfizetés kiválasztása](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Válassza ki a **Fürt típusát**, majd állítsa be a következő értékeket a **Fürtkonfiguráció** panelen:
   
    * **Fürt típusa**: Kafka
    * **Verzió**: Kafka 0.10.0 (HDI 3.6)

    Végül mentse a beállításokat a **Kiválasztás** gomb használatával.
     
 ![Fürttípus kiválasztása](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. A fürt típusának kijelölése után erősítse meg a beállítást a __Kiválasztás__ gombbal. Ezután kattintson a __Tovább__ gombra az alapszintű konfiguráció befejezéséhez.

5. A **Tárolás** panelen válasszon ki vagy hozzon létre egy Storage-fiókot. A jelen dokumentumban leírt lépésekben a többi mező alapértelmezett értékét ne módosítsa. Kattintson a __Tovább__ gombra a tárolókonfiguráció mentéséhez.

    ![A tárfiók HDInsight-beállításainak konfigurálása](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

6. Az __Alkalmazások (opcionális)__ területen kattintson a __Tovább__ gombra a folytatáshoz. Az alábbi példához nem szükséges alkalmazás.

7. A __Fürtméret__ lapon kattintson a __Tovább__ gombra a folytatáshoz.

    > [!WARNING]
    > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. További információkért lásd az [Adatok magas rendelkezésre állása](#data-high-availability) szakaszt.

    ![A Kafka-fürt méretének beállítása](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > A **lemezek száma feldolgozó csomópontonként** bejegyzés a HDInsighton futó Kafka skálázhatóságát szabályozza. A HDInsight-beli Kafka a fürt virtuális gépeinek helyi lemezét használja. Mivel a Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. Prémium lemezeket DS és GS sorozatbeli virtuális gépek használnak. Minden más virtuálisgép-típus standard lemezeket használ.

8. A __Speciális beállítások__ lapon kattintson a __Tovább__ gombra a folytatáshoz.

9. Az **Összegzés** lapon tekintse át a fürt konfigurációját. A __Szerkesztés__ hivatkozásai használatával módosítsa a hibás beállításokat. Végül kattintson a__Létrehozás__ gombra a fürt létrehozásához.
   
    ![A fürtkonfiguráció összegzése](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > A fürt létrehozása 20 percig is eltarthat.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

> [!IMPORTANT]
> Az alábbi lépések végrehajtásakor SSH-ügyfelet kell használnia. További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

Az ügyfélről SSH használatával csatlakozhat a fürthöz:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Cserélje le az **SSHUSER** elemet a fürt létrehozásakor megadott SSH-felhasználónévre. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.

Ha a rendszer kéri, írja be az SSH-fiókhoz használt jelszót.

További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>A Zookeeper és a közvetítő gazdagép információinak lekérése

A Kafka használata során két gazdagép értékeit kell ismernie: a *Zookeeper*-gazdagépekét és a *Közvetítő* gazdagépekét. A Kafka API és a Kafkában elérhető számos segédprogram használja ezeket a gazdagépeket.

Az alábbi lépésekkel létrehozhatja a gazdagép adatait tartalmazó környezeti változókat. A jelen dokumentumban lévő lépésekben használjuk ezeket a környezeti változókat.

1. Egy, a fürthöz csatlakozó SSH-kapcsolaton használja a következő parancsot a `jq` segédprogram telepítéséhez. A segédprogram JSON-dokumentumok elemzését végzi, és hasznos szerepet tölt be a közvetítő gazdagép adatainak lekérésében:
   
    ```bash
    sudo apt -y install jq
    ```

2. A következő parancsokkal állíthatja be a környezeti változókat az Ambariból lekért adatokkal:

    ```bash
    CLUSTERNAME='your cluster name'
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > A `CLUSTERNAME=` értékét állítsa a Kafka-fürt nevére. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.

    A következő szöveg egy példa a `$KAFKAZKHOSTS` tartalmára:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    A következő szöveg egy példa a `$KAFKABROKERS` tartalmára:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > A `cut` paranccsal a gazdagépek listáját két gazdagép bejegyzésre szűkítheti. A Kafka-fogyasztók vagy -előállítók létrehozásakor nem kell megadnia a gazdagépek teljes listáját.
   
    > [!WARNING]
    > Ne bízzon abban, hogy az ebben a munkamenetben visszaadott adatok mindig pontosak lesznek. A fürt átméretezésekor új közvetítők lesznek hozzáadva vagy eltávolítva. Ha hiba esetén egy fürt lecserélődik, a fürt gazdaneve változhat.
    >
    > A Zookeeper- és a közvetítő gazdagépek adatait rövid idővel a felhasználásuk előtt érdemes lekérni, hogy biztosan érvényes információkkal rendelkezzen.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

A Kafka *témaköröknek* nevezett kategóriákban tárolja az adatstreameket. Témakör létrehozásához használja a Kafkához biztosított szkripet egy fürt átjárócsomójával létesített SSH-kapcsolatból:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Ez a parancs a `$KAFKAZKHOSTS`-ban tárolt állomásadatok használatával kapcsolódik a Zookeeperhez, majd létrehoz egy **test** nevű Kafka-témakört. A témakör létrehozását a témakörök listázására szolgáló alábbi szkripttel ellenőrizheti:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

A parancs kimenete listázza a Kafka-témaköröket, és tartalmazza a **test** témakört.

## <a name="produce-and-consume-records"></a>Rekordok létrehozása és felhasználása

A Kafka témakörökben tárolja a *rekordokat*. A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. Az előállítók Kafka-*közvetítőknek* adják tovább a rekordokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő.

Kövesse az alábbi lépéseket a rekordoknak a korábban létrehozott test témakörben való eltárolására, majd a beolvasásukra egy fogyasztó használatával:

1. A Kafkához biztosított szkript használatával írja a rekordokat a témakörbe az SSH-munkamenetben:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    A parancs után a rendszer nem lép vissza a parancssorhoz. Ezért írjon inkább néhány szöveges üzenetet, majd a **Ctrl + C** billentyűparancs használatával állítsa le a témakörbe történő küldést. A rendszer minden sort külön rekordként küld el.

2. A Kafkához biztosított szkript használatával olvassa be rekordokat a témakörből:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    A parancs lekéri a rekordokat a témakörből, majd megjeleníti őket. A `--from-beginning` használata arra utasítja a fogyasztót, hogy a stream elejétől kezdje a műveletet, így az összes rekord lekérése megtörténik.

    > [!NOTE]
    > Ha a Kafka régebbi verzióját használja, lehet, hogy a `--bootstrap-server $KAFKABROKERS` előtagot a `--zookeeper $KAFKAZKHOSTS` előtagra kell lecserélnie.

3. Használja a __Ctrl + C__ billentyűparancsot a fogyasztó leállításához.

Szoftveresen is létrehozhat előállítókat és fogyasztókat. Az API használatára vonatkozó példákért tekintse meg a [Kafka Producer és Consumer API-k HDInsighttal történő használatát](apache-kafka-producer-consumer-api.md) ismertető dokumentumot.

## <a name="data-high-availability"></a>Adatok magas rendelkezésre állása

Minden egyes Azure-régió (hely) _tartalék tartományokat_ biztosít. A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.

Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) dokumentumban talál.

> [!IMPORTANT]
> Javasoljuk, hogy olyan Azure-régiót használjon, amely három tartalék tartományt tartalmaz, és használjon 3-as replikációs tényezőt.

Ha kénytelen olyan régiót használni, amely csak két tartalék tartomány tartalmaz, használjon 4-es replikációs tényezőt, hogy egyenletesen ossza el a replikákat a két tartalék tartományban.

### <a name="kafka-and-fault-domains"></a>A Kafka és a tartalék tartományok

A Kafka nem kezeli a tartalék tartományokat. Témakörök számára történő partícióreplikák létrehozásakor lehetséges, hogy a Kafka nem a magas rendelkezésre állásnak megfelelően osztja ki a replikákat. A magas rendelkezésre állás biztosításához használja a [Kafka vissza-egyensúlyozási eszközét](https://github.com/hdinsight/hdinsight-kafka-tools). Ezt az eszközt egy SSH-munkamenetből kell futtatni a Kafka-fürt főcsomópontjához.

A Kafka-adatok lehető legmagasabb rendelkezésre állásának biztosításához a következő időpontokban kell újra egyensúlyoznia a partícióreplikákat a témaköréhez:

* Új témakör vagy partíció létrehozásakor

* Fürt vertikális felskálázásakor

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>További lépések

A jelen dokumentumban megismerkedett az Apache Kafka HDInsightban való használatának az alapjaival. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatreplikálás Kafka-fürtök között](apache-kafka-mirroring.md)
* [Kafka Producer és Consumer API-k a HDInsighttal](apache-kafka-producer-consumer-api.md)
* [Kafka Streams API a HDInsighttal](apache-kafka-streams-api.md)
* [Az Apache Spark stream (DStream) használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Spark strukturált stream használata a Kafkával a HDInsighton](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Az Apache Spark strukturált stream használata adatok áthelyezéséhez a HDInsighton lévő Kafkáról a Cosmos DB-re](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)
* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)
* [A Kafka használata az Azure Container Service-szel](apache-kafka-azure-container-services.md)
* [A Kafka használata az Azure Function Appsszel](apache-kafka-azure-functions.md)
