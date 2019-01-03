---
title: Magas rendelkezésre állás a Hadoop – Azure HDInsight
description: Ismerje meg, hogyan HDInsight-fürtök megbízhatóságot és rendelkezésre állás javítása egy további fő csomópontjának használatával. Ismerje meg, hogyan csatlakozni külön-külön mindegyik átjárócsomóponthoz, SSH-val és az Ambari és a Hive, például Hadoop-szolgáltatásokhoz ez befolyásolja.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
keywords: hadoop magas rendelkezésre állás
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 89878b2774727d49d81ebec4c2a3c2cee355d8e8
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743663"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Rendelkezésre állás és megbízhatóság, a HDInsight Apache Hadoop-fürtök

HDInsight-fürtökön elérhető két fő csomópont a rendelkezésre állási és az Apache Hadoop-szolgáltatásokhoz és a futó feladatok megbízhatóságát.

Hadoop-szolgáltatásait és adatait egy fürtben több csomóponton replikálásával éri el magas rendelkezésre állás és megbízhatóság. Azonban a hadoop disztribúciók jellemzően az csak egy átjárócsomóponttal rendelkeznek. Az egyetlen átjárócsomóponthoz, bármilyen kimaradásról okozhat a fürt nem működik. HDInsight a Hadoop rendelkezésre állásának és megbízhatóságának javítása érdekében két átjárócsomópontokra biztosít.

[!INCLUDE [windows-retirement-notice](../../includes/windows-retirement-notice.md)]

## <a name="availability-and-reliability-of-nodes"></a>Rendelkezésre állás és megbízhatóság csomópontok

Egy HDInsight-fürtben található csomópontok az Azure Virtual Machines vannak megvalósítva. A következő részekben bemutatjuk a HDInsight együttes az egyes csomóponttípusok. 

> [!NOTE]  
> Nem minden csomóponttípusok egy fürttípus szolgálnak. Például egy Hadoop-fürt típusa nem rendelkezik egyetlen Nimbus csomópontot. További információ a HDInsight-fürttípusok által használt csomópontok fürt típusok című szakaszában talál a [Linux-alapú Hadoop-fürtök a HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentumot.

### <a name="head-nodes"></a>Átjárócsomópontok

Hadoop-szolgáltatásokhoz, magas rendelkezésre állás biztosítása érdekében a HDInsight két fő csomópont biztosít. Két fő csomópont egyszerre aktív és a HDInsight-fürtön belül futnak. Bizonyos szolgáltatások esetén például az Apache HDFS- vagy Apache Hadoop YARN, csak "aktív" az egyik fő csomópont egy adott időpontban. Egyéb szolgáltatások, például a hiveserver2-n vagy a Hive-Metaadattár aktívak a két fő csomópont egy időben.

Fő csomópont (és más csomópontjai a HDInsight) állomásneve a csomópont a részét képező numerikus értéknek kell tartoznia. Ha például `hn0-CLUSTERNAME` vagy `hn4-CLUSTERNAME`.

> [!IMPORTANT]  
> A numerikus értéket rendel egy csomópont-e elsődleges vagy másodlagos. A numerikus érték csak megtalálható annak minden egyes csomópont esetében egyedi nevét.

### <a name="nimbus-nodes"></a>Nimbus-csomópontok

Nimbus-csomópontok az Apache Storm-fürtök érhetők el. A Nimbus-csomópontok hasonló funkciókat nyújtanak, mint a Hadoop JobTracker terjesztése és feldolgozási figyelése a munkavégző csomópontok között. HDInsight két Nimbus csomóponttal rendelkezik kínál a Storm-fürtök

### <a name="apache-zookeeper-nodes"></a>Az Apache Zookeeper-csomópontok

[ZooKeeper](https://zookeeper.apache.org/) csomópontok használt vezetőválasztási az átjárócsomópontokhoz fő szolgáltatások. Annak érdekében, hogy szolgáltatásokat, az adatcsomópontok (munkavégző) és átjárók tudja, melyik átjárócsomópont szolgáltatás főkulcsának aktív a azok is használhatók. Alapértelmezés szerint a HDInsight három ZooKeeper-csomópontok biztosít.

### <a name="worker-nodes"></a>Munkavégző csomópontok

Munkavégző csomópontok a tényleges adatelemzés végrehajtása a feladat elküldésekor a fürthöz. Ha egy feldolgozó csomópont meghibásodik, az általa feladat elküldésekor egy másik munkavégző csomópont. Alapértelmezés szerint a HDInsight négy feldolgozó csomópontokat hoz létre. Ez a szám igényei alatt és fürt létrehozása után módosíthatja.

### <a name="edge-node"></a>Határcsomópont

Élcsomópont aktívan nem vesz részt a fürtön belüli adatok elemzéséhez. Akkor használják a fejlesztők és adatszakértők és a hadoop együttes használata során. Az élcsomópont az azonos Azure virtuális hálózatban, mint a fürt más csomópontjain él, és közvetlenül hozzáférhet az összes csomóponton. Az élcsomópont anélkül, hogy az erőforrások erről a kritikus fontosságú Hadoop-szolgáltatásokhoz vagy elemzési feladatokat is használható.

Machine Learning szolgáltatások a HDInsight jelenleg a csak fürt típusát, amely alapértelmezés szerint egy élcsomópontot biztosít. Machine Learning-szolgáltatásokhoz a HDInsight, az élcsomóponton használt R teszt kód helyileg, a csomópont, mielőtt elküldené azokat a fürt elosztott feldolgozásához.

Élcsomópont az és egyéb fürttípusok a további információkért lásd: a [élcsomópontok használata a HDInsight](hdinsight-apps-use-edge-node.md) dokumentumot.

## <a name="accessing-the-nodes"></a>A csomópontok elérése

Hozzáférés a fürthöz az interneten egy nyilvános átjárót nyugtázását. (Ha van ilyen) és hozzáférési korlátozódik az átjárócsomópontokat csatlakozik az élcsomóponthoz. A fő csomópontokon futó szolgáltatásokhoz való hozzáférés nem kellene több átjárócsomópontokhoz függvénye. A nyilvános átjárót irányítja a kérelmeket az átjárócsomóponthoz, amely a kért szolgáltatást futtatja. Ha például az Apache Ambari jelenleg a másodlagos átjárócsomóponthoz üzemelteti, ha az átjáró irányítja a kérelmeket az Ambari ahhoz a csomóponthoz.

A nyilvános átjárót keresztüli eléréshez port 443-as (HTTPS), a 22-es és a 23 korlátozódik.

* Port __443-as__ használatos az Ambari és egyéb webes felhasználói felületen vagy REST API-k, a központi csomóponton elérésére.

* Port __22-es__ használatával érheti el az elsődleges átjárócsomóponthoz vagy az élcsomópont SSH-n keresztül.

* Port __23__ használatával érheti el a másodlagos átjárócsomóponthoz SSH-n keresztül. Ha például `ssh username@mycluster-ssh.azurehdinsight.net` nevű fürtöt, az elsődleges átjárócsomóponthoz csatlakozik **sajátfürt**.

SSH használatával kapcsolatos további információkért lásd: a [az SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentumot.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Belső teljesen minősített tartománynevet (FQDN)

Egy HDInsight-fürtben található csomópontok rendelkezik egy belső IP-cím és teljes Tartománynevét, amely csak a fürtből. A fürtön, a belső teljes Tartományneve vagy IP-címet használó szolgáltatások elérésekor, ellenőrizheti a IP vagy FQDN a szolgáltatás eléréséhez használt Ambari kell használnia.

Például az Apache Oozie-szolgáltatás csak akkor futtathatnak egy fő csomópontot és a használatával a `oozie` parancsot egy SSH-munkamenetből a szolgáltatás URL-címe van szükség. Az URL-cím is lehet Ambariból lekért adatokkal a következő paranccsal:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Ez a parancs hasonló ad vissza értéket az alábbi parancsot, amely tartalmazza a belső URL-cím használata a `oozie` parancsot:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Az Ambari REST API-hoz való használatáról további információkért lásd: [figyelése és felügyelete HDInsight az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Más csomóponttípusok elérése

Csomópontok nem érhetők el közvetlenül az interneten keresztül az alábbi módszerek segítségével csatlakozhat:

* **SSH**: Ha csatlakoztatva van egy SSH-val átjárócsomóponthoz, ezután használhatja az SSH a fő csomópont kapcsolódni a fürt más csomópontjaira. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

* **SSH alagút**: Ha szeretne hozzáférni egy webes szolgáltatás egyik csomópontját, hogy nem lesz közzétéve az interneten lévő üzemeltetett, egy SSH-alagutat kell használnia. További információkért lásd: a [SSH-alagút használata a HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentumot.

* **Az Azure Virtual Network**: Ha a HDInsight-fürt az Azure virtuális hálózathoz, minden erőforrást azonos virtuális hálózaton közvetlenül hozzáférhet a fürt összes csomópontján. További információkért lásd: a [kiterjesztése HDInsight használata az Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentumot.

## <a name="how-to-check-on-a-service-status"></a>Hogyan lehet a szolgáltatás állapotának ellenőrzése

Az Ambari webes felület vagy az Ambari REST API használatával az átjárócsomópontokkal futó szolgáltatások állapotának ellenőrzéséhez.

### <a name="ambari-web-ui"></a>Az Ambari webes felhasználói felületen

Az Ambari webes Kezelőfelületen megtekinthető, https://CLUSTERNAME.azurehdinsight.net. Cserélje le a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg a HTTP felhasználói hitelesítő adatok a fürt számára. Az alapértelmezett HTTP-felhasználónév **rendszergazdai** és a jelszó a a fürt létrehozásakor megadott jelszót.

Az Ambari oldalon érkezésekor a telepített szolgáltatások listája látható az oldal bal oldalán.

![Telepített szolgáltatások](./media/hdinsight-high-availability-linux/services.png)

Nincsenek ikonok mellett egy szolgáltatás, amely állapotát jelzi az esetlegesen megjelenő sorozata. A szolgáltatás olyan riasztások megtekinthetők a a **riasztások** hivatkozásra az oldal tetején. Egyes szolgáltatások további információkat szeretne megtekinteni, választhat.

A szolgáltatás lapján információt nyújt az állapotára és az egyes szolgáltatásokat, bár nem biztosít melyik központi csomóponton fut a szolgáltatás az adatokat. Ezek az információk megtekintéséhez használja a **gazdagépek** hivatkozásra az oldal tetején. Ez a lap megjeleníti a gazdagépet a fürtöt, az átjárócsomópontokat.

![gazdagépek listájához](./media/hdinsight-high-availability-linux/hosts.png)

A szolgáltatások és az ezen a csomóponton futó összetevők számára az egyik fő csomópontot mutató hivatkozás jeleníti meg.

![Összetevő-állapot](./media/hdinsight-high-availability-linux/nodeservices.png)

Az Ambari használatával kapcsolatos további információkért lásd: [figyelése és kezelése a HDInsight az Apache Ambari webes kezelőfelületen](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Az Ambari REST API

Az Ambari REST API az interneten keresztül érhető el. A HDInsight nyilvános átjárót kezeli a fő csomópont jelenleg a REST API-t üzemeltető érkező kérések útválasztására.

Az alábbi parancs segítségével ellenőrizze a szolgáltatás állapotát, az Ambari REST API-n keresztül:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Cserélje le **jelszó** a HTTP (rendszergazdai) felhasználói fiók jelszavára.
* Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
* Cserélje le **SERVICENAME** állapotát ellenőrizni szeretné a szolgáltatás nevére.

Például állapotának ellenőrzéséhez a **HDFS** szolgáltatást a nevű fürtben **sajátfürt**, egy jelszót az **jelszó**, akkor használja a következő parancsot:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

A válasz a következő JSON hasonlít:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

Az URL-cím tudatja velünk, hogy a szolgáltatás jelenleg fut a fő csomópontot **hn0-CLUSTERNAME**.

Az állapot azt jelzi, hogy a szolgáltatás jelenleg fut, vagy **elindítva**.

Ha nem tudja, milyen szolgáltatások telepítve vannak a fürthöz, a következő paranccsal kérdezheti le:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Az Ambari REST API-hoz való használatáról további információkért lásd: [figyelése és felügyelete HDInsight az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Szolgáltatás-összetevők

Szolgáltatások állapotának ellenőrzéséhez külön-külön kívánt összetevőket tartalmazhatnak. Ha például a HDFS a NameNode összetevőt tartalmaz. Információk megtekintéséhez kattintson egy összetevő a következő paranccsal lehetséges:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Ha nem tudja, milyen összetevők által szolgáltatásként biztosított, a következő parancs segítségével kérdezheti le:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Az átjárócsomópontokkal naplófájlok elérése

### <a name="ssh"></a>SSH

Egy ssh-n keresztül átjárócsomóponthoz csatlakozik, miközben naplófájlok területen található **/var/log**. Ha például **/var/log/hadoop-yarn/yarn** a YARN naplók tartalmaznak.

Minden egyes átjárócsomópont van egyedi naplóbejegyzések, így ellenőrizze a naplókat is.

### <a name="sftp"></a>SFTP

Csatlakozás az átjárócsomóponthoz, az SSH File Transfer Protocol vagy biztonságos fájl Transfer Protocol (SFTP) használatával, és töltse le a naplófájlok közvetlenül is.

Hasonló használatát egy SSH-ügyféllel, amikor a fürthöz csatlakozik, meg kell adni, az SSH-felhasználói fiók nevét és a fürt SSH-címét. Például: `sftp username@mycluster-ssh.azurehdinsight.net`. Adja meg a fiók, amikor a rendszer kéri a jelszót, vagy adja meg a nyilvános kulcs használatával a `-i` paraméter.

A csatlakozás után megnyílik egy `sftp>` parancssort. A parancssorból, könyvtárak, módosíthatja, fájlok feltöltését és letöltését. Címtárak, módosítsa például a következő parancsokat a **/var/log/hadoop/hdfs** directory és az összes olyan fájl a könyvtárban, majd letöltése.

    cd /var/log/hadoop/hdfs
    get *

Az elérhető parancsok listájának megtekintéséhez írja be a `help` , a `sftp>` parancssort.

> [!NOTE]  
> Is találhatók grafikus felületek, amelyek lehetővé teszik, hogy a fájlrendszer használatakor SFTP használatával jelenítheti meg. Ha például [MobaXTerm](https://mobaxterm.mobatek.net/) lehetővé teszi, hogy a fájlrendszer egy felületen hasonlít a Windows Intézőben keresse meg.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Naplófájlok az Ambari megnyitásához az SSH-alagút kell használnia. A webes felületek, az egyes szolgáltatások nem érhetőek nyilvánosan az interneten. SSH-alagút használatával kapcsolatos információkért lásd: a [SSH-bújtatással](hdinsight-linux-ambari-ssh-tunnel.md) dokumentumot.

Az Ambari webes felhasználói Felületet jelölje ki a kívánt megtekinthetők a naplófájlok számára (például YARN) szolgáltatás. Ezután **Gyorshivatkozások** melyik fő csomópont számára a naplók megtekintéséhez válassza ki.

![A naplók megtekintéséhez Gyorshivatkozások használatával](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>A csomópont méretének konfigurálása

A csomópont mérete csak a fürt létrehozásakor lehet kiválasztani. A HDInsight a különböző elérhető Virtuálisgép-méretek listáját találja a [díjszabását ismertető lapon HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Amikor egy fürtöt hoz létre, megadhatja a csomópontok mérete. A következő információkat nyújt útmutatást a méret használatával megadása a [az Azure portal][preview-portal], [Azure PowerShell-lel][azure-powershell], és a [Azure klasszikus parancssori felület][azure-cli]:

* **Az Azure portal**: A fürt létrehozásakor megadhatja a fürt által használt csomópontok mérete:

    ![Méret kiválasztása a Fürtlétrehozási varázslóban képe](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Az Azure klasszikus parancssori felület**: Használatakor a `azure hdinsight cluster create` paranccsal beállíthatja a fő, feldolgozó és ZooKeeper-csomópontok mérete használatával a `--headNodeSize`, `--workerNodeSize`, és `--zookeeperNodeSize` paramétereket.

* **Az Azure PowerShell**: Használatakor a `New-AzureRmHDInsightCluster` parancsmaggal beállíthatja a fő, feldolgozó és ZooKeeper-csomópontok mérete használatával a `-HeadNodeVMSize`, `-WorkerNodeSize`, és `-ZookeeperNodeSize` paramétereket.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozások segítségével további információ az említett ebben a dokumentumban dolgokat.

* [Az Apache Ambari REST-referencia](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Telepítése és konfigurálása a klasszikus Azure-CLI](../cli-install-nodejs.md)
* [Telepítse és konfigurálja az Azure PowerShellt](/powershell/azure/overview)
* [A HDInsight az Apache Ambari kezelése](hdinsight-hadoop-manage-ambari.md)
* [Linux-alapú HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
