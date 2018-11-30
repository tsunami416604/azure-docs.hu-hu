---
title: Az Apache Kafka használatának első lépései – Rövid Azure HDInsight-útmutató
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Apache Kafka-fürtöt az Azure HDInsightban az Azure Portal használatával. A Kafka-témakörökről, -előfizetőkről és -fogyasztókról is olvashat.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: mvc,hdinsightactive
ms.topic: quickstart
ms.date: 04/16/2018
ms.openlocfilehash: 03d7c63d09ddaf64a552a4534612f0f7e9fd249b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312367"
---
# <a name="quickstart-create-an-apache-kafka-on-hdinsight-cluster"></a>Rövid útmutató: Apache Kafka on HDInsight-fürt létrehozása

[Az Apache Kafka](https://kafka.apache.org/) egy nyílt forráskódú, elosztott adatstreamelési platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkciókat kínál. 

Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre [Apache Kafka](https://kafka.apache.org)-fürtöt egy Azure Resource Manager-sablonnal. Azt is megtudhatja, hogyan küldhet és fogadhat üzeneteket a mellékelt segédprogramokkal a Kafka segítségével.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]
> A Kafka API csak az ugyanazon virtuális hálózaton belüli erőforrások számára érhető el. Ebben a rövid útmutatóban közvetlenül éri el a fürtöt SSH-val. Ha más szolgáltatásokat, hálózatokat vagy virtuális gépeket szeretne csatlakoztatni a Kafkához, először létre kell hoznia egy virtuális hálózatot, majd létre kell hoznia a hálózaton belüli erőforrásokat.
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

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-kafka-java-get-started%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/apache-kafka-quickstart-resource-manager-template/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. A Kafka-fürt létrehozásához használja a következő értékeket:

    | Tulajdonság | Érték |
    | --- | --- |
    | Előfizetés | Az Azure-előfizetése. |
    | Erőforráscsoport | Az az erőforráscsoport, amelyben a fürt létrejön. |
    | Hely | Az az Azure-régió, amelyben a fürt létrejön. |
    | Fürt neve | A Kafka-fürt neve. |
    | Fürt bejelentkezési felhasználóneve | A fürtön futtatott HTTPs-alapú szolgáltatásokba való bejelentkezéshez használt fióknév. |
    | Fürt bejelentkezési jelszava | A bejelentkezési felhasználónévhez tartozó jelszó. |
    | SSH-felhasználónév | Az SSH-felhasználónév. Ez a fiók SSH-val fér hozzá a fürthöz. |
    | SSH-jelszó | Az SSH-felhasználó jelszava. |

    ![A sablontulajdonságok képernyőképe](./media/apache-kafka-quickstart-resource-manager-template/kafka-template-parameters.png)

3. Jelölje be az **Elfogadom a fenti feltételeket** és a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Vásárlás** elemre.

> [!NOTE]
> A fürt létrehozása 20 percig is eltarthat.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

1. A Kafka-fürt elsődleges átjárócsomópontjához való csatlakozáshoz használja a következő parancsot. Cserélje le az `sshuser` elemet az SSH-felhasználónévre. Cserélje le a `mykafka` elemet a Kafka-fürt nevére.

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
    http://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Az Apache Zookeeper és a közvetítő gazdagép információinak lekérése

A Kafka használata során ismernie kell a *Apache Zookeeper* és *Broker* gazdagépek. A Kafka API és a Kafkában elérhető számos segédprogram használja ezeket a gazdagépeket.

Ebben a szakaszban a fürtön lévő Ambari REST API-ból szerzi be a gazdagép információit.

1. Egy, a fürthöz csatlakozó SSH-kapcsolaton használja a következő parancsot a `jq` segédprogram telepítéséhez. A segédprogram JSON-dokumentumok elemzésére használható, és hasznos a gazdagép adatainak lekéréséhez:
   
    ```bash
    sudo apt -y install jq
    ```

2. A környezeti változók fürtnévként történő beállítását az alábbi paranccsal végezheti el:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Ha a rendszer kéri, írja be a Kafka-fürt nevét.

3. A környezeti változók Zookeeper-gazdagépadatokkal történő beállítását az alábbi paranccsal végezheti el:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ha a rendszer kéri, adja meg a fürt bejelentkezési fiókjának a jelszavát (nem az SSH-fiókét).

    > [!NOTE]
    > Ez a parancs lekéri az összes Zookeeper-gazdagépet, majd csak az első két bejegyzést adja vissza. Ez azért van, mert hasznos lehet a redundancia, ha az egyik gazdagép esetleg nem érhető el.

4. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. A környezeti változók Kafka közvetítőgazdagép-adatokkal történő beállítását az alábbi paranccsal végezheti el:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
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

    Ez a parancs a `$KAFKAZKHOSTS`-ban tárolt gazdagépadatok használatával kapcsolódik a Zookeeperhez, majd létrehoz egy **test** nevű Kafka-témakört. 

    * A témakörben tárolt adatok nyolc partícióban vannak elosztva.

    * Mindegyik partíció három feldolgozó csomópontra van replikálva a fürtben.

        > [!IMPORTANT]
        > Ha olyan Azure-régióban hozta létre a fürtöt, amely három tartalék tartományt biztosít, használjon 3-as replikációs tényezőt. Ellenkező esetben használjon 4-es replikációs tényezőt.
        
        A három tartalék tartományt tartalmazó régiókban a 3-as replikációs tényező lehetővé teszi, hogy a replikákat el lehessen osztani a tartalék tartományok között. A két tartalék tartományt tartalmazó régiókban a négyes replikációs tényező egyenlően osztja el a replikákat a tartományok között.
        
        Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) dokumentumban talál.

        > [!IMPORTANT] 
        > A Kafka nem észleli a tartalék Azure-tartományokat. Témakörök számára történő partícióreplikák létrehozásakor lehetséges, hogy a Kafka nem a magas rendelkezésre állásnak megfelelően osztja ki a replikákat.

        Magas rendelkezésre állás biztosítása érdekében használhatja a [Apache Kafka partíció-újraegyensúlyozó eszközt](https://github.com/hdinsight/hdinsight-kafka-tools). Ezt az eszközt egy SSH-kapcsolatból kell futtatni a Kafka-fürt fő csomópontjához.

        A Kafka-adatok lehető legmagasabb rendelkezésre állása érdekében egyensúlyozza újra a témaköre partícióreplikáit a következő esetekben:

        * Új témakör vagy partíció létrehozásakor

        * Fürt vertikális felskálázásakor

* **A témakörök listázásához** használja az alábbi parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Ez a parancs listázza a Kafka-fürtön elérhető témaköröket.

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

A Kafka témakörökben tárolja a *rekordokat*. A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. A létrehozók és a feldolgozók a *Kafka-közvetítő* szolgáltatással kommunikálnak. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő gazdagép.

Kövesse az alábbi lépéseket a rekordoknak a korábban létrehozott test témakörben való tárolására, majd a beolvasásukra egy fogyasztó használatával:

1. Ha rekordokat szeretne írni a témakörbe, használja az SSH-kapcsolat `kafka-console-producer.sh` segédprogramját:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    A parancs kiadása után egy üres sor jelenik meg.

2. Írjon be egy szöveges üzenetet az üres sorba, majd nyomja le az Enter billentyűt. Írjon be így még néhány szöveges üzenetet, majd a **Ctrl + C** billentyűparancs használatával térjen vissza a szokásos parancssorhoz. A rendszer minden sort külön rekordként küld el a Kafka-témakörbe.

3. Ha rekordokat szeretne olvasni a témakörből, használja az SSH-kapcsolat `kafka-console-consumer.sh` segédprogramját:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    A parancs lekéri a rekordokat a témakörből, majd megjeleníti őket. A `--from-beginning` használata arra utasítja a fogyasztót, hogy a stream elejétől kezdje a műveletet, így az összes rekord lekérése megtörténik.

    > [!NOTE]
    > Ha a Kafka régebbi verzióját használja, cserélje le a `--bootstrap-server $KAFKABROKERS` előtagot a következőre: `--zookeeper $KAFKAZKHOSTS`.

4. Használja a __Ctrl + C__ billentyűparancsot a fogyasztó leállításához.

Szoftveresen is létrehozhat előállítókat és fogyasztókat. Ez az API használatának példájáért lásd a [Apache Kafka Producer és Consumer API-hoz a HDInsight](apache-kafka-producer-consumer-api.md) dokumentum.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni kívánja a jelen rövid útmutató által létrehozott erőforrásokat, akkor törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban.
> 
> A Kafka on HDInsight-fürt törlése a Kafkában tárolt összes adatot is törli.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Apache Spark használata az Apache kafka platformmal](../hdinsight-apache-kafka-spark-structured-streaming.md)

