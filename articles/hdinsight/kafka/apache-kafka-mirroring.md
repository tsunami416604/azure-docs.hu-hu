---
title: Az Apache Kafka-témakörökhöz – Azure HDInsight tükrözéséhez.
description: Ismerje meg, az Apache Kafka-tükrözési funkció használata témakörök másodlagos fürthöz tükrözés egy Kafka on HDInsight-fürt replikáját karbantartásához.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 0c37ad6de867c4abe4ebf0e6c7a40b5cf27c4541
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079639"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>A MirrorMaker használata a Kafka on HDInsight az Apache Kafka-témakörökhöz replikáláshoz

Útmutató témakörök replikálása egy másodlagos fürt Apache Kafka tükrözési funkció használatához. Tükrözés, folyamatos folyamatként futott vagy időnként módszerként való áttelepítéshez használt adatokat az egyik fürtről a másikra.

Ebben a példában tükrözés segítségével témakörök replikálása a HDInsight fürtök közötti. Mindkét fürt az Azure Virtual Network ugyanabban a régióban találhatók.

> [!WARNING]  
> Tükrözés nem lehet tekinteni hibatűrés megvalósítása a módszerrel. Az eltolás egy témakörön belül elemekre eltérőek a forrás- és a fürtök közötti, így az ügyfelek nem használhatják a két az felcserélhető.
>
> Ha a hibatűrés a témakörök replikálása a fürtön belül kell beállítania. További információkért lásd: [Apache Kafka on HDInsight – első lépések](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Hogyan működik az Apache Kafka-tükrözés

Tükrözés works használatával a [Mirrormakerrel](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) eszközzel (az Apache Kafka része) rögzíti a kiindulási fürt témakörei könyvet, majd hozzon létre egy helyi példány a célfürtön. Mirrormakerrel használ (legalább) *fogyasztók* , olvassa el a forrás-fürtből, és a egy *előállítói* , amely a helyi (cél) fürt ír.

A következő ábra szemlélteti a tükrözés folyamat:

![A tükrözési folyamat ábrája](./media/apache-kafka-mirroring/kafka-mirroring.png)

Az Apache Kafka on HDInsight nem biztosít hozzáférést a Kafka szolgáltatás a nyilvános interneten keresztül. A Kafka gyártók vagy fogyasztókat, a Kafka-fürt csomópontjainak azonos Azure virtuális hálózatban kell lennie. Ebben a példában mind a Kafka forrás- és -fürtök Azure virtuális hálózatban találhatók. Az alábbi ábrán látható, hogy a fürtök közötti kommunikáció áramlását:

![Diagramját, a forrás- és Kafka-fürtök az Azure-beli virtuális hálózathoz](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Lehet, hogy a forrás- és fürtöket más csomópontok és a partíciók száma az, és a témakörök belül eltolások különböző is. A kulcs értékét, hogy történő particionálása esetén használt tükrözés tart fenn, így sorrendje a kulcs alapon megőrződik.

### <a name="mirroring-across-network-boundaries"></a>Hálózati határok között történő tükrözés

Kafka-fürtök különböző hálózatokon között tükrözéséhez van szüksége, van-e a következő további szempontok:

* **Átjárók**: A hálózatok kommunikálhat a TCPIP szinten kell lennie.

* **Névfeloldás**: A Kafka-fürtök, az egyes hálózatok csatlakozni egymáshoz állomásnevek használatával képesnek kell lennie. Ez lehet szükség a tartománynévrendszer (DNS) kiszolgáló, az egyes kérelmek továbbítása a más hálózatokon konfigurált hálózatok.

    A hálózathoz, a megadott automatikus DNS használata helyett egy Azure virtuális hálózat létrehozásakor meg kell adnia egy egyéni DNS-kiszolgáló és a kiszolgáló IP-címét. A virtuális hálózat létrehozása után, majd hozzon létre egy Azure virtuális gép által használt IP-címet, majd telepítenie és konfigurálnia kell DNS-szoftver rajta.

    > [!WARNING]  
    > Hozzon létre, és az egyéni DNS-kiszolgáló konfigurálása a virtuális hálózatban a HDInsight telepítése előtt. Nem tartozik a HDInsight használatára konfigurált virtuális hálózat DNS-kiszolgáló számára szükséges további konfiguráció.

A két Azure virtuális hálózatok összekapcsolása további információkért lásd: [VNet – VNet kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-apache-kafka-clusters"></a>Az Apache Kafka-fürtök létrehozása

Bár létrehozhat Azure-beli virtuális hálózathoz, és a Kafka-fürtök manuális, egyszerűbb legyen a használata az Azure Resource Manager-sablon. Az alábbi lépések segítségével Azure-beli virtuális hálózathoz és a két Kafka-fürtök üzembe helyezése az Azure-előfizetéshez.

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Az Azure Resource Manager-sablon a következő helyen található: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]  
    > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. A sablon egy három feldolgozó csomópontot tartalmazó Kafka-fürtöt hoz létre.

2. Az alábbi információk segítségével feltöltik a a **egyéni üzembe helyezés** panelen:
    
    ![HDInsight-egyéni üzembe helyezés](./media/apache-kafka-mirroring/parameters.png)
    
    * **Erőforráscsoport**: Hozzon létre egy csoportot, vagy válasszon ki egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürt.

    * **Hely**: Válasszon egy helyet, földrajzilag közel.
     
    * **Fürt neve alapján**: Ez az érték a Kafka-fürtök alapnevét szolgál. Ha például **hdi** nevű fürtöket hoz létre, **forrás-hdi** és **cél-hdi**.

    * **A fürt bejelentkezési név**: A rendszergazdai felhasználóneve a forrás- és a Kafka-fürtök.

    * **A fürt bejelentkezési jelszavának**: A rendszergazdai felhasználójának jelszava a forrás- és a Kafka-fürtök.

    * **SSH-felhasználónév**: Az SSH-felhasználót a forrás- és Kafka-fürtök létrehozásához.

    * **SSH jelszó**: A jelszót az SSH-felhasználó számára a forrás- és a Kafka-fürtök.

3. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

4. Végül jelölje be a **Rögzítés az irányítópulton** elemet, majd válassza a **Vásárlás** lehetőséget. Körülbelül 20 perc alatt létrehozni a fürtöket vesz igénybe.

> [!IMPORTANT]  
> A HDInsight-fürtök neve vannak **forrás-BASENAME** és **cél-BASENAME**, ahol a sablonhoz megadott BASENAME. Ezeket a neveket használni a későbbi lépésekben a fürtök való kapcsolódás során.

## <a name="create-topics"></a>Hozzon létre kapcsolatos témakörök

1. Csatlakozás a **forrás** a fürt SSH-val:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Cserélje le **sshuser** a fürt létrehozásakor használt SSH-felhasználónévvel. Cserélje le **BASENAME** a fürt létrehozásakor használt alap nevét.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A következő parancsok használatával az Apache Zookeeper gazdagépet keressen a kiindulási fürt:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Cserélje le `$CLUSTERNAME` a kiindulási fürt nevére. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.

3. Nevű témakör létrehozásához `testtopic`, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. A következő parancs használatával győződjön meg arról, hogy létrejött-e a témakör:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    A válaszban `testtopic`.

4. A Zookeeper gazdagépadatok megtekintéséhez használja a következő (a **forrás**) fürt:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Ez hasonló információt ad vissza a következő szöveget:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Mentse ezt az információt. A következő szakaszban szolgál.

## <a name="configure-mirroring"></a>Konfigurálja a tükrözés

1. Csatlakozás a **cél** a fürt egy másik SSH-munkamenet használatával:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Cserélje le **sshuser** a fürt létrehozásakor használt SSH-felhasználónévvel. Cserélje le **BASENAME** a fürt létrehozásakor használt alap nevét.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` fájllal folytatott kommunikáció konfigurálása a **forrás** fürt. A fájl létrehozásához használja a következő parancsot:

    ```bash
    nano consumer.properties
    ```

    Használja a következő szöveget a tartalmát, a `consumer.properties` fájlt:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Cserélje le **SOURCE_ZKHOSTS** adataival Zookeeper gazdagépek a **forrás** fürt.

    Ez a fájl olvasása a forrásból a Kafka-fürt használatára fogyasztói információkat ismerteti. További információ a fogyasztói konfigurációhoz, lásd: [fogyasztói Configs](https://kafka.apache.org/documentation#consumerconfigs) a kafka.Apache.org webhelyen.

    Mentse a fájlt, használja a **Ctrl + X**, **Y**, majd **Enter**.

3. Mielőtt konfigurálná a gyártó, melyekkel Miután a célfürtöt, keressen a közvetítő gazdagépek a **cél** fürt. A következő parancsok használatával lekérheti ezt az információt:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Cserélje le `$CLUSTERNAME` a célul szolgáló fürtcsomópont nevét. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.

    A `echo` parancs megjeleníti az információkat az alábbi szöveghez hasonló:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. A `producer.properties` fájl közötti kommunikációra szolgál a __cél__ fürt. A fájl létrehozásához használja a következő parancsot:

    ```bash
    nano producer.properties
    ```

    Használja a következő szöveget a tartalmát, a `producer.properties` fájlt:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Cserélje le **DEST_BROKERS** broker információkkal az előző lépésben.

    További információk termelő konfigurációs, lásd: [előállítói Configs](https://kafka.apache.org/documentation#producerconfigs) a kafka.Apache.org webhelyen.

5. A következő parancsokat használja a Zookeeper-gazdagépeken található a célul szolgáló fürtcsomópont számára:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Cserélje le `$CLUSTERNAME` a célul szolgáló fürtcsomópont nevét. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.

7. A HDInsight alatt futó Kafka az alapértelmezett konfigurációja nem engedélyezi a témakörök automatikus létrehozása. Az alábbi lehetőségek közül a tükrözés folyamat elindítása előtt kell használnia:

    * **A témakörök létrehozásához a célfürtön**: Ez a beállítás is lehetővé teszi a partíciók és a replikációs tényező számának beállításához.

        A következő parancs segítségével hozhat létre előre témakörök:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Cserélje le `testtopic` hozhat létre a témakör nevét.

    * **Konfigurálja a fürt automatikus témakör-létrehozás**: Ez a beállítás lehetővé teszi, hogy Mirrormakerrel automatikusan létrehozza a témaköröket, azonban, előfordulhat, hogy egy másik számmal, partíciók vagy replikációs tényezőt, mint a forrás-témakör létrehozása.

        Konfigurálja a célul szolgáló fürtcsomópont automatikusan létrehozza a témaköröket, hajtsa végre ezeket a lépéseket:

        1. Az a [az Azure portal](https://portal.azure.com), ki kell választania a célhelyet a Kafka-fürt.
        2. Válassza ki a fürt áttekintés __fürt irányítópultja__. Válassza ki __HDInsight-fürt irányítópultja__. Amikor a rendszer kéri, hitelesíteni a fürt bejelentkezési (rendszergazdai) hitelesítő adataival.
        3. Válassza ki a __Kafka__ szolgáltatás az oldal bal oldalán lévő listából.
        4. Válassza ki __Configs__ az oldal közepén.
        5. Az a __szűrő__ mezőben adja meg a egy értéke `auto.create`. Ez a tulajdonságokat, és megjeleníti listájának szűrése a `auto.create.topics.enable` beállítás.
        6. Módosítsa az értéket a `auto.create.topics.enable` igaz értékre, és válassza ki a __mentése__. Megjegyzés hozzáadása, és válassza ki __mentése__ újra.
        7. Válassza ki a __Kafka__ szolgáltatást, __indítsa újra a__, majd válassza ki __indítsa újra az összes érintett__. Amikor a rendszer kéri, válassza ki a __megerősítése indítsa újra az összes__.

## <a name="start-mirrormaker"></a>Indítsa el a MirrorMaker

1. Az SSH-kapcsolatot a a **cél** fürt, a MirrorMaker folyamat elindításához a következő paranccsal:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Ebben a példában használt paraméterek a következők:

    * **--consumer.config**: Megadja a felhasználói tulajdonságokat tartalmazó fájlt. Ezek a tulajdonságok hozhatók létre, amely beolvassa a fogyasztó a *forrás* Kafka-fürt.

    * **--producer.config**: Megadja az előállítói tulajdonságait tartalmazó fájlt. Ezek a Tulajdonságok író előállító létrehozásához használt az *cél* Kafka-fürt.

    * **engedélyezési lista –**: A cél a forrás-fürtből származó replikált Mirrormakerrel témakörök listáját.

    * **--num.streams**: Hozzon létre felhasználói szálak számát.

   Az Indítás Mirrormakerrel hasonló információt ad vissza a következő szöveget:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Az SSH-kapcsolatot a a **forrás** fürt, indítsa el egy előállítói és üzenetek küldése a témakör a következő paranccsal:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Cserélje le `$CLUSTERNAME` a kiindulási fürt nevére. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.

     Amikor a kurzort egy üres sort érkeznek, írjon be néhány szöveges üzenetet. A küldött üzeneteket a témakör a **forrás** fürt. Ha elkészült, használjon **Ctrl + C** a gyártó folyamat befejezéséhez.

3. Az SSH-kapcsolatot a a **cél** fürt esetén használjon **Ctrl + C** a MirrorMaker folyamat befejezéséhez. A folyamat néhány másodpercig is eltarthat. Győződjön meg arról, hogy az üzenetek a rendeltetési helyre replikált, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Cserélje le `$CLUSTERNAME` a célul szolgáló fürtcsomópont nevét. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.

    Mostantól tartalmazza a témakörök listáját `testtopic`, amelyben létrejön, amikor MirrorMaster tükrözi a témakör a forrás-fürtről a célhelyre. Az üzenetek a témakörben lekért ugyanazok, mint a forrás fürt adta meg.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Mivel a jelen dokumentumban leírt lépések az Azure-erőforráscsoport mindkét fürtöket létrehozni, törölheti az erőforráscsoportot az Azure Portalon. Az erőforráscsoport törlése eltávolítja az ebben a dokumentumban, az Azure Virtual Network és storage-fiókot a fürtök által használt alábbi létrehozott összes erőforrást.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtudhatta, hogyan használható [Mirrormakerrel](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) a replika létrehozásához egy [Apache Kafka](https://kafka.apache.org/) fürt. Az alábbi hivatkozások segítségével Fedezzen fel más módokat a Kafka használata:

* [Apache Kafka MirrorMaker documentation](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) at cwiki.apache.org.
* [A HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md)
* [Az Apache Kafka on HDInsight az Apache Spark használata](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Kafka on HDInsight az Apache Storm használata](../hdinsight-apache-storm-with-kafka.md)
* [Csatlakozás az Apache Kafka az Azure virtuális hálózaton keresztül](apache-kafka-connect-vpn-gateway.md)
