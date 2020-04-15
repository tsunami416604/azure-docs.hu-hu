---
title: Magas rendelkezésre állás a Hadoop számára - Azure HDInsight
description: Ismerje meg, hogy a HDInsight-fürtök hogyan növelik a megbízhatóságot és a rendelkezésre állást egy további főcsomópont használatával. Ismerje meg, hogy ez hogyan befolyásolja a Hadoop-szolgáltatásokat, például az Ambari-t és a Hive-t, valamint hogyan csatlakozhat egyénileg az egyes főcsomópontokhoz az SSH használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop magas rendelkezésre állás
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 767d87efcf94d720159dcf3b9dc42981ec957ef0
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381395"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Az Apache Hadoop-fürtök elérhetősége és megbízhatósága a HDInsightban

A HDInsight-fürtök két fő csomópontot biztosítanak az Apache Hadoop-szolgáltatások és futó feladatok rendelkezésre állásának és megbízhatóságának növelése érdekében.

A Hadoop magas rendelkezésre állást és megbízhatóságot ér el a szolgáltatások és az adatok replikálásával a fürt több csomópontján. A Hadoop standard disztribúciói azonban általában csak egyetlen főcsomódval rendelkeznek. Az egyfej-csomópont bármilyen kimaradása a fürt leállását okozhatja. A HDInsight két headnode-t biztosít a Hadoop rendelkezésre állásának és megbízhatóságának javítása érdekében.

## <a name="availability-and-reliability-of-nodes"></a>A csomópontok rendelkezésre állása és megbízhatósága

A HDInsight-fürt csomópontjai az Azure virtuális gépei használatával vannak megvalósítva. A következő szakaszok a HDInsight ban használt egyes csomóponttípusokat ismertetik.

> [!NOTE]  
> Nem minden csomóponttípus van használva egy fürttípushoz. Egy Hadoop-fürttípuspéldául nem rendelkezik Nimbus-csomókkal. A HDInsight-fürttípusok által használt csomópontokról további információt a [Linux-alapú Hadoop-fürtök létrehozása a HDInsight-dokumentumban](hdinsight-hadoop-provision-linux-clusters.md#cluster-type) található Fürttípusok című szakaszban talál.

### <a name="head-nodes"></a>Fő csomópontok

A Hadoop-szolgáltatások magas rendelkezésre állásának biztosítása érdekében a HDInsight két főcsomópontot biztosít. Mindkét főcsomópont aktív és fut a HDInsight-fürtön belül egyszerre. Egyes szolgáltatások, például az Apache HDFS vagy az Apache Hadoop YARN, egy adott időpontban csak egy főcsomóponton "aktívak". Más szolgáltatások, például a HiveServer2 vagy a Hive MetaStore egyszerre mindkét főcsomóponton aktívak.

A fürt különböző csomóponttípusainak állomásneveinek beszerzéséhez használja az [Ambari REST API-t.](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)

> [!IMPORTANT]  
> Ne társítsa a numerikus értéket ahhoz, hogy egy csomópont elsődleges vagy másodlagos. A numerikus érték csak akkor van jelen, hogy egyedi nevet adjon az egyes csomópontoknak.

### <a name="nimbus-nodes"></a>Nimbus csomópontok

A Nimbus-csomópontok Apache Storm-fürtökkel érhetők el. A Nimbus-csomópontok a Hadoop JobTracker-hez hasonló funkciókat biztosítanak a feldolgozócsomópontok közötti feldolgozás elosztásával és figyelésével. A HDInsight két Nimbus-csomópontot biztosít storm-fürtökhöz

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper csomópontok

[ZooKeeper](https://zookeeper.apache.org/) csomópontok használják vezető választás a fő szolgáltatások vezető csomópontok. Ők is használják annak biztosításához, hogy a szolgáltatások, adatok (dolgozó) csomópontok és átjárók tudják, hogy melyik fő csomópont on a fő szolgáltatás aktív. Alapértelmezés szerint a HDInsight három ZooKeeper-csomópontot biztosít.

### <a name="worker-nodes"></a>Dolgozó csomópontok

A munkavégző csomópontok a tényleges adatelemzést akkor hajtják végre, amikor egy feladatot elküldanek a fürtnek. Ha egy munkavégző csomópont meghibásodik, a feladat, amelyet elvégzett, egy másik munkavégző csomópontnak lesz elküldve. Alapértelmezés szerint a HDInsight négy munkavégző csomópontot hoz létre. Ezt a számot módosíthatja úgy, hogy megfeleljen az igényeinek a fürt létrehozása során és után is.

### <a name="edge-node"></a>peremcsomópont

A peremhálózati csomópont nem vesz aktívan részt a fürtön belüli adatelemzésben. A Hadoop-al való munka során fejlesztők vagy adatszakértők használják. A peremhálózati csomópont ugyanabban az Azure virtuális hálózatban él, mint a fürt többi csomópontja, és közvetlenül hozzáférhet az összes többi csomóponthoz. A peremhálózati csomópont anélkül használható, hogy erőforrásokat nem venné el a kritikus Hadoop-szolgáltatásoktól vagy elemzési feladatoktól.

Jelenleg a HDInsight ML-szolgáltatásai az egyetlen olyan fürttípus, amely alapértelmezés szerint peremcsomópontot biztosít. A HDInsight ML-szolgáltatások esetében a peremhálózati csomópontot helyileg használják az R-kód a csomóponton, mielőtt elosztott feldolgozásra elküldené a fürtnek.

A peremhálózati csomópontok más fürttípusokkal való használatáról a [PEREMhálózati csomópontok használata a HDInsight-dokumentumban című témakörben talál.](hdinsight-apps-use-edge-node.md)

## <a name="accessing-the-nodes"></a>A csomópontok elérése

A fürthöz való hozzáférés az interneten keresztül nyilvános átjárón keresztül történik. A hozzáférés a főcsomópontokhoz való csatlakozásra korlátozódik, és ha van ilyen, a peremcsomópontra. A vezető csomópontokon futó szolgáltatásokhoz való hozzáférést nem érinti a több főcsomópont. A nyilvános átjáró a kéréseket a kért szolgáltatást kiszolgáló főcsomóponthoz irányítja. Ha például az Apache Ambari jelenleg a másodlagos főcsomóponton található, az átjáró az Ambari bejövő kérelmeit az adott csomóponthoz irányítja.

A nyilvános átjárón keresztüli hozzáférés a 443-as (HTTPS), 22-es és 23-as portokra korlátozódik.

|Port |Leírás |
|---|---|
|443|A fő csomópontokon üzemeltetett Ambari és más webes felhasználói felület i vagy REST API-k eléréséhez használható.|
|22|Az elsődleges főcsomópont vagy az SSH-val rendelkező peremcsomópont elérésére szolgál.|
|23|A másodlagos főcsomópont SSH-val való eléréséhez használható. Például `ssh username@mycluster-ssh.azurehdinsight.net` csatlakozik a fürt elsődleges főcsomópontjához, a **mycluster-hez.**|

Az SSH használatáról az [SSH használata a HDInsight-dokumentumban](hdinsight-hadoop-linux-use-ssh-unix.md) talál további információt.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Belső, teljesen minősített tartománynevek (FQDN)

A HDInsight-fürt csomópontjai belső IP-címmel és teljes tartománynévvel rendelkeznek, amely csak a fürtből érhető el. Ha a fürt szolgáltatásait a belső teljes tartománynhálózat vagy IP-cím használatával éri el, az Ambari használatával ellenőrizze az IP-címet vagy a teljes tartománynnt a szolgáltatás elérésekor használandó.

Például az Apache Oozie szolgáltatás csak egy főcsomóponton `oozie` futtatható, és egy SSH-munkamenet ből származó parancs használatához szükség van a szolgáltatás URL-címére. Ez az URL-cím az Ambari-ból a következő paranccsal olvasható be:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Ez a parancs a következőhöz hasonló értéket ad vissza, amely a `oozie` paranccsal használandó belső URL-címet tartalmazza:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Az Ambari REST API-val való munkáról további információt a [HDInsight figyelése és kezelése az Apache Ambari REST API használatával című témakörben talál.](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="accessing-other-node-types"></a>Egyéb csomóponttípusok elérése

Az alábbi módszerekkel csatlakozhat olyan csomópontokhoz, amelyek nem érhetők el közvetlenül az interneten keresztül:

|Módszer |Leírás |
|---|---|
|SSH|Miután ssh-t használó főcsomóponthoz csatlakozott, a főcsomóponts SSH segítségével csatlakozhat a fürt más csomópontjaihoz. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).|
|SSH alagút|Ha az internetnek nem elérhető csomópontok egyikén üzemeltetett webszolgáltatásra van szüksége, sSH-alagutat kell használnia. További információ: [SSH-alagút használata HDInsight-dokumentummal.](hdinsight-linux-ambari-ssh-tunnel.md)|
|Azure Virtual Network|Ha a HDInsight-fürt egy Azure virtuális hálózat része, az ugyanazon a virtuális hálózaton lévő bármely erőforrás közvetlenül hozzáférhet a fürt összes csomópontjához. További információt a [HDInsight-hálózat megtervezése](hdinsight-plan-virtual-network-deployment.md) című dokumentumban talál.|

## <a name="how-to-check-on-a-service-status"></a>Szolgáltatásállapot ának ellenőrzése

A fő csomópontokon futó szolgáltatások állapotának ellenőrzéséhez használja az Ambari web felhasználói felületét vagy az Ambari REST API-t.

### <a name="ambari-web-ui"></a>Ambari webes felhasználói felület

Az Ambari Web felhasználói felülete a. `https://CLUSTERNAME.azurehdinsight.net` Cserélje le a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg a fürt HTTP-felhasználói hitelesítő adatait. Az alapértelmezett HTTP-felhasználónév **a rendszergazda,** a jelszó pedig a fürt létrehozásakor megadott jelszó.

Amikor megérkezik az Ambari oldalra, a telepített szolgáltatások a lap bal oldalán jelennek meg.

![Apache Ambari telepített szolgáltatások](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Egy szolgáltatás mellett ikonok sora jelenhet meg az állapot jelzésére. A szolgáltatással kapcsolatos riasztások az oldal tetején található Riasztások hivatkozással **tekinthetők** meg.  Az Ambari számos előre definiált riasztást kínál.

A következő riasztások segítenek a fürt rendelkezésre állásának figyelésében:

| Riasztás neve                               | Leírás                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Metrikus figyelő állapota                    | Ez a riasztás a Metrikák figyelési folyamat állapotát jelzi a figyelő állapotparancsfájlja szerint.                                                                                   |
| Ambari ügynök Szívverés                   | Ez a riasztás akkor aktiválódik, ha a kiszolgáló elvesztette a kapcsolatot egy ügynökkel.                                                                                                                        |
| ZooKeeper kiszolgáló folyamata                 | Ez az állomásszintű riasztás akkor aktiválódik, ha a ZooKeeper kiszolgáló folyamat nem állapítható meg, hogy a hálózaton, és figyel.                                                               |
| IoCache metaadat-kiszolgáló állapota           | Ez az állomásszintű riasztás akkor aktiválódik, ha az IOCache metaadat-kiszolgálója nem állapítható meg, hogy felvan-e, és válaszol az ügyfélkérésekre                                                            |
| JournalNode webes felhasználói felület                       | Ez az állomásszintű riasztás akkor aktiválódik, ha a JournalNode web felhasználói felülete nem érhető el.                                                                                                                 |
| Spark2 takarékossági kiszolgáló                     | Ez az állomásszintű riasztás akkor aktiválódik, ha a Spark2 thrift kiszolgáló nem állapítható meg, hogy fel.                                                                                                |
| Előzménykiszolgálófolyamat                   | Ez az állomásszintű riasztás akkor aktiválódik, ha az Előzménykiszolgáló folyamat nem állapítható meg, hogy a hálózaton legyen, és figyelje.                                                                |
| Előzménykiszolgáló webfelhasználói felülete                    | Ez az állomásszintű riasztás akkor aktiválódik, ha az Előzménykiszolgáló webes felhasználói felülete nem érhető el.                                                                                                              |
| `ResourceManager`Webes felhasználói felület                   | Ez az állomásszintű riasztás akkor `ResourceManager` aktiválódik, ha a webes felhasználói felület nem érhető el.                                                                                                             |
| NodeManager állapotösszefoglalója               | Ez a szolgáltatásszintű riasztás akkor aktiválódik, ha nem megfelelő a nodemanagerek                                                                                                                    |
| Az Alkalmazás idővonalának webes felhasználói felülete                      | Ez az állomásszintű riasztás akkor aktiválódik, ha az Alkalmazásidővonal-kiszolgáló webes felhasználói felülete nem érhető el.                                                                                                         |
| DataNode állapot összefoglalása                  | Ez a szolgáltatásszintű riasztás akkor aktiválódik, ha nem megfelelő datanodes                                                                                                                       |
| NameNode webes felhasználói felület                          | Ez az állomásszintű riasztás akkor aktiválódik, ha a NameNode web felhasználói felülete nem érhető el.                                                                                                                    |
| ZooKeeper feladatátvevő vezérlő folyamata    | Ez az állomásszintű riasztás akkor aktiválódik, ha a ZooKeeper feladatátvevő vezérlő folyamat nem lehet megerősíteni, hogy a hálózaton, és figyel.                                                   |
| Oozie kiszolgáló webes felhasználói felülete                      | Ez az állomásszintű riasztás akkor aktiválódik, ha az Oozie-kiszolgáló webes felhasználói felülete nem érhető el.                                                                                                                |
| Oozie kiszolgáló állapota                      | Ez a gazdagépszintű riasztás akkor aktiválódik, ha az Oozie-kiszolgáló nem állapítható meg, hogy fel, és válaszol az ügyfél kérésekre.                                                                      |
| Hive metastore folyamat                   | Ez a gazdagépszintű riasztás akkor aktiválódik, ha a Hive Metastore folyamat nem állapítható meg, hogy a hálózaton, és figyel.                                                                 |
| HiveServer2 folyamat                      | Ez az állomásszintű riasztás akkor aktiválódik, ha a HiveServer nem állapítható meg, hogy fel, és válaszol az ügyfél kérésekre.                                                                        |
| WebHCat kiszolgáló állapota                    | Ez az állomásszintű riasztás akkor `templeton` aktiválódik, ha a kiszolgáló állapota nem kifogástalan.                                                                                                            |
| Elérhető ZooKeeper-kiszolgálók százalékos százaléka      | Ez a riasztás akkor aktiválódik, ha a fürtben lévő ZooKeeper-kiszolgálók száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a ZooKeeper folyamatellenőrzések eredményeit.     |
| Spark2 Livy kiszolgáló                       | Ez az állomásszintű riasztás akkor aktiválódik, ha a Livy2 server nem állapítható meg, hogy fel.                                                                                                        |
| Spark2 előzménykiszolgálója                    | Ez az állomásszintű riasztás akkor aktiválódik, ha a Spark2 előzménykiszolgáló nem állapítható meg, hogy fel.                                                                                               |
| Metrikák gyűjtői folyamata                | Ez a riasztás akkor aktiválódik, ha a metrikagyűjtő nem lehet megerősíteni, hogy a beállított porton a küszöbértéknek megfelelő számú másodpercben.                                 |
| Metrikák gyűjtője - HBase fő folyamat | Ez a riasztás akkor aktiválódik, ha a metrikagyűjtő HBase fő folyamatok nem lehet megerősíteni, hogy a rendszer a hálózaton a beállított kritikus küszöbértéket, másodpercben megadott. |
| Elérhető százalékmérők figyelői       | Ez a riasztás akkor aktiválódik, ha a Metrikák figyelési folyamatainak egy százaléka nem, és figyeli a hálózaton a beállított figyelmeztetési és kritikus küszöbértékeket.                             |
| Elérhető nodemanagerek százalékos száma           | Ez a riasztás akkor aktiválódik, ha a fürtben lévő NodeManagers lefelé lévő kezelők száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a NodeManager folyamatellenőrzések eredményeit.        |
| NodeManager állapota                       | Ez az állomásszintű riasztás ellenőrzi a NodeManager összetevőből elérhető csomópont-állapottulajdonságot.                                                                                              |
| NodeManager webes felhasználói felület                       | Ez az állomásszintű riasztás akkor aktiválódik, ha a NodeManager webfelhasználói felület nem érhető el.                                                                                                                 |
| NameNode magas rendelkezésre állási állapot        | Ez a szolgáltatásszintű riasztás akkor aktiválódik, ha az Aktív nameNode vagy a Készenléti névcsomópont nem fut.                                                                                     |
| DataNode folyamat                         | Ez az állomásszintű riasztás akkor aktiválódik, ha az egyes DataNode folyamatok nem lehet létrehozni, hogy a hálózaton, és figyel.                                                         |
| DataNode webes felhasználói felülete                          | Ez az állomásszintű riasztás akkor aktiválódik, ha a DataNode web felhasználói felülete nem érhető el.                                                                                                                    |
| Rendelkezésre álló százaléknaplónodes           | Ez a riasztás akkor aktiválódik, ha a journalnodes a fürtben nagyobb, mint a beállított kritikus küszöbértéket. Összesíti a JournalNode folyamatellenőrzések eredményeit.        |
| Rendelkezésre álló adatcsomópontok százalékos száma              | Ez a riasztás akkor aktiválódik, ha a fürtben lévő datanodes-ok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a DataNode folyamatellenőrzések eredményeit.              |
| Zeppelin kiszolgáló állapota                   | Ez az állomásszintű riasztás akkor aktiválódik, ha a Zeppelin-kiszolgáló nem állapítható meg, hogy fel, és válaszol az ügyfél kérésekre.                                                                   |
| HiveServer2 interaktív folyamat          | Ez az állomásszintű riasztás akkor aktiválódik, ha a HiveServerInteractive nem állapítható meg, hogy fel, és válaszol az ügyfél-kérelmekre.                                                             |
| LLAP alkalmazás                         | Ez a riasztás akkor aktiválódik, ha az LLAP alkalmazás nem állapítható meg, hogy fel, és válaszol a kérelmekre.                                                                                    |

Kiválaszthatja az egyes szolgáltatások megtekintéséhez további információkat is.

Bár a szolgáltatáslap az egyes szolgáltatások állapotáról és konfigurációjával kapcsolatos információkat tartalmaz, nem ad információt arról, hogy a szolgáltatás melyik főcsomóponton fut. Az információk megtekintéséhez használja a lap tetején található **Hosts** hivatkozást. Ez a lap a fürtön belüli állomásokat jeleníti meg, beleértve a főcsomópontokat is.

![Apache Ambari headnode hosts list](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Az egyik főcsomópont hivatkozásának kiválasztása az adott csomóponton futó szolgáltatásokat és összetevőket jeleníti meg.

![Apache Ambari összetevő állapota](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Az Ambari használatáról további információt [a HDInsight figyelése és kezelése az Apache Ambari Web felhasználói felületén című témakörben talál.](hdinsight-hadoop-manage-ambari.md)

### <a name="ambari-rest-api"></a>Ambari REST API

Az Ambari REST API az interneten keresztül érhető el. A HDInsight nyilvános átjáró kezeli az útválasztási kérelmeket a fő csomópont, amely jelenleg a REST API-t.

A következő paranccsal ellenőrizheti egy szolgáltatás állapotát az Ambari REST API-n keresztül:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Cserélje le a **PASSWORD-t** a HTTP-felhasználói (rendszergazdai) fiók jelszavára.
* Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
* Cserélje le a **SERVICENAME** nevet annak a szolgáltatásnak a nevére, amelynek állapotát ellenőrizni szeretné.

Ha például egy **mycluster**nevű fürtön szeretné ellenőrizni a **HDFS-szolgáltatás** állapotát **jelszójelszóval,** a következő parancsot kell használnia:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

A válasz hasonló a következő JSON-hoz:

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

Az URL azt mondja, hogy a szolgáltatás jelenleg fut a fej csomópont nevű **mycluster.wutj3h4ic1zejluqhxxvckxq0g**.

Az állapot azt mondja, hogy a szolgáltatás jelenleg fut, vagy **STARTED**.

Ha nem tudja, hogy milyen szolgáltatások vannak telepítve a fürtre, a következő paranccsal lekérheti a listát:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Az Ambari REST API-val való munkáról további információt a [HDInsight figyelése és kezelése az Apache Ambari REST API használatával című témakörben talál.](hdinsight-hadoop-manage-ambari-rest-api.md)

#### <a name="service-components"></a>Szolgáltatás-összetevők

A szolgáltatások tartalmazhatnak olyan összetevőket, amelyek állapotát egyenként szeretné ellenőrizni. A HDFS például tartalmazza a NameNode összetevőt. Az összetevőre vonatkozó információk megtekintéséhez a parancs a következő:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Ha nem tudja, hogy egy szolgáltatás milyen összetevőket biztosít, a következő paranccsal lekérheti a listát:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>A naplófájlok elérése a főcsomópontokon

### <a name="ssh"></a>SSH

Miközben egy sSH-on keresztül idén keresztül egy főcsomóponthoz csatlakozik, a naplófájlok a **/var/log**könyvtár ban találhatók. Például a **/var/log/hadoop-yarn/yarn** a YARN-hoz tartalmaz rönköket.

Minden főcsomópont rendelkezhet egyedi naplóbejegyzésekkel, ezért mindkettőn ellenőrizze a naplókat.

### <a name="sftp"></a>SFTP

A főcsomóponthoz az SSH Fájlátviteli protokoll vagy a Secure File Transfer Protocol (SFTP) protokoll segítségével is csatlakozhat, és közvetlenül letöltheti a naplófájlokat.

Az SSH-ügyfél hez hasonlóan a fürthöz való csatlakozáskor meg kell adnia az SSH felhasználói fiók nevét és a fürt SSH-címét. Például: `sftp username@mycluster-ssh.azurehdinsight.net`. Amikor a rendszer kéri, adja meg a fiók `-i` jelszavát, vagy adjon meg egy nyilvános kulcsot a paraméter használatával.

A csatlakozás után egy `sftp>` üzenet jelenik meg. Ebből a kérdésből módosíthatja a könyvtárakat, feltölthet és letöltheti a fájlokat. A következő parancsok például a **könyvtárakat a /var/log/hadoop/hdfs** könyvtárra módosítják, majd letöltik a könyvtárösszes fájlját.

    cd /var/log/hadoop/hdfs
    get *

Az elérhető parancsok listájának `help` megadásához írja be a `sftp>` parancssorba.

> [!NOTE]  
> Vannak olyan grafikus felületek is, amelyek lehetővé teszik a fájlrendszer megjelenítését, ha SFTP-vel csatlakozik. A [MobaXTerm](https://mobaxterm.mobatek.net/) például lehetővé teszi a fájlrendszer böngészését a Windows Intézőhöz hasonló felülethasználatával.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> A naplófájlok ambari használatával való eléréséhez SSH-alagutat kell használnia. Az egyes szolgáltatások webes felületei nem nyilvánosan elérhetők az interneten. Az SSH-alagutak használatáról az [SSH bújtatás használata](hdinsight-linux-ambari-ssh-tunnel.md) című dokumentumban talál további információt.

Az Ambari Web felhasználói felületén válassza ki azt a szolgáltatást, amelynek naplóit meg szeretné tekinteni (például YARN). Ezután a **Gyorshivatkozások segítségével** válassza ki, hogy melyik főcsomóponthoz szeretné megtekinteni a naplókat.

![Gyorshivatkozások használata a naplók megtekintéséhez](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>A csomópont méretének konfigurálása

A csomópont mérete csak a fürt létrehozása során választható ki. A [HDInsight díjszabási lapján](https://azure.microsoft.com/pricing/details/hdinsight/)megtalálhatja a HDInsight számára elérhető különböző virtuálisgép-méretek listáját.

Fürt létrehozásakor megadhatja a csomópontok méretét. Az alábbi információk útmutatást adnak a méret megadásához az [Azure Portal](https://portal.azure.com/), az [Azure PowerShell-modul Az](/powershell/azureps-cmdlets-docs)és az Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával:

* **Azure Portal:** Fürt létrehozásakor beállíthatja a fürt által használt csomópontok méretét:

    ![A fürtlétrehozó varázsló képe csomópontméret-kijelöléssel](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI:** A [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) parancs használatakor beállíthatja a fej, a feldolgozó és a `--headnode-size`ZooKeeper csomópontok méretét a , `--workernode-size`és `--zookeepernode-size` a paraméterek használatával.

* **Azure PowerShell:** A [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsmag használatakor beállíthatja a fej, a feldolgozó és a `-HeadNodeSize`ZooKeeper csomópontok méretét a , `-WorkerNodeSize`és `-ZookeeperNodeSize` a paraméterek használatával.

## <a name="next-steps"></a>További lépések

A cikkben tárgyalt elemekről az:

* [Apache Ambari REST referencia](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Az Azure CLI telepítése és konfigurálása](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Az Azure PowerShell-modul telepítése és konfigurálása](/powershell/azure/overview)
* [HdInsight kezelése az Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)
* [Linux alapú HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md)
