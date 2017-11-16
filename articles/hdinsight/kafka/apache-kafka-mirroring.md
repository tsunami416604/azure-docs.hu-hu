---
title: "Apache Kafka témakörök - Azure HDInsight tükrözésének |} Microsoft Docs"
description: "Útmutató Apache Kafka tükrözési szolgáltatás használatával egy replikát készít egy HDInsight-fürt Kafka karbantartása másodlagos fürtre témakörök tükrözése révén."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: a7063375ac4a2f9f172b5c380c2d5472a12e1bfb
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Apache Kafka témakörök replikálhat a HDInsight Kafka MirrorMaker segítségével

Útmutató Apache Kafka tükrözési szolgáltatás használatával kapcsolatos témakörök replikálása egy másodlagos fürthöz. Tükrözés folyamatos folyamatként lefutott, és áttelepítése módszerként időnként használt adatokat az egyik fürtről a másikra.

Ebben a példában a Tükrözés történő replikálásához használt témakörök két HDInsight-fürtök között. Mindkét fürtről egy Azure virtuális hálózat ugyanabban a régióban van.

> [!WARNING]
> Tükrözés nem tekinthető hibatűrést eléréséhez eszközként. Az eltolás egy témakör elemekre különböznek a forrás és cél fürtök, így nem tudják használni a két azonos értelemben.
>
> Ha a hibatűrés replikációs a témakörök a fürtön belül kell beállítani. További információkért lásd: [Ismerkedjen meg a HDInsight Kafka](apache-kafka-get-started.md).

## <a name="how-kafka-mirroring-works"></a>Tükrözés Kafka működése

Tükrözés működik a MirrorMaker eszközzel (Apache Kafka része) a témakörök a kiindulási fürt bejegyzéseit használnak, és ezután hozzon létre egy helyi példány a célfürtön. MirrorMaker használ egy (vagy több) *fogyasztók* , olvassa el a forrás-fürtből, és egy *készítő* , amely a helyi (cél) fürt ír.

Az alábbi ábra a tükrözés folyamatát mutatja be:

![A tükrözési folyamat diagramja](./media/apache-kafka-mirroring/kafka-mirroring.png)

A HDInsight Apache Kafka nem biztosít hozzáférést a Kafka szolgáltatáshoz a nyilvános interneten keresztül. Kafka gyártók vagy a fogyasztók a Kafka fürt csomópontja azonos Azure virtuális hálózaton kell lennie. Ehhez a példához mindkét a Kafka forrás és cél fürtök Azure virtuális hálózat található. Az alábbi ábra bemutatja, hogyan kommunikációs a fürtök között zajló kommunikációról:

![Egy Azure virtuális hálózatban fürtök forrás és cél Kafka ábrája](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Lehet, hogy a forrás és cél fürtök különböző csomópontok és a partíciók számának, és a témakörök belül eltolások különböző is. A kulcs értékét, particionálás, amellyel tükrözés tart fenn, így sorrendje a kulcs alapú megőrződik.

### <a name="mirroring-across-network-boundaries"></a>Hálózati határokon keresztül történő tükrözés

Különböző hálózatokon Kafka fürtök közötti tükrözésének van szüksége, ha van a következő szempontokat:

* **Átjárók**: A hálózatok tudjanak kommunikálni a TCPIP szinten kell lennie.

* **Nevek feloldása**: Kafka a fürt minden egyes hálózati segítségével hostnames csatlakozni egymáshoz képesnek kell lennie. A tartománynévrendszer (DNS) kiszolgáló minden más hálózataihoz kérelmek továbbítása konfigurált hálózati elképzelhető, hogy szükség.

    A automatikus DNS, a hálózati megadott helyett egy Azure virtuális hálózat létrehozásakor meg kell adnia egy egyéni DNS-kiszolgáló és a kiszolgáló IP-címét. A virtuális hálózat létrehozása után, majd hozzon létre egy Azure virtuális gép által használt IP-címet, majd telepítenie és konfigurálnia kell DNS szoftver rajta.

    > [!WARNING]
    > Hozzon létre, és az egyéni DNS-kiszolgáló konfigurálása a HDInsight a virtuális hálózaton történő telepítése előtt. A HDInsight a konfigurált virtuális hálózat DNS-kiszolgáló használatára szükség további beállításokra van.

A két Azure virtuális hálózatokhoz csatlakozó további információkért lásd: [VNet – VNet-kapcsolatot konfiguráló](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Kafka fürtök létrehozása

Létrehozhat egy Azure virtuális hálózatra, és manuálisan fürtök Kafka, célszerűbb Azure Resource Manager sablonnal. Az alábbi lépések segítségével egy Azure virtuális hálózat és két Kafka fürt központi telepítése az Azure-előfizetéshez.

1. A következő gomb segítségével jelentkezzen be az Azure-ba, és nyissa meg a sablon az Azure portálon.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Az Azure Resource Manager sablon itt található: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. Ez a sablon a három munkavégző csomópontokhoz tartalmaz Kafka fürtöt hoz létre.

2. A következő információk segítségével a feltöltik a **egyéni telepítési** panel:
    
    ![HDInsight egyéni központi telepítés](./media/apache-kafka-mirroring/parameters.png)
    
    * **Erőforráscsoport**: hozzon létre egy csoportot, vagy válasszon egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürthöz.

    * **Hely**: Adjon meg egy földrajzilag Önhöz legközelebb eső helyet.
     
    * **Fürt neve kiinduló**: Ez az érték használható alap neveként a Kafka fürtök számára. Ha például **hdi** nevű fürtök létrehozása **forrás-hdi** és **cél-hdi**.

    * **A fürt bejelentkezési felhasználónevét**: A rendszergazda felhasználóneve a forrás és cél Kafka fürtök.

    * **A fürt bejelentkezési jelszó**: a forrás- és a rendszergazdai jelszóval Kafka fürtök.

    * **SSH-felhasználónév**: az SSH-felhasználó a forrás és cél Kafka fürtök létrehozása.

    * **SSH-jelszónak**: a forrás- és az SSH-felhasználó jelszavát Kafka fürtök.

3. Olvassa el a **feltételek és kikötések**, majd válassza ki **elfogadom a feltételeket és a fenti feltételek**.

4. Végül ellenőrizze **rögzítés az irányítópulton** majd **beszerzési**. A fürt létrehozása nagyjából 20 percet vesz igénybe.

> [!IMPORTANT]
> A HDInsight-fürtök nevét **forrás-BASENAME** és **cél-BASENAME**, ahol BASENAME a sablonhoz megadott név. Ezeket a neveket a későbbi lépésekben használja, a fürtök történő csatlakozás során.

## <a name="create-topics"></a>Hozzon létre kapcsolatos témakörök

1. Csatlakozás a **forrás** fürtön SSH használatával:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Cserélje le **sshuser** az a fürt létrehozásakor használt SSH-felhasználónév. Cserélje le **BASENAME** a fürt létrehozásakor használt alap névvel.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az alábbi parancsokkal a Zookeeper állomások találhatók a kiindulási fürt:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Cserélje le `$CLUSTERNAME` a kiindulási fürt nevével. Amikor a rendszer kéri, adja meg a jelszót a fürt (rendszergazda) bejelentkezési fiók.

3. Egy nevű üzenettémakör létrehozásához `testtopic`, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. A következő parancs használatával győződjön meg arról, hogy létrejött-e a következő témakörben:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    A válaszban `testtopic`.

4. A Zookeeper állomás kapcsolatos információ megtekintéséhez használja a következő (a **forrás**) fürt:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Ez hasonló ad vissza adatokat a következő szöveget:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Mentse ezt az információt. A következő szakaszban használatban van.

## <a name="configure-mirroring"></a>Konfigurálja a tükrözés

1. Csatlakozás a **cél** a fürt egy másik SSH-munkamenetet használatával:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Cserélje le **sshuser** az a fürt létrehozásakor használt SSH-felhasználónév. Cserélje le **BASENAME** a fürt létrehozásakor használt alap névvel.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` fájllal kommunikáció konfigurálása a **forrás** fürt. A fájl létrehozásához a következő paranccsal:

    ```bash
    nano consumer.properties
    ```

    Használja a következő szöveget a tartalmát a `consumer.properties` fájlt:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Cserélje le **SOURCE_ZKHOSTS** adataival a Zookeeper állomások a **forrás** fürt.

    Ez a fájl olvasásához a forrás Kafka fürt fogyasztói információkat ismerteti. További információk a fogyasztói konfigurációhoz, lásd: [fogyasztói Configs](https://kafka.apache.org/documentation#consumerconfigs) kafka.apache.org címen.

    Mentse a fájlt, használja a **Ctrl + X**, **Y**, majd **Enter**.

3. Mielőtt konfigurálná a termelő kommunikáló Miután a célfürtöt, keresse meg az átvitelszervező-állomás a **cél** fürt. A következő parancsok használatával ezek az információk beolvasása:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Cserélje le `$CLUSTERNAME` a célfürt nevével. Amikor a rendszer kéri, adja meg a jelszót a fürt (rendszergazda) bejelentkezési fiók.

    A `echo` parancs adatait adja vissza az alábbihoz hasonló:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. A `producer.properties` fájl való kommunikációhoz használt a __cél__ fürt. A fájl létrehozásához a következő paranccsal:

    ```bash
    nano producer.properties
    ```

    Használja a következő szöveget a tartalmát a `producer.properties` fájlt:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Cserélje le **DEST_BROKERS** az előző lépésben broker adataival.

    További információk készítő konfigurációs, lásd: [készítő Configs](https://kafka.apache.org/documentation#producerconfigs) kafka.apache.org címen.

## <a name="start-mirrormaker"></a>Indítsa el a MirrorMaker

1. Az SSH-kapcsolatot a a **cél** fürt esetén a MirrorMaker folyamat a következő paranccsal:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Ebben a példában használt paraméterek a következők:

    * **--consumer.config**: felhasználói tulajdonságokat tartalmazó fájlt határozza meg. Ezek a tulajdonságok hozhatók létre, amely olvassa be az ügyféllel a *forrás* Kafka fürt.

    * **--producer.config**: készítő tulajdonságokat tartalmazó fájlt határozza meg. Ezek a tulajdonságok hozhatók létre, ír termelő a *cél* Kafka fürt.

    * **--az engedélyezett**: replikáló MirrorMaker a forrás-fürtről a cél-témakörök listáját.

    * **--num.streams**: létrehozásához fogyasztói szálak száma.

 Rendszerindításkor MirrorMaker adatait adja vissza az alábbihoz hasonló:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Az SSH-kapcsolatot a a **forrás** fürt esetén az alábbi parancs segítségével indítsa el a gyártó és üzenetek küldése a következő témakörben:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Cserélje le `$CLUSTERNAME` a kiindulási fürt nevével. Amikor a rendszer kéri, adja meg a jelszót a fürt (rendszergazda) bejelentkezési fiók.

     Amikor egy üres sort a kurzorral érkeznek, adja meg néhány szöveges üzeneteket. Az üzenettémakörbe küldött üzeneteket a témakör a **forrás** fürt. Amikor végzett, **Ctrl + C** a gyártó folyamat befejezéséhez.

3. Az SSH-kapcsolatot a a **cél** fürt esetén használjon **Ctrl + C** a MirrorMaker folyamat befejezéséhez. Győződjön meg arról, hogy a cél a témakör és az üzenetek lettek replikálva, használja a következő parancsokat:

    ```bash
    DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Cserélje le `$CLUSTERNAME` a célfürt nevével. Amikor a rendszer kéri, adja meg a jelszót a fürt (rendszergazda) bejelentkezési fiók.

    Most már tartalmaz témakörök listáját `testtopic`, ami akkor jön létre, amikor MirrorMaster tükrözi a témakör a forrás-fürtről a célhelyre. A témakör sorból beolvasott üzenetekből ugyanazok a kiindulási fürt beírtak szerint.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

A jelen dokumentumban leírt lépések az azonos Azure erőforráscsoport mindkét fürtöket létrehozni, mert az erőforráscsoportot az Azure portálon törölheti. Az erőforráscsoport törlése eltávolítja a következő Ez a dokumentum, az Azure-beli virtuális hálózatra és a fürt által használt tárfiók által létrehozott összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Ebben a dokumentumban megtanulhatta MirrorMaker Kafka fürt másolatának létrehozásához használja. Az alábbi hivatkozások segítségével felderíteni a más módon történő együttműködésre Kafka:

* [Apache Kafka MirrorMaker dokumentáció](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) cwiki.apache.org címen.
* [Ismerkedés az Apache Kafka a HDInsight-on](apache-kafka-get-started.md)
* [Az Apache Spark használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)
* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)
