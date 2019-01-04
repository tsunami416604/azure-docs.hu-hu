---
title: Az Apache Kafka használatának első lépései – Rövid Azure HDInsight-útmutató
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Apache Kafka-fürtöt az Azure HDInsightban az Azure Portal használatával. A Kafka-témakörökről, -előfizetőkről és -fogyasztókról is olvashat.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: mvc,hdinsightactive
ms.topic: quickstart
ms.date: 10/12/2018
ms.openlocfilehash: 76f09af66e362fb6b03346b43a6be1a3ec7cf681
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976766"
---
# <a name="quickstart-create-an-apache-kafka-on-hdinsight-cluster"></a>Gyors útmutató: Hozzon létre egy Apache Kafka HDInsight-fürtön

Az Apache Kafka egy nyílt forráskódú, elosztott adatstreamelési platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkciókat kínál. 

Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre [Apache Kafka](https://kafka.apache.org)-fürtöt az Azure Portal használatával. Azt is megtudhatja, hogyan küldhet és fogadhat üzeneteket a mellékelt segédprogramokkal az Apache Kafka segítségével.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]  
> Az Apache Kafka API csak az ugyanazon virtuális hálózaton belüli erőforrások számára érhető el. Ebben a rövid útmutatóban közvetlenül éri el a fürtöt SSH-val. Ha más szolgáltatásokat, hálózatokat vagy virtuális gépeket szeretne csatlakoztatni az Apache Kafkához, először létre kell hoznia egy virtuális hálózatot, majd létre kell hoznia a hálózaton belüli erőforrásokat.
>
> További információt a [Csatlakozás az Apache Kafkához virtuális hálózattal](apache-kafka-connect-vpn-gateway.md) című dokumentumban találhat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Egy SSH-ügyfél. A jelen dokumentum lépései SSH használatával csatlakoznak a fürthöz.

    Az `ssh` parancs alapértelmezés szerint a Linux, Unix és macOS rendszerek része. Windows 10 rendszeren a következő módszerek egyikével telepítse az `ssh` parancsot:

    * Az [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) használata. A Cloud Shell tartalmazza az `ssh` parancsot, és úgy konfigurálható, hogy a Basht vagy a PowerShellt használja a felületkörnyezetként.

    * [A Linux Windows alrendszerének telepítése](https://docs.microsoft.com/windows/wsl/install-win10). A Microsoft Store-ban elérhető Linux-disztribúciók tartalmazzák az `ssh` parancsot.

    > [!IMPORTANT]  
    > A dokumentum lépései azt feltételezik, hogy a fent említett egyik SSH-ügyfelet használja. Ha más SSH-ügyfelet használ, és problémákba ütközik, tekintse át az SSH-ügyfél dokumentációját.
    >
    > További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-fürt létrehozása

Egy Apache Kafka on HDInsight-fürt létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **+ erőforrás létrehozása**.

1. A **Azure Marketplace-en**válassza **Analytics**.

1. A **kiemelt**válassza **HDInsight**.
   
    ![HDInsight-fürt létrehozása](./media/apache-kafka-get-started/create-hdinsight.png)

2. Az **Alapvető beállítások** képernyőn adja meg vagy válassza ki a következő információkat:

    | Beállítás | Érték |
    | --- | --- |
    | Fürt neve | A HDInsight-fürt egyedi neve. |
    | Előfizetés | Válassza ki előfizetését. |
    
   Válassza ki a __Fürt típusát__ a **Fürtkonfiguráció** megjelenítéséhez.
   
   ![A HDInsight-beli Apache Kafka-fürt alapszintű konfigurálása](./media/apache-kafka-get-started/hdinsight-basic-configuration-1.png)

3. A __Fürtkonfigurációban__ válassza ki a következő értékeket:

    | Beállítás | Érték |
    | --- | --- |
    | Fürt típusa | Kafka |
    | Verzió | Kafka 1.1.0 (HDI 3.6) |

    A **Kiválasztás** gombbal mentse a fürttípus beállításait, majd lépjen vissza az __Alapvető beállításokra__.

    ![Fürttípus kiválasztása](./media/apache-kafka-get-started/kafka-cluster-type.png)

4. Az __Alapvető beállítások__ képernyőn adja meg vagy válassza ki a következő információkat:

    | Beállítás | Érték |
    | --- | --- |
    | Fürt bejelentkezési felhasználóneve | A fürtön futtatott webszolgáltatások vagy REST API-k eléréséhez használt bejelentkezési név. Tartsa meg az alapértelmezett értéket (admin). |
    | Fürt bejelentkezési jelszava | A fürtön futtatott webszolgáltatások vagy REST API-k eléréséhez használt jelszó. |
    | Secure Shell- (SSH-) felhasználónév | A fürt SSH-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Alapértelmezés szerint a jelszó megegyezik a fürt bejelentkezési jelszavával. |
    | Erőforráscsoport | Az erőforráscsoport, amelyben a fürt létre lesz hozva. |
    | Hely | Az Azure-régió, amelyben a fürt létre lesz hozva. |

    > [!TIP]  
    > Minden egyes Azure-régió (hely) _tartalék tartományokat_ biztosít. A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.
    >
    > Az adatok magas rendelkezésre állásának biztosításához válasszon egy olyan régiót (helyet), amely __három tartalék tartományt__ tartalmaz. Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) dokumentumban talál.

   ![Előfizetés kiválasztása](./media/apache-kafka-get-started/hdinsight-basic-configuration-2.png)

    Kattintson a __Tovább__ gombra az alapszintű konfiguráció befejezéséhez.

5. Ebben a rövid útmutatóban hagyja meg az alapértelmezett biztonsági beállításokat. Az Enterprise Security Package csomaggal kapcsolatos további információért lásd: [HDInsight-fürt konfigurálása az Enterprise Security Package használatára az Azure Active Directory Domain Services használatával](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). A saját kulcs Apache Kafka-lemeztitkosításhoz való használatával kapcsolatos információért lásd: [Saját kulcs használata az Apache Kafka on Azure HDInsightban](apache-kafka-byok.md)

   Ha szeretné egy virtuális hálózathoz csatlakoztatni a fürtöt, válasszon egy virtuális hálózatot a **Virtuális hálózat** legördülő listából.

   ![Fürt hozzáadása egy virtuális hálózathoz](./media/apache-kafka-get-started/kafka-security-config.png)

6. A **Tárolás** panelen válasszon ki vagy hozzon létre egy Storage-fiókot. A jelen dokumentumban leírt lépésekben a többi mező alapértelmezett értékét ne módosítsa. Kattintson a __Tovább__ gombra a tárolókonfiguráció mentéséhez. A Data Lake Storage Gen2 használatával további információkért lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

   ![A tárfiók HDInsight-beállításainak konfigurálása](./media/apache-kafka-get-started/storage-configuration.png)

7. Az __Alkalmazások (opcionális)__ területen kattintson a __Tovább__ gombra az alapértelmezett beállítások használatával való folytatáshoz.

8. A __Fürtméret__ lapon kattintson a __Tovább__ gombra az alapértelmezett beállítások használatával való folytatáshoz.

    > [!IMPORTANT]  
    > Az Apache Kafka on HDInsight platform rendelkezésre állásának biztosításához a __feldolgozó csomópontok száma__ bejegyzés értékének legalább 3-nak kell lennie. Az alapértelmezett érték a 4.
    
    > [!TIP]  
    > A **lemezek száma feldolgozó csomópontonként** bejegyzés az Apache Kafka on HDInsight skálázhatóságát konfigurálja. Az Apache Kafka on HDInsight a fürt virtuális gépeinek helyi lemezén tárolja az adatokat. Mivel az Apache Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. A lemez típusa a feldolgozó csomópontok (Apache Kafka-közvetítők) által használt virtuálisgép-mérettől függ. A DS és GS sorozatbeli virtuális gépek automatikusan prémium lemezeket használnak. Minden más virtuálisgép-típus standard lemezeket használ.

   ![Az Apache Kafka-fürt méretének beállítása](./media/apache-kafka-get-started/kafka-cluster-size.png)

9. A __Speciális beállítások__ lapon kattintson a __Tovább__ gombra az alapértelmezett beállítások használatával való folytatáshoz.

10. Az **Összegzés** lapon tekintse át a fürt konfigurációját. A __Szerkesztés__ hivatkozásai használatával módosítsa a hibás beállításokat. Végül kattintson a__Létrehozás__ gombra a fürt létrehozásához.
   
    ![A fürtkonfiguráció összegzése](./media/apache-kafka-get-started/kafka-configuration-summary.png)
   
    > [!NOTE]
    > A fürt létrehozása 20 percig is eltarthat.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

1. Az Apache Kafka-fürt az elsődleges átjárócsomóponthoz csatlakozik, használja a következő parancsot. Cserélje le az `sshuser` elemet az SSH-felhasználónévre. Cserélje le `mykafka` az Apache Kafkacluster nevére.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Amikor első alkalommal csatlakozik a fürthöz, az SSH-ügyfél olyan figyelmeztetést jeleníthet meg, amely szerint a gazdaszámítógép nem hitelesíthető. Amikor a rendszer kéri, írja be a __yes__ (igen) szót, majd nyomja majd nyomja le az __Enter__ billentyűt, hogy a gazdaszámítógépet felvegye az SSH-ügyfél megbízható kiszolgálókat tartalmazó listájába.

3. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

Miután csatlakozott, az alábbi szöveghez hasonló információkat lát:

```text
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
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Az Apache Zookeeper és a közvetítő gazdagép információinak lekérése

A Kafka használata során ismernie kell a *Apache Zookeeper* és *Broker* gazdagépek. Az Apache Kafka API és a Kafkában elérhető számos segédprogram használja ezeket a gazdagépeket.

Ebben a szakaszban a gazdagép adatait az Apache Ambari REST API a fürtön kap.

1. Egy, a fürthöz csatlakozó SSH-kapcsolaton használja a következő parancsot a `jq` segédprogram telepítéséhez. A segédprogram JSON-dokumentumok elemzésére használható, és hasznos a gazdagép adatainak lekéréséhez:
   
    ```bash
    sudo apt -y install jq
    ```

2. A környezeti változók fürtnévként történő beállítását az alábbi paranccsal végezheti el:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Ha a rendszer kéri, írja be az Apache Kafka-fürt nevét.

3. A környezeti változók Zookeeper-gazdagépadatokkal történő beállítását az alábbi paranccsal végezheti el:
    
    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    > [!TIP]
    > A parancs közvetlenül a fürt átjárócsomópontján kérdezi le az Ambari szolgáltatást. Az Ambarit a következő nyilvános címen is elérheti: `https://$CLUSTERNAME.azurehdinsight.net:80/`. Egyes hálózati beállítások megakadályozhatják a nyilvános cím elérését. Ilyen például a hálózati biztonsági csoportok (NSG-k) használata a HDInsighthoz való hozzáférés korlátozására a virtuális hálózatokon.

    Ha a rendszer kéri, adja meg a fürt bejelentkezési fiókjának a jelszavát (nem az SSH-fiókét).

    > [!NOTE]
    > Ez a parancs lekéri az összes Zookeeper-gazdagépet, majd csak az első két bejegyzést adja vissza. Ez azért van, mert hasznos lehet a redundancia, ha az egyik gazdagép esetleg nem érhető el.

4. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. A környezeti változók Apache Kafka-közvetítőgazdagépek adataival való beállításához használja az alábbi parancsot:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Ha a rendszer kéri, adja meg a fürt bejelentkezési fiókjának a jelszavát (nem az SSH-fiókét).

6. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
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

        > [!IMPORTANT]
        > Ha olyan Azure-régióban hozta létre a fürtöt, amely három tartalék tartományt biztosít, használjon 3-as replikációs tényezőt. Ellenkező esetben használjon 4-es replikációs tényezőt.
        
        A három tartalék tartományt tartalmazó régiókban a 3-as replikációs tényező lehetővé teszi, hogy a replikákat el lehessen osztani a tartalék tartományok között. A két tartalék tartományt tartalmazó régiókban a négyes replikációs tényező egyenlően osztja el a replikákat a tartományok között.
        
        Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) dokumentumban talál.

        > [!IMPORTANT] 
        > Az Apache Kafka nem észleli a tartalék Azure-tartományokat. Témakörök számára történő partícióreplikák létrehozásakor lehetséges, hogy a Kafka nem a magas rendelkezésre állásnak megfelelően osztja ki a replikákat.

        Magas rendelkezésre állás biztosítása érdekében használhatja a [Apache Kafka partíció-újraegyensúlyozó eszközt](https://github.com/hdinsight/hdinsight-kafka-tools). Ezt az eszközt egy SSH-kapcsolatból kell futtatni az Apache Kafka-fürt főcsomópontjához.

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

A Kafka témakörökben tárolja a *rekordokat*. A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. A létrehozók és a feldolgozók a *Kafka-közvetítő* szolgáltatással kommunikálnak. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Apache Kafka-közvetítőgazdagép.

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

    > [!NOTE]
    > Ha a Kafka régebbi verzióját használja, cserélje le a `--bootstrap-server $KAFKABROKERS` előtagot a következőre: `--zookeeper $KAFKAZKHOSTS`.

4. Használja a __Ctrl + C__ billentyűparancsot a fogyasztó leállításához.

Szoftveresen is létrehozhat előállítókat és fogyasztókat. Ez az API használatának példájáért lásd a [Apache Kafka Producer és Consumer API-hoz a HDInsight](apache-kafka-producer-consumer-api.md) dokumentum.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutató által létrehozott erőforrások törléséhez törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban.
> 
> Az Apache Kafka on HDInsight-fürt törlése a Kafkában tárolt összes adatot is törli.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Apache Spark használata az Apache kafka platformmal](../hdinsight-apache-kafka-spark-structured-streaming.md)

