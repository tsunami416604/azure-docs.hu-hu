---
title: Az Apache Kafka témáinak tükrözése – Azure HDInsight
description: Ismerje meg, hogyan használhatja az Apache Kafka tükrözési szolgáltatását egy Kafka replikájának karbantartásához a HDInsight-fürtön a témakörök tükrözésével egy másodlagos fürtbe.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425884"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>A MirrorMaker használata Apache Kafka-témakörök replikálására a Kafka on HDInsight esetében

Ismerje meg, hogyan használhatja az Apache Kafka tükrözési funkcióját témakörök másodlagos fürtbe replikálásához. A tükrözés futtatható folyamatos folyamatként, vagy időnként használható az adatok egyik fürtről a másikra történő áttelepítésére.

Ebben a példában a tükrözés két HDInsight-fürt közötti témakörök replikálására szolgál. Mindkét fürt különböző virtuális hálózatokban található a különböző adatközpontokban.

> [!WARNING]  
> A tükrözés nem tekinthető a hibatűrés elérésének eszközének. A témakörelemeinek eltolása eltér az elsődleges és a másodlagos fürtök között, így az ügyfelek nem használhatják a két szinonimaként.
>
> Ha aggódik a hibatűrés miatt, állítsa be a replikációt a fürtön belüli témakörökhöz. További információ: [Az Apache Kafka a HDInsight-on című témakörben talál.](apache-kafka-get-started.md)

## <a name="how-apache-kafka-mirroring-works"></a>Az Apache Kafka tükrözés működése

A tükrözés a [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) eszközzel (az Apache Kafka részével) működik az elsődleges fürt témaköreinek rekordjainak felhasználásához, majd helyi példány létrehozása a másodlagos fürtön. A MirrorMaker egy (vagy több) *fogyasztót* használ, amely az elsődleges fürtből olvas, és egy *olyan gyártót,* amely a helyi (másodlagos) fürtbe ír.

A katasztrófa-helyreállítási helyreállítás leghasznosabb tükrözési beállítása a Kafka-fürtöket használja a különböző Azure-régiókban. Ennek elérése érdekében a virtuális hálózatok, ahol a fürtök találhatók, együtt kukucskálnak.

Az alábbi ábra bemutatja a tükrözési folyamatot és a fürtök közötti kommunikáció menetét:

![A tükrözési folyamat ábrája](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Az elsődleges és másodlagos fürtök eltérőek lehetnek a csomópontok és partíciók számában, és a témakörökön belüli eltolások is eltérőek. A tükrözés megtartja a particionáláshoz használt kulcsértéket, így a rekordsorrend kulcsonként megmarad.

### <a name="mirroring-across-network-boundaries"></a>Tükrözés a hálózat határain keresztül

Ha tükröznie kell a különböző hálózatokkafka fürtjei között, a következő szempontokat kell figyelembe venni:

* **Átjárók**: A hálózatoknak képesnek kell lenniük a TCP/IP szintű kommunikációra.

* **Kiszolgálócímzés**: A fürtcsomópontokat az IP-címük vagy a teljesen minősített tartománynevek használatával is megcímezheti.

    * **IP-címek:** Ha a Kafka-fürtöket IP-címhirdetések használatára konfigurálja, folytathatja a tükrözési beállítást a közvetítő csomópontok és az állatkerti őrcsomópontok IP-címei vel.
    
    * **Tartománynevek:** Ha nem konfigurálja a Kafka-fürtöket az IP-címhirdetésekhez, a fürtöknek képesnek kell lenniük arra, hogy teljesen minősített tartománynevek (FQDN) használatával csatlakozzanak egymáshoz. Ehhez minden olyan hálózatban dns-kiszolgálóra van szükség, amely úgy van beállítva, hogy a kérelmeket a többi hálózatnak továbbítsa. Az Azure virtuális hálózat létrehozásakor a hálózathoz biztosított automatikus DNS használata helyett meg kell adnia egy egyéni DNS-kiszolgálót és a kiszolgáló IP-címét. A virtuális hálózat létrehozása után létre kell hoznia egy Azure virtuális gépet, amely ezt az IP-címet használja, majd telepítenie és konfigurálnia kell rajta a DNS-szoftvert.

    > [!WARNING]  
    > A HDInsight virtuális hálózatra való telepítése előtt hozza létre és konfigurálja az egyéni DNS-kiszolgálót. Nincs szükség további konfigurációra ahhoz, hogy a HDInsight a virtuális hálózathoz konfigurált DNS-kiszolgálót használhassa.

A két Azure virtuális hálózat csatlakoztatásáról a Virtuális hálózat és [virtuális hálózat között lévő kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)című témakörben talál további információt.

## <a name="mirroring-architecture"></a>Tükrözési architektúra

Ez az architektúra két fürtöt tartalmaz különböző erőforráscsoportokban és virtuális hálózatokban: egy **elsődleges** és **másodlagos**.

### <a name="creation-steps"></a>Létrehozási lépések

1. Hozzon létre két új erőforráscsoportot:

    |Erőforráscsoport | Hely |
    |---|---|
    | kafka-elsődleges-rg | USA középső régiója |
    | kafka-másodlagos-rg | USA északi középső régiója |

1. Hozzon létre egy új virtuális hálózat **kafka-elsődleges-vnet** a **kafka-primary-rg**. Hagyja meg az alapértelmezett beállításokat.
1. Hozzon létre egy új virtuális hálózati **kafka-másodlagos-vnet** **a kafka-secondary-rg,** szintén alapértelmezett beállításokkal.

1. Hozzon létre két új Kafka-fürtöt:

    | Fürt neve | Erőforráscsoport | Virtual Network | Tárfiók |
    |---|---|---|---|
    | kafka-elsődleges-fürt | kafka-elsődleges-rg | kafka-elsődleges-vnet | kafkaprimarytárolás |
    | kafka-másodlagos-fürt | kafka-másodlagos-rg | kafka-másodlagos-vnet | kafkamásodlagos tárolás |

1. Hozzon létre virtuális hálózati társviszony-létesítéseket. Ez a lépés két társviszonyt hoz létre: az egyik a **kafka-primary-vnet-től** **a kafka-secondary-vnet-ig,** a másik pedig a **kafka-secondary-vnet-ről** **a kafka-primer-vnet-re.**
    1. Válassza ki a **kafka-elsődleges-vnet** virtuális hálózat.
    1. Válassza **a Társviszony-létesítések** lehetőséget a **Beállítások csoportban.**
    1. Válassza a **Hozzáadás** lehetőséget.
    1. A **Társviszony-létesítés hozzáadása** képernyőn adja meg a részleteket az alábbi képernyőképen látható módon.

        ![HDInsight Kafka hozzá vnet társviszony-létesítés](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>IP-hirdetések konfigurálása

Az IP-hirdetések konfigurálása, hogy az ügyfél tartománynevek helyett bróker IP-címekkel csatlakozzon.

1. Nyissa meg az elsődleges fürt Ambari irányítópultját: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
1. Válassza ki **a Szolgáltatások** > **Kafka**. CliSelectck a **Configs** lap.
1. Adja hozzá a következő konfigurációs sorokat a **kafka-env sablon** szakaszhoz. Kattintson a **Mentés** gombra.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Írjon be egy megjegyzést a **Konfiguráció mentése** képernyőn, és kattintson a **Mentés gombra.**
1. Ha a rendszer konfigurációs figyelmeztetést kér, kattintson a **Folytatás gombra.**
1. Válassza az **Ok gombot** a **Konfigurációs módosítások mentése kor.**
1. Válassza **az Újraindítás** > **újraindítása Minden érintett et** a Szükséges **újraindítás** értesítésben. Válassza **az Újraindítás megerősítése az összeset**lehetőséget.

    ![Az Apache Ambari minden érintett](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Állítsa be a Kafka-t úgy, hogy minden hálózati csatolón figyeljen.
    
1. Maradjon a **Configs** lapon **szolgáltatások** > **Kafka**. A **Kafka Broker** szakaszban állítsa `PLAINTEXT://0.0.0.0:9092`a **hallgatók** tulajdonságát .
1. Kattintson a **Mentés** gombra.
1. Válassza **az Újraindítás**lehetőséget, majd erősítse meg az Újraindítás **mindet.**

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Record Broker IP-címek és Zookeeper-címek az elsődleges fürthöz.

1. Válassza a **Hosts** lehetőséget az Ambari irányítópulton.
1. Jegyezze fel a brókerek és zookeepers IP-címeket. A közvetítő csomópontok **wn,** mint az első két betű a gazdagép nevét, és az állattartó csomópontok **zk,** mint az első két betű az állomás név.

    ![Apache Ambari nézet csomópont IP-címek](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Ismételje meg az előző három lépést a második fürt **kafka-másodlagos-fürt:** konfigurálja az IP-hirdetmény, állítsa be a figyelők, és jegyezze fel a Broker és Zookeeper IP-címek.

## <a name="create-topics"></a>Témakörök létrehozása

1. Csatlakozzon az **elsődleges** fürthöz az SSH használatával:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Cserélje le a **ssh felhasználónevet** a fürt létrehozásakor használt SSH felhasználónévre. Cserélje le a **PRIMARYCLUSTER-t** a fürt létrehozásakor használt alapnévre.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. A következő paranccsal hozzon létre egy változót az Apache Zookeeper gazdagépekkel az elsődleges fürthöz. A karakterláncokat, mint kell `ZOOKEEPER_IP_ADDRESS1` cserélni a tényleges IP-címek `10.23.0.11` `10.23.0.7`korábban rögzített, mint például a és . Ha teljes tartománynév-feloldást használ egyéni DNS-kiszolgálóval, kövesse [az alábbi lépéseket](apache-kafka-get-started.md#getkafkainfo) a közvetítői és zookeeper-nevek lekéréséhez.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. A névvel `testtopic`ellátott témakör létrehozásához használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. A témakör létrehozásának ellenőrzéséhez használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A válasz `testtopic`tartalmazza a .

1. Az alábbi módon tekintheti meg a Zookeeper gazdagép **(elsődleges)** fürt adatait:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Ez a következő szöveghez hasonló információkat ad vissza:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Mentse ezt az információt. A következő részben használják.

## <a name="configure-mirroring"></a>Tükrözés konfigurálása

1. Csatlakozás a **másodlagos** fürthöz egy másik SSH-munkamenet használatával:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Cserélje le a **ssh felhasználónevet** a fürt létrehozásakor használt SSH felhasználónévre. Cserélje le a **SECONDARYCLUSTER-t** a fürt létrehozásakor használt névre.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. A `consumer.properties` fájl az **elsődleges** fürttel való kommunikáció konfigurálására szolgál. A fájl létrehozásához használja a következő parancsot:

    ```bash
    nano consumer.properties
    ```

    A `consumer.properties` fájl tartalmaként a következő szöveget használja:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Cserélje le **PRIMARY_ZKHOSTS** az **elsődleges** fürt Zookeeper IP-címeire.

    Ez a fájl az elsődleges Kafka-fürtből való olvasáshoz használandó fogyasztói adatokat ismerteti. További információ fogyasztói konfiguráció, [lásd: Fogyasztói konfigurációk](https://kafka.apache.org/documentation#consumerconfigs) kafka.apache.org.

    A fájl mentéséhez használja a **Ctrl + X**, **Y**, majd az **Enter billentyűt.**

1. A másodlagos fürttel kommunikáló gyártó konfigurálása előtt állítson be egy változót a **másodlagos** fürt közvetítői IP-címeihez. A változó létrehozásához használja az alábbi parancsokat:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    A `echo $SECONDARY_BROKERHOSTS` parancsnak a következő szöveghez hasonló információkat kell visszaadnia:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. A `producer.properties` másodlagos **fürt** kommunikálására fájl szolgál. A fájl létrehozásához használja a következő parancsot:

    ```bash
    nano producer.properties
    ```

    A `producer.properties` fájl tartalmaként a következő szöveget használja:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Cserélje le **SECONDARY_BROKERHOSTS** az előző lépésben használt bróker IP-címekkel.

    További információ a gyártó konfigurációja, [lásd: Producer Configs](https://kafka.apache.org/documentation#producerconfigs) at kafka.apache.org.

1. A következő parancsokkal hozzon létre egy környezeti változót a másodlagos fürt Zookeeper állomásainak IP-címeivel:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. A Kafka alapértelmezett konfigurációja a HDInsight-on nem teszi lehetővé a témakörök automatikus létrehozását. A Tükrözési folyamat megkezdése előtt az alábbi lehetőségek egyikét kell használnia:

    * **A témakörök létrehozása a másodlagos fürtön:** Ez a beállítás lehetővé teszi a partíciók és a replikációs tényező számának beállítását is.

        A témaköröket a következő paranccsal hozhat létre előre:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Cserélje `testtopic` le a létrehozandó témakör nevére.

    * **Konfigurálja a fürtöt az automatikus témakör-létrehozáshoz**: Ez a beállítás lehetővé teszi a MirrorMaker számára, hogy automatikusan hozzon létre témaköröket, azonban az elsődleges témakörtől eltérő számú partícióval vagy replikációs tényezővel hozhatja létre őket.

        Ha a másodlagos fürtöt úgy szeretné beállítani, hogy automatikusan hozzon létre témaköröket, hajtsa végre az alábbi lépéseket:

        1. Nyissa meg a másodlagos fürt Ambari irányítópultját: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Kattintson **a Szolgáltatások** > **Kafka gombra.** Kattintson a **Configs** fülre.
        1. A __Szűrő__ mezőbe írja be `auto.create`a értékét. Ez szűri a tulajdonságok `auto.create.topics.enable` listáját, és megjeleníti a beállítást.
        1. Módosítsa a `auto.create.topics.enable` true értékét, majd válassza a __Mentés gombot.__ Vegyen fel egy jegyzetet, majd kattintson ismét __a Mentés__ gombra.
        1. Válassza a __Kafka__ szolgáltatást, válassza __az Újraindítás__lehetőséget, majd __az Összes érintett újraindítása__lehetőséget. Amikor a rendszer kéri, válassza __az Újraindítás megerősítése az összeset__lehetőséget.

        ![kafka engedélyezése automatikus létrehozása témák](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Tükörkészítő indítása

1. Az SSH-kapcsolatról a **másodlagos** fürtre a következő paranccsal indítsa el a MirrorMaker folyamatot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    A példában használt paraméterek a következők:

    |Paraméter |Leírás |
    |---|---|
    |--consumer.config|Megadja a fogyasztói tulajdonságokat tartalmazó fájlt. Ezek a tulajdonságok az *elsődleges* Kafka-fürtből beolvasásra felolvasó fogyasztó létrehozására szolgálnak.|
    |--producer.config|Megadja a gyártó tulajdonságait tartalmazó fájlt. Ezek a tulajdonságok a *másodlagos* Kafka-fürtbe írt előproducer létrehozásához használatosak.|
    |--engedélyezési lista|A MirrorMaker által az elsődleges fürtről a másodlagosra replikáló témakörök listája.|
    |--num.streamek|A létrehozandó fogyasztói szálak száma.|

    A másodlagos csomóponton lévő fogyasztó most az üzenetek fogadására vár.

2. Az SSH-kapcsolatról az **elsődleges** fürtre a következő paranccsal indítson el egy gyártót, és küldjön üzeneteket a témakörnek:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Amikor egy kurzorral rendelkező üres sorhoz érkezik, írjon be néhány szöveges üzenetet. Az üzeneteket a rendszer az **elsődleges** fürttémakörbe küldi. Ha elkészült, a **Ctrl + C billentyűkombinációval** fejezze be a gyártói folyamatot.

3. Az SSH-kapcsolatról a **másodlagos** fürtre a **Ctrl + C billentyűkombinációval** fejezd be a MirrorMaker-folyamatot. A folyamat befejezése több másodpercet is igénybe vehet. Annak ellenőrzéséhez, hogy az üzenetek replikálódtak-e a másodlagos ra, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    A témakörök listája `testtopic`most tartalmazza a , amely akkor jön létre, amikor a MirrorMaster tükrözi a témakört az elsődleges fürtről a másodlagosra. A témakörből beolvasott üzenetek megegyeznek az elsődleges fürtön megadott üzenetekkel.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

A jelen dokumentumban leírt lépések fürtöket hoztak létre különböző Azure-erőforráscsoportokban. Az összes létrehozott erőforrás törléséhez törölheti a létrehozott két erőforráscsoportot: **a kafka-primary-rg** és **a kafka-secondary_rg**. Az erőforráscsoportok törlése eltávolítja a dokumentum követésével létrehozott összes erőforrást, beleértve a fürtöket, a virtuális hálózatokat és a tárfiókokat.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan használhatja a [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) t [egy Apache Kafka-fürt](https://kafka.apache.org/) replikájának létrehozásához. Az alábbi hivatkozásoksegítségével további módszereket fedezhet fel a Kafka használatára:

* [Apache Kafka MirrorMaker dokumentáció](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) cwiki.apache.org.
* [Kafka Mirror Maker bevált gyakorlatok](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Ismerkedés az Apache Kafkával a HDInsighton](apache-kafka-get-started.md)
* [Az Apache Spark használata az Apache Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)
* [Csatlakozás az Apache Kafkához egy Azure virtuális hálózaton keresztül](apache-kafka-connect-vpn-gateway.md)
