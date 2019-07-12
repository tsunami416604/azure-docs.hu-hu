---
title: Az Apache Kafka-témakörökhöz – Azure HDInsight tükrözéséhez.
description: Ismerje meg, az Apache Kafka-tükrözési funkció használata témakörök másodlagos fürthöz tükrözés egy Kafka on HDInsight-fürt replikáját karbantartásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657157"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>A MirrorMaker használata a Kafka on HDInsight az Apache Kafka-témakörökhöz replikáláshoz

Útmutató témakörök replikálása egy másodlagos fürt Apache Kafka tükrözési funkció használatához. Tükrözés, folyamatos folyamatként futott vagy időnként módszerként való áttelepítéshez használt adatokat az egyik fürtről a másikra.

Ebben a példában tükrözés segítségével témakörök replikálása a HDInsight fürtök közötti. Mindkét fürt különböző virtuális hálózatokon, különböző adatközpontokban találhatók.

> [!WARNING]  
> Tükrözés nem lehet tekinteni hibatűrés megvalósítása a módszerrel. Az eltolás egy témakörön belül elemekre eltérőek az elsődleges és másodlagos fürtök között, így az ügyfelek nem használhatják a két az felcserélhető.
>
> Ha a hibatűrés a témakörök replikálása a fürtön belül kell beállítania. További információkért lásd: [Apache Kafka on HDInsight – első lépések](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Hogyan működik az Apache Kafka-tükrözés

Tükrözés works használatával a [Mirrormakerrel](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) felhasználhat rekordokat témakörei a az elsődleges fürt, és hozzon létre egy helyi másolatot a másodlagos fürtön eszközzel (az Apache Kafka része). Mirrormakerrel használ (legalább) *fogyasztók* , olvassa el az elsődleges fürtből, és a egy *előállítói* , amely a helyi (másodlagos) fürt ír.

A leghasznosabb a vész-helyreállítási tükrözési telepítő használja a Kafka-fürtök különböző Azure-régióban. Ennek érdekében a fürtök-ket a virtuális hálózatok társviszonyban állnak össze.

A következő ábra szemlélteti a tükrözési folyamat és a fürtök közötti a kommunikáció áramlását:

![A tükrözési folyamat ábrája](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Lehet, hogy az elsődleges és másodlagos fürtök különböző csomópontok és partíciók mennyiségét, és a témakörök belül eltolások különböző is. A kulcs értékét, hogy történő particionálása esetén használt tükrözés tart fenn, így sorrendje a kulcs alapon megőrződik.

### <a name="mirroring-across-network-boundaries"></a>Hálózati határok között történő tükrözés

Kafka-fürtök különböző hálózatokon között tükrözéséhez van szüksége, van-e a következő további szempontok:

* **Átjárók**: A hálózatok kommunikálhat a TCP/IP-szinten kell lennie.

* **Kiszolgáló-címzés**: Ha szeretné, oldja meg a fürt csomópontjai az IP-címeket vagy a teljes tartománynevek használatával.

    * **IP-címek**: Ha a Kafka-fürtök IP-cím hirdetési használatára konfigurálja, folytathatja a tükrözési beállítása az üzenetközvetítő csomópontok és a zookeeper-csomópontok IP-címek használatával.
    
    * **Tartománynevek**: Ha nem adja meg az IP-cím hirdetési célból Kafka-fürtök, a fürtök kell tudni teljesen minősített tartománynevek (FQDN) használatával csatlakozni egymáshoz. Ehhez a tartománynévrendszer (DNS) kiszolgáló, az egyes kérelmek továbbítása a más hálózatokon konfigurált hálózatok. A hálózathoz, a megadott automatikus DNS használata helyett egy Azure virtuális hálózat létrehozásakor meg kell adnia egy egyéni DNS-kiszolgáló és a kiszolgáló IP-címét. A virtuális hálózat létrehozása után, majd hozzon létre egy Azure virtuális gép által használt IP-címet, majd telepítenie és konfigurálnia kell DNS-szoftver rajta.

    > [!WARNING]  
    > Hozzon létre, és az egyéni DNS-kiszolgáló konfigurálása a virtuális hálózatban a HDInsight telepítése előtt. Nem tartozik a HDInsight használatára konfigurált virtuális hálózat DNS-kiszolgáló számára szükséges további konfiguráció.

A két Azure virtuális hálózatok összekapcsolása további információkért lásd: [VNet – VNet kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Architektúra-tükrözés

Ez az architektúra két fürtöket különböző erőforráscsoportokban és virtuális hálózatok funkciói: egy **elsődleges** és **másodlagos**.

### <a name="creation-steps"></a>Létrehozási lépései

1. Két új erőforráscsoport létrehozásához:

    |Erőforráscsoport | Location |
    |---|---|
    | a kafka-elsődleges-rg | USA középső régiója |
    | kafka-secondary-rg | USA északi középső régiója |

1. Hozzon létre egy új virtuális hálózat **kafka-elsődleges-vnet** a **kafka-elsődleges-rg**. Hagyja bejelölve az alapértelmezett beállításokat.
1. Hozzon létre egy új virtuális hálózat **kafka-másodlagos-vnet** a **kafka-másodlagos-rg**, alapértelmezett beállítások használatával is.

1. Két új Kafka-fürtök létrehozása:

    | Fürt neve | Erőforráscsoport | Virtuális hálózat | Tárfiók |
    |---|---|---|---|
    | a kafka-elsődleges-fürt | a kafka-elsődleges-rg | kafka-primary-vnet | kafkaprimarystorage |
    | másodlagos a kafka-fürt | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Hozzon létre virtuális hálózati társviszony-létesítéseket. Ebben a lépésben létrehozza a két társviszony-létesítéseket: egyet a **kafka-elsődleges-vnet** való **kafka-másodlagos-vnet** , és egy biztonsági **kafka-másodlagos-vnet** való  **a kafka-elsődleges-vnet**.
    1. Válassza ki a **kafka-elsődleges-vnet** virtuális hálózatot.
    1. Kattintson a **Társviszonyok** alatt **beállítások**.
    1. Kattintson a **Hozzáadás**lehetőségre.
    1. Az a **felvenni a társviszonyt** képernyőn, adja meg, hogy az alábbi képernyőképen látható módon.

        ![Adja hozzá a virtuális hálózatok közötti társviszony](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. Konfigurálja az IP-hirdetés:
    1. Nyissa meg az elsődleges fürt Ambari irányítópultján: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. Kattintson a **szolgáltatások** > **Kafka**. Kattintson a **Configs** fülre.
    1. Adja hozzá a következő konfigurációs sorokat aljára **kafka-env sablon** szakaszban. Kattintson a **Save** (Mentés) gombra.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Adjon meg egy a **konfiguráció mentése** képernyőn, és kattintson a **mentése**.
    1. Ha a konfigurációs figyelmeztetési kéri, kattintson a **folytatja ennek ellenére**.
    1. Kattintson a **Ok** a a **konfigurációs módosítások mentése**.
    1. Kattintson a **indítsa újra a** > **indítsa újra az összes érintett** a a **újraindítás szükséges** értesítést. Kattintson a **Újraindítás megerősítése összes**.

        ![Indítsa újra a kafka-csomópontok](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Konfigurálja a Kafka figyelni az összes hálózati adapteren.
    1. Maradjon a **Configs** lapjára **szolgáltatások** > **Kafka**. Az a **Kafka-közvetítő** set szakasz a **figyelői** tulajdonságot `PLAINTEXT://0.0.0.0:9092`.
    1. Kattintson a **Save** (Mentés) gombra.
    1. Kattintson a **indítsa újra a**, és **Újraindítás megerősítése összes**.

1. Jegyezze fel a közvetítő IP-címek és az elsődleges fürt Zookeeper-címek.
    1. Kattintson a **gazdagépek** az Ambari-irányítópulton.
    1. Jegyezze fel az IP-címek a közvetítők és a Zookeeper. Az üzenetközvetítő csomópontok rendelkezik **le** az első két betű a gazdagépnév és a zookeeper-csomópontok rendelkezik **zk** az első két betű a gazdagép nevét.

        ![ip-címek megtekintése](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Ismételje meg az előző három lépést a második fürt **másodlagos a kafka-fürt**: IP-hirdetés konfigurálása, állítsa be a figyelők és jegyezze fel a közvetítő és Zookeeper-IP-címeket.

## <a name="create-topics"></a>Hozzon létre kapcsolatos témakörök

1. Csatlakozás a **elsődleges** a fürt SSH-val:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Cserélje le **sshuser** a fürt létrehozásakor használt SSH-felhasználónévvel. Cserélje le **BASENAME** a fürt létrehozásakor használt alap nevét.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A következő paranccsal hozzon létre egy változót az elsődleges fürt Apache Zookeeper állomással. A karakterláncokat, például `ZOOKEEPER_IP_ADDRESS1` kell cserélni a tényleges korábban rögzített IP-címek például `10.23.0.11` és `10.23.0.7`. Ha a teljes tartománynév feloldása az egyéni DNS-kiszolgálót használ, kövesse az [ezeket a lépéseket](apache-kafka-get-started.md#getkafkainfo) beolvasni az ügynök és a zookeeper neveit.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Nevű témakör létrehozásához `testtopic`, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. A következő parancs használatával győződjön meg arról, hogy létrejött-e a témakör:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A válaszban `testtopic`.

4. A Zookeeper gazdagépadatok megtekintéséhez használja a következő (a **elsődleges**) fürt:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Ez hasonló információt ad vissza a következő szöveget:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Mentse ezt az információt. A következő szakaszban szolgál.

## <a name="configure-mirroring"></a>Konfigurálja a tükrözés

1. Csatlakozás a **másodlagos** a fürt egy másik SSH-munkamenet használatával:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Cserélje le **sshuser** a fürt létrehozásakor használt SSH-felhasználónévvel. Cserélje le **SECONDARYCLUSTER** a fürt létrehozásakor használt nevét.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` fájllal folytatott kommunikáció konfigurálása a **elsődleges** fürt. A fájl létrehozásához használja a következő parancsot:

    ```bash
    nano consumer.properties
    ```

    Használja a következő szöveget a tartalmát, a `consumer.properties` fájlt:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Cserélje le **PRIMARY_ZKHOSTS** Zookeeper IP-címekről származó a **elsődleges** fürt.

    Ezt a fájlt használja, ha az elsődleges Kafka-fürt olvasásakor a fogyasztói adatokat ismerteti. További információ a fogyasztói konfigurációhoz, lásd: [fogyasztói Configs](https://kafka.apache.org/documentation#consumerconfigs) a kafka.Apache.org webhelyen.

    Mentse a fájlt, használja a **Ctrl + X**, **Y**, majd **Enter**.

3. Mielőtt konfigurálná a gyártó, amely kommunikál a másodlagos fürttel, a telepítő broker IP-címét egy változót az **másodlagos** fürt. A következő parancsok segítségével hozza létre a változót:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    A parancs `echo $SECONDARY_BROKERHOSTS` adatokat kell visszaadnia az alábbi szöveghez hasonló:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. A `producer.properties` fájl közötti kommunikációra szolgál a **másodlagos** fürt. A fájl létrehozásához használja a következő parancsot:

    ```bash
    nano producer.properties
    ```

    Használja a következő szöveget a tartalmát, a `producer.properties` fájlt:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Cserélje le **SECONDARY_BROKERHOSTS** broker IP-címekről az előző lépésben használt.

    További információk termelő konfigurációs, lásd: [előállítói Configs](https://kafka.apache.org/documentation#producerconfigs) a kafka.Apache.org webhelyen.

5. A következő parancsokat használja egy környezeti változó létrehozásához a Zookeeper-gazdagépek, a másodlagos fürt IP-címmel:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. A HDInsight alatt futó Kafka az alapértelmezett konfigurációja nem engedélyezi a témakörök automatikus létrehozása. Az alábbi lehetőségek közül a tükrözés folyamat elindítása előtt kell használnia:

    * **A témakörök létrehozásához a másodlagos fürtön**: Ez a beállítás is lehetővé teszi a partíciók és a replikációs tényező számának beállításához.

        A következő parancs segítségével hozhat létre előre témakörök:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Cserélje le `testtopic` hozhat létre a témakör nevét.

    * **Konfigurálja a fürt automatikus témakör-létrehozás**: Ez a beállítás lehetővé teszi, hogy Mirrormakerrel automatikusan létrehozza a témaköröket, azonban, előfordulhat, hogy egy másik számmal, partíciók vagy replikációs tényezőt, mint az elsődleges témakör létrehozása.

        Adja meg a másodlagos fürt automatikusan létrehozza a témaköröket, hajtsa végre ezeket a lépéseket:

        1. Nyissa meg a másodlagos fürt Ambari irányítópultján: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Kattintson a **szolgáltatások** > **Kafka**. Kattintson a **Configs** fülre.
        5. Az a __szűrő__ mezőben adja meg a egy értéke `auto.create`. Ez a tulajdonságokat, és megjeleníti listájának szűrése a `auto.create.topics.enable` beállítás.
        6. Módosítsa az értéket a `auto.create.topics.enable` igaz értékre, és válassza ki a __mentése__. Megjegyzés hozzáadása, és válassza ki __mentése__ újra.
        7. Válassza ki a __Kafka__ szolgáltatást, __indítsa újra a__, majd válassza ki __indítsa újra az összes érintett__. Amikor a rendszer kéri, válassza ki a __megerősítése indítsa újra az összes__.

        ![a témakör automatikus létrehozásának konfigurálása](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Indítsa el a MirrorMaker

1. Az SSH-kapcsolatot a a **másodlagos** fürt, a MirrorMaker folyamat elindításához a következő paranccsal:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Ebben a példában használt paraméterek a következők:

    * **--consumer.config**: Megadja a felhasználói tulajdonságokat tartalmazó fájlt. Ezek a tulajdonságok hozhatók létre, amely beolvassa a fogyasztó a *elsődleges* Kafka-fürt.

    * **--producer.config**: Megadja az előállítói tulajdonságait tartalmazó fájlt. Ezek a Tulajdonságok író előállító létrehozásához használt az *másodlagos* Kafka-fürt.

    * **engedélyezési lista –** : Témakörök, amelyek a MirrorMaker replikálja az elsődleges fürtből a másodlagos listája.

    * **--num.streams**: Hozzon létre felhasználói szálak számát.

    A fogyasztó a másodlagos csomópont most vár üzeneteket fogadni.

2. Az SSH-kapcsolatot a a **elsődleges** fürt, indítsa el egy előállítói és üzenetek küldése a témakör a következő paranccsal:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Amikor a kurzort egy üres sort érkeznek, írjon be néhány szöveges üzenetet. A küldött üzeneteket a témakör a **elsődleges** fürt. Ha elkészült, használjon **Ctrl + C** a gyártó folyamat befejezéséhez.

3. Az SSH-kapcsolatot a a **másodlagos** fürt esetén használjon **Ctrl + C** a MirrorMaker folyamat befejezéséhez. A folyamat néhány másodpercig is eltarthat. Győződjön meg arról, hogy az üzenetek a másodlagos lettek replikálva, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Mostantól tartalmazza a témakörök listáját `testtopic`, amelyben létrejön, amikor MirrorMaster tükrözi az elsődleges fürtből a témakör a másodlagos. Az üzenetek a témakörben lekért ugyanazok, mint az elsődleges fürt megadott azokat.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

A jelen dokumentumban leírt lépésekben létrehozott fürtök különböző Azure-erőforráscsoportot. Az összes létrehozott erőforrások törléséhez törölheti a létrehozott két erőforráscsoportok: **kafka-elsődleges-rg** és **kafka-secondary_rg**. Az erőforráscsoportok törlésével eltávolítja az összes alábbi ebben a dokumentumban, többek között fürtökkel, a virtuális hálózatok és a storage-fiókok által létrehozott erőforrásokat.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtudhatta, hogyan használható [Mirrormakerrel](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) a replika létrehozásához egy [Apache Kafka](https://kafka.apache.org/) fürt. Az alábbi hivatkozások segítségével Fedezzen fel más módokat a Kafka használata:

* [Apache Kafka MirrorMaker documentation](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) at cwiki.apache.org.
* [A HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md)
* [Az Apache Kafka on HDInsight az Apache Spark használata](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Kafka on HDInsight az Apache Storm használata](../hdinsight-apache-storm-with-kafka.md)
* [Csatlakozás az Apache Kafka az Azure virtuális hálózaton keresztül](apache-kafka-connect-vpn-gateway.md)
