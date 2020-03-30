---
title: 'Rövid útmutató: Az Apache Kafka beállítása a HDInsighton az Azure Portalon'
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Apache Kafka-fürtöt az Azure HDInsightban az Azure Portal használatával. A Kafka-témakörökről, -előfizetőkről és -fogyasztókról is olvashat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/24/2020
ms.openlocfilehash: 90f7010970f70379c8adecc4214c44d896a1beaf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130246"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Kafka-fürt létrehozása az Azure HDInsightban az Azure Portal használatával

[Az Apache Kafka](./apache-kafka-introduction.md) egy nyílt forráskódú, elosztott streaming platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkciókat kínál.

Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Apache Kafka-fürtöt az Azure Portal használatával. Azt is megtudhatja, hogyan küldhet és fogadhat üzeneteket a mellékelt segédprogramokkal az Apache Kafka segítségével. Az elérhető konfigurációk részletes magyarázata a [Fürtök beállítása a HDInsightban](../hdinsight-hadoop-provision-linux-clusters.md). A portál fürtök létrehozásához való használatával kapcsolatos további információkért [lásd: Fürtök létrehozása a portálon.](../hdinsight-hadoop-create-linux-clusters-portal.md)

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Az Apache Kafka API csak az ugyanazon virtuális hálózaton belüli erőforrások számára érhető el. Ebben a rövid útmutatóban közvetlenül éri el a fürtöt SSH-val. Ha más szolgáltatásokat, hálózatokat vagy virtuális gépeket szeretne csatlakoztatni az Apache Kafkához, először létre kell hoznia egy virtuális hálózatot, majd létre kell hoznia a hálózaton belüli erőforrásokat. További információt a [Csatlakozás az Apache Kafkához virtuális hálózattal](apache-kafka-connect-vpn-gateway.md) című dokumentumban találhat.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-fürt létrehozása

Apache Kafka-fürt hdinsighton történő létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A felső menüben válassza a **+ Erőforrás létrehozása**lehetőséget.

    ![Az Azure Portal erőforrás létrehozása HDInsight](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Válassza az **Analytics** > **Azure HDInsight** lehetőséget a **HDInsight-fürt létrehozása** lap megugrásához.

1. Az **Alapok** lapon adja meg a következő információkat:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetés    |  A legördülő listából válassza ki a fürthöz használt Azure-előfizetést. |
    |Erőforráscsoport     | Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.  Az erőforráscsoport az Azure összetevőit tartalmazó tároló.  Ebben az esetben az erőforráscsoport a HDInsight-fürtöt és a függő Azure Storage-fiókot tartalmazza. |
    |Fürt neve   | Adjon meg egy globálisan egyedi nevet. A név legfeljebb 59 karakterből állhat, beleértve a betűket, számokat és kötőjeleket. A név első és utolsó karaktere nem lehet kötőjel. |
    |Régió    | A legördülő listából válassza ki azt a régiót, ahol a fürt létrejön.  Válasszon egy önhöz közelebbi régiót a jobb teljesítmény érdekében. |
    |Fürt típusa| Lista megnyitásához válassza **a Fürttípus kiválasztása** lehetőséget. A listából válassza a **Kafka** elemet fürttípusként.|
    |Verzió|A fürttípus alapértelmezett verziója lesz megadva. Ha másik verziót szeretne megadni, válasszon a legördülő listából.|
    |A fürt bejelentkezési felhasználóneve és jelszava    | Az alapértelmezett bejelentkezési név **admin**. A jelszónak legalább 10 karakter hosszúságúnak kell lennie, és legalább egy számjegyet, egy nagybetűt és egy kisbetűt, egy nem alfanumerikus karaktert (kivéve a " ' karaktereket . \) Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
    |Secure Shell- (SSH-) felhasználónév | Az alapértelmezett felhasználónév az **sshuser**.  SSH-felhasználónévként más nevet is megadhat. |
    |Fürtbejelentkezési jelszó használata az SSH-hoz| Jelölje be ezt a jelölőnégyzetet, ha ugyanazt a jelszót szeretné használni az SSH-felhasználó számára, mint amelyet a fürtbejelentkezési felhasználó számára megadott.|

   ![Az Azure Portal fürtalapok létrehozása](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Minden egyes Azure-régió (hely) _tartalék tartományokat_ biztosít. A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.

    Az adatok magas rendelkezésre állásának biztosításához válasszon egy olyan régiót (helyet), amely __három tartalék tartományt__ tartalmaz. Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) dokumentumban talál.

    Válassza a **Tovább: Tárolási >>** lapot a tárolási beállításokra való ugráshoz.

1. A **Tárolás** lapon adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tároló típusa|Használja az alapértelmezett **Azure Storage**értéket.|
    |Kiválasztási módszer|Használja az alapértelmezett : **Válassza ki a listából lehetőséget.**|
    |Az elsődleges tárfiók|A legördülő lista segítségével válasszon ki egy meglévő tárfiókot, vagy válassza **az Új létrehozása lehetőséget.** Ha új fiókot hoz létre, a név nek 3 és 24 karakter közötti hosszúságúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.|
    |Tároló|Használja az automatikusan feltöltött értéket.|

    ![HDInsight Linux – első lépések fürttárolási értékek biztosítása](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "HDInsight-fürt létrehozásához tárolási értékek biztosítása")

    Válassza a **Biztonság + hálózat** lapot.

1. Ebben a rövid útmutatóban hagyja meg az alapértelmezett biztonsági beállításokat. Az Enterprise Security Package csomaggal kapcsolatos további információért lásd: [HDInsight-fürt konfigurálása az Enterprise Security Package használatára az Azure Active Directory Domain Services használatával](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Ha meg szeretné tudni, hogyan használhatja saját kulcsát az Apache Kafka lemeztitkosításhoz, látogasson el [az Ügyfél által felügyelt kulcslemez-titkosításra](../disk-encryption.md)

   Ha szeretné egy virtuális hálózathoz csatlakoztatni a fürtöt, válasszon egy virtuális hálózatot a **Virtuális hálózat** legördülő listából.

   ![Fürt hozzáadása egy virtuális hálózathoz](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Válassza a **Konfiguráció + árképzés** lapot.

1. Az Apache Kafka HDInsight-on való rendelkezésre __állásának__ biztosításához a **Feldolgozó-csomópont** csomópontjainak csomópontjainak számát 3 vagy annál nagyobb ra kell állítani. Az alapértelmezett érték a 4.

    A **standard lemezek munkavégző csomópontbejegyzésenként** konfigurálja az Apache Kafka méretezhetőségét a HDInsighton. Az Apache Kafka on HDInsight a fürt virtuális gépeinek helyi lemezén tárolja az adatokat. Mivel az Apache Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. A lemez típusa a feldolgozó csomópontok (Apache Kafka-közvetítők) által használt virtuálisgép-mérettől függ. A DS és GS sorozatbeli virtuális gépek automatikusan prémium lemezeket használnak. Minden más virtuálisgép-típus standard lemezeket használ.

   ![Az Apache Kafka-fürt méretének beállítása](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Válassza a **Véleményezés + létrehozás** lapot.

1. Tekintse át a fürt konfigurációját. Módosítsa a helytelen beállításokat. Végül válassza a **Létrehozás** gombot a fürt létrehozásához.

    ![kafka fürtkonfiguráció összegzése](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    A fürt létrehozása 20 percig is eltarthat.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

    Miután csatlakozott, az alábbi szöveghez hasonló információkat lát:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Szerezd meg az Apache Zookeeper és broker gazdagép adatait

Amikor kafkával dolgozik, ismernie kell az *Apache Zookeeper* és *Broker* házigazdák. Az Apache Kafka API és a Kafkában elérhető számos segédprogram használja ezeket a gazdagépeket.

Ebben a szakaszban az állomás információkat az Apache Ambari REST API-t a fürtön.

1. Telepítse [a jq](https://stedolan.github.io/jq/)parancssori JSON processzort. Ez a segédprogram a JSON-dokumentumok elemzésére szolgál, és hasznos a gazdagép adatainak elemzéséhez. A nyitott SSH-kapcsolatból írja `jq`be a következő parancsot a telepítéshez:

    ```bash
    sudo apt -y install jq
    ```

1. Jelszóváltozó beállítása. Cserélje `PASSWORD` le a fürt bejelentkezési jelszavát, majd írja be a parancsot:

    ```bash
    export password='PASSWORD'
    ```

1. Bontsa ki a megfelelően kis- és nagybetűket, és adja meg a fürt nevét. A fürtnév tényleges burkolata a fürt létrehozásának módjától függően eltérhet a várttól. Ez a parancs beszerzi a tényleges burkolatot, majd egy változóban tárolja. Írja be a következő parancsot:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Ha ezt a folyamatot a fürtön kívülről végzi, más eljárás van a fürtnevének tárolására. A fürt nevét kisbetűvel az Azure Portalon. Ezután helyettesítse `<clustername>` a fürt nevét a `export clusterName='<clustername>'`következő parancsban, és hajtsa végre: .


1. Ha a Zookeeper gazdagép adatait használó környezeti változót szeretne beállítani, használja az alábbi parancsot. A parancs beolvassa az összes Zookeeper állomást, majd csak az első két bejegyzést adja vissza. Ez azért van, mert hasznos lehet a redundancia, ha az egyik gazdagép esetleg nem érhető el.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Ez a parancs Ambari-hozzáférést igényel. Ha a fürt egy NSG mögött van, futtassa ezt a parancsot egy olyan gépről, amely hozzáfér az Ambari eléréséhez. 

1. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. A környezeti változók Apache Kafka-közvetítőgazdagépek adataival való beállításához használja az alábbi parancsot:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Ez a parancs Ambari-hozzáférést igényel. Ha a fürt egy NSG mögött van, futtassa ezt a parancsot egy olyan gépről, amely hozzáfér az Ambari eléréséhez. 

1. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

    ```bash
    echo $KAFKABROKERS
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Apache Kafka-témakörök kezelése

A Kafka *témakörökben* tárolja az adatstreameket. A `kafka-topics.sh` segédprogrammal kezelheti a témaköröket.

* **Egy témakör létrehozásához** használja az alábbi parancsot az SSH-kapcsolatban:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Ez a parancs a `$KAFKAZKHOSTS`-ban tárolt gazdagépadatok használatával kapcsolódik a Zookeeperhez, majd létrehoz egy **test** nevű Apache Kafka-témakört.

    * A témakörben tárolt adatok nyolc partícióban vannak elosztva.

    * Mindegyik partíció három feldolgozó csomópontra van replikálva a fürtben.

        Ha olyan Azure-régióban hozta létre a fürtöt, amely három tartalék tartományt biztosít, használjon 3-as replikációs tényezőt. Ellenkező esetben használjon 4-es replikációs tényezőt.
        
        A három tartalék tartományt tartalmazó régiókban a 3-as replikációs tényező lehetővé teszi, hogy a replikákat el lehessen osztani a tartalék tartományok között. A két tartalék tartományt tartalmazó régiókban a négyes replikációs tényező egyenlően osztja el a replikákat a tartományok között.
        
        Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) dokumentumban talál.

        Az Apache Kafka nem észleli a tartalék Azure-tartományokat. Témakörök számára történő partícióreplikák létrehozásakor lehetséges, hogy a Kafka nem a magas rendelkezésre állásnak megfelelően osztja ki a replikákat.

        A magas rendelkezésre állás érdekében használja az [Apache Kafka partíció-újraegyensúlyozáseszközt.](https://github.com/hdinsight/hdinsight-kafka-tools) Ezt az eszközt egy SSH-kapcsolatból kell futtatni az Apache Kafka-fürt főcsomópontjához.

        Az Apache Kafka-adatok lehető legmagasabb rendelkezésre állása érdekében egyensúlyozza újra a témaköre partícióreplikáit a következő esetekben:

        * Új témakör vagy partíció létrehozásakor

        * Fürt vertikális felskálázásakor

* **A témakörök listázásához** használja az alábbi parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Ez a parancs listázza az Apache Kafka-fürtön elérhető témaköröket.

* **Egy témakör törléséhez** használja az alábbi parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Ez a parancs törli a `topicname` nevű témakört.

    > [!WARNING]  
    > Ha törli a korábban létrehozott `test` témakört, akkor újra létre kell hoznia. A dokumentum későbbi lépései használni fogják.

A `kafka-topics.sh` segédprogrammal elérhető parancsokkal kapcsolatos további információért használja a következő parancsot:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Rekordok létrehozása és felhasználása

A Kafka témakörökben tárolja a *rekordokat.* A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. A létrehozók és a feldolgozók a *Kafka-közvetítő* szolgáltatással kommunikálnak. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Apache Kafka-közvetítőgazdagép.

Kövesse az alábbi lépéseket a rekordoknak a korábban létrehozott test témakörben való tárolására, majd a beolvasásukra egy fogyasztó használatával:

1. Ha rekordokat szeretne írni a témakörbe, használja az SSH-kapcsolat `kafka-console-producer.sh` segédprogramját:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    A parancs kiadása után egy üres sor jelenik meg.

2. Írjon be egy szöveges üzenetet az üres sorba, majd nyomja le az Enter billentyűt. Írjon be így még néhány szöveges üzenetet, majd a **Ctrl + C** billentyűparancs használatával térjen vissza a szokásos parancssorhoz. A rendszer minden sort külön rekordként küld el az Apache Kafka-témakörbe.

3. Ha rekordokat szeretne olvasni a témakörből, használja az SSH-kapcsolat `kafka-console-consumer.sh` segédprogramját:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    A parancs lekéri a rekordokat a témakörből, majd megjeleníti őket. A `--from-beginning` használata arra utasítja a fogyasztót, hogy a stream elejétől kezdje a műveletet, így az összes rekord lekérése megtörténik.

    Ha a Kafka régebbi verzióját használja, cserélje le a `--bootstrap-server $KAFKABROKERS` előtagot a következőre: `--zookeeper $KAFKAZKHOSTS`.

4. Használja a __Ctrl + C__ billentyűparancsot a fogyasztó leállításához.

Szoftveresen is létrehozhat előállítókat és fogyasztókat. Az API használatával például tekintse meg az [Apache Kafka producerés a consumer API HDInsight-dokumentummal.](apache-kafka-producer-consumer-api.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutató által létrehozott erőforrások törléséhez törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]  
> Az Apache Kafka-fürt HDInsight-fürttörlése törli a Kafkában tárolt adatokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Apache Spark használata az Apache Kafka segítségével](../hdinsight-apache-kafka-spark-structured-streaming.md)
