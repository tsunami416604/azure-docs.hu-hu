---
title: Magas rendelkezésre állás a Hadoop – Azure HDInsight
description: Ismerje meg, hogy a HDInsight-fürtök hogyan javítják a megbízhatóságot és a rendelkezésre állást egy további fő csomópont használatával. Ismerje meg, hogy ez milyen hatással van a Hadoop-szolgáltatásokra, például a Ambari és a kaptárra, valamint az egyes fő csomópontok SSH-val való összekapcsolásának módjára.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Hadoop magas rendelkezésre állása
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 21ecf33291924097f076aa28088eb4eac652ce67
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849660"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Apache Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsight-ben

A HDInsight-fürtök két fő csomópontot biztosítanak a Apache Hadoop szolgáltatások és a rendszert futtató feladatok rendelkezésre állásának és megbízhatóságának növeléséhez.

A Hadoop magas rendelkezésre állást és megbízhatóságot biztosít a szolgáltatások és az adatszolgáltatások fürtön belüli több csomóponton történő replikálásával. A Hadoop standard eloszlása azonban általában csak egyetlen fő csomóponttal rendelkezik. Az egyetlen fő csomópont meghibásodása miatt a fürt leállhat. A HDInsight két átjárócsomópontokkal biztosít a Hadoop rendelkezésre állásának és megbízhatóságának javítása érdekében.

## <a name="availability-and-reliability-of-nodes"></a>A csomópontok rendelkezésre állása és megbízhatósága

A HDInsight-fürtök csomópontjai az Azure Virtual Machines használatával valósíthatók meg. A következő fejezetek a HDInsight-mel használt egyes csomópont-típusokat tárgyalják.

> [!NOTE]  
> Nem minden csomópont-típust használ a fürt típusához. Például egy Hadoop-fürt típusa nem rendelkezik Nimbus-csomópontokkal. A HDInsight-fürtök által használt csomópontokkal kapcsolatos további információkért tekintse meg a [Linux-alapú Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type) dokumentumban című szakaszt.

### <a name="head-nodes"></a>Fő csomópontok

A Hadoop-szolgáltatások magas rendelkezésre állásának biztosítása érdekében a HDInsight két fő csomópontot biztosít. Mindkét fő csomópont aktív, és egyidejűleg fut a HDInsight-fürtön belül. Egyes szolgáltatások, például az Apache HDFS vagy a Apache Hadoop FONALak, csak az egyik főcsomóponton aktívak az adott időpontban. Más szolgáltatások, például a HiveServer2 vagy a kaptár Metaadattár egyszerre aktívak mindkét fő csomóponton.

A fürt különböző csomópontjaihoz tartozó állomásnevek beszerzéséhez használja a [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Ne társítsa a numerikus értéket azzal, hogy a csomópont elsődleges vagy másodlagos. A numerikus érték csak az egyes csomópontok egyedi nevének megadását mutatja be.

### <a name="nimbus-nodes"></a>Nimbus-csomópontok

A Nimbus-csomópontok Apache Storm fürtökkel érhetők el. A Nimbus-csomópontok hasonló funkciókat biztosítanak a Hadoop-JobTracker a munkavégző csomópontok feldolgozásának elosztásával és figyelésével. A HDInsight két Nimbus-csomópontot biztosít a Storm-fürtökhöz

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper-csomópontok

A [ZooKeeper](https://zookeeper.apache.org/) -csomópontok a fő szolgáltatások vezető megválasztására szolgálnak a fő csomópontokon. Azt is használják, hogy biztosítják, hogy a szolgáltatások, az adatok (feldolgozó) csomópontjai és az átjárók tudják, melyik fő szolgáltatás aktív a főkiszolgálón. Alapértelmezés szerint a HDInsight három ZooKeeper-csomópontot biztosít.

### <a name="worker-nodes"></a>Munkavégző csomópontok

A feldolgozó csomópontok végrehajtják a tényleges adatelemzést, ha a feladatot elküldik a fürtnek. Ha egy feldolgozó csomópont meghibásodik, az általa végrehajtott feladat egy másik munkavégző csomópontnak lesz elküldve. Alapértelmezés szerint a HDInsight négy munkavégző csomópontot hoz létre. Ezt a számot módosíthatja úgy, hogy az igényeinek megfelelően és a fürt létrehozása után is megfeleljen.

### <a name="edge-node"></a>peremhálózati csomópont

Az Edge-csomópontok nem vesznek aktívan részt a fürtön belüli adatelemzésben. A fejlesztők és az adatszakértők a Hadoop használatakor használják. A peremhálózati csomópont ugyanabban az Azure-Virtual Networkban él, mint a fürt többi csomópontja, és közvetlenül hozzáférhet az összes többi csomóponthoz is. A peremhálózati csomópont a kritikus Hadoop-szolgáltatások és az elemzési feladatok erőforrásainak elhagyása nélkül is használható.

A HDInsight-ben jelenleg a ML-szolgáltatások az egyetlen olyan fürt típusa, amely alapértelmezés szerint egy peremhálózati csomópontot biztosít. A HDInsight ML-szolgáltatásai esetében a peremhálózati csomópont a csomóponton helyileg teszteli az R-kódot, mielőtt elküldi azt a fürtbe elosztott feldolgozás céljából.

Az Edge-csomópontok más típusú fürtökkel való használatáról további információért lásd: [Edge-csomópontok használata a HDInsight](hdinsight-apps-use-edge-node.md) dokumentumban.

## <a name="accessing-the-nodes"></a>A csomópontok elérése

A fürt interneten keresztüli elérését nyilvános átjárón keresztül biztosítjuk. A hozzáférés csak a fő csomópontokhoz való csatlakozásra korlátozódik, és ha van ilyen, a peremhálózati csomópont. A fő csomópontokon futó szolgáltatásokhoz való hozzáférést nem érinti több fő csomópont. A nyilvános átjáró átirányítja a kérelmeket a kért szolgáltatást üzemeltető fő csomópontra. Ha például az Apache Ambari jelenleg a másodlagos főcsomóponton fut, az átjáró a Ambari bejövő kéréseit a csomópontra irányítja.

A nyilvános átjárón keresztüli hozzáférés a 443 (HTTPS), a 22 és a 23 portra korlátozódik.

|Port |Description |
|---|---|
|443|A fő csomópontokon üzemeltetett Ambari és egyéb webes felhasználói felület vagy REST API-k elérésére szolgál.|
|22|Az elsődleges fej csomópontjának vagy peremhálózati csomópontjának az SSH-val való elérésére szolgál.|
|23|A másodlagos fej csomópontjának SSH-val való elérésére szolgál. Például csatlakozik a `ssh username@mycluster-ssh.azurehdinsight.net` **mycluster**nevű fürt elsődleges fő csomópontjára.|

Az SSH használatával kapcsolatos további információkért tekintse meg az [SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) -mel című dokumentumot.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Belső teljesen minősített tartománynevek (FQDN)

A HDInsight-fürtök csomópontjai belső IP-címmel és teljes tartománynévvel rendelkeznek, amely csak a fürtből érhető el. Ha a belső FQDN vagy IP-cím használatával fér hozzá a fürtben lévő szolgáltatásokhoz, a Ambari használatával ellenőrizze a szolgáltatáshoz való hozzáféréskor használandó IP-címet vagy teljes tartománynevet.

Az Apache Oozie szolgáltatás például csak egy fő csomóponton futhat, és az `oozie` SSH-munkamenet parancsának használatával a szolgáltatás URL-címét kell használnia. Ez az URL-cím a Ambari-ből kérhető le a következő parancs használatával:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

A parancs a következőhöz hasonló értéket ad vissza, amely tartalmazza a parancshoz használandó belső URL-címet `oozie` :

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

További információ a Ambari REST API használatáról: a [HDInsight figyelése és kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Más csomópont-típusok elérése

A következő módszerekkel csatlakozhat olyan csomópontokhoz, amelyek nem érhetők el közvetlenül az interneten keresztül:

|Metódus |Description |
|---|---|
|SSH|Miután az SSH-val csatlakozott egy Head csomóponthoz, a fő csomóponton található SSH használatával csatlakozhat a fürt más csomópontjaihoz. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).|
|SSH-alagút|Ha az egyik olyan csomóponton üzemeltetett webszolgáltatáshoz kell hozzáférni, amely nem érhető el az interneten, SSH-alagutat kell használnia. További információ: [SSH-alagút használata HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) -dokumentummal.|
|Azure Virtual Network|Ha a HDInsight-fürt egy Azure-Virtual Network része, akkor az ugyanazon Virtual Network található összes erőforrás közvetlenül hozzáférhet a fürtben lévő összes csomóponthoz. További információkért lásd a [virtuális hálózat megtervezése a HDInsight](hdinsight-plan-virtual-network-deployment.md) dokumentumhoz című témakört.|

## <a name="how-to-check-on-a-service-status"></a>A szolgáltatás állapotának beadása

A fő csomópontokon futó szolgáltatások állapotának megtekintéséhez használja a Ambari webes felhasználói felületét vagy a Ambari REST API.

### <a name="ambari-web-ui"></a>Ambari webes felhasználói felület

A Ambari webes felhasználói felülete a következő címen tekinthető meg: `https://CLUSTERNAME.azurehdinsight.net` . Cserélje le a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg a fürt HTTP-felhasználói hitelesítő adatait. Az alapértelmezett HTTP-Felhasználónév a **rendszergazda** , a jelszó pedig a fürt létrehozásakor megadott jelszó.

Amikor megérkezik a Ambari lapra, a telepített szolgáltatások megjelennek a lap bal oldalán.

![Apache Ambari telepített szolgáltatások](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Az állapot jelzéséhez számos ikon jelenhet meg a szolgáltatás mellett. A szolgáltatással kapcsolatos riasztások az oldal tetején található **riasztások** hivatkozás használatával tekinthetők meg.  A Ambari számos előre meghatározott riasztást biztosít.

A következő riasztások segítik a fürt rendelkezésre állásának figyelését:

| Riasztás neve                               | Description                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Metrikus figyelő állapota                    | Ez a riasztás a mérőszámok figyelő folyamatának állapotát jelzi a figyelő állapotának parancsfájlja alapján.                                                                                   |
| Ambari-ügynök szívverése                   | Ez a riasztás akkor aktiválódik, ha a kiszolgáló elvesztette a kapcsolatot egy ügynökkel.                                                                                                                        |
| ZooKeeper-kiszolgáló folyamata                 | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a ZooKeeper-kiszolgáló folyamata nem határozható meg a hálózaton való működéshez és figyeléshez.                                                               |
| IOCache metaadat-kiszolgálójának állapota           | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a IOCache metaadat-kiszolgáló nem állapítható meg, és nem válaszol az ügyfelek kéréseire                                                            |
| JournalNode webes felhasználói felület                       | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a JournalNode webes felhasználói felülete nem érhető el.                                                                                                                 |
| Spark2 takarékosság-kiszolgáló                     | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a Spark2 takarékossági kiszolgáló nem határozható meg.                                                                                                |
| Előzmények kiszolgálói folyamat                   | Ez a gazdagép szintű riasztás akkor aktiválódik, ha az előzmények kiszolgálói folyamat nem hozható létre és nem figyelhető a hálózaton.                                                                |
| Előzmények kiszolgáló webes felhasználói felülete                    | Ez a gazdagép szintű riasztás akkor aktiválódik, ha az előzményeket kiszolgáló webes FELÜLETe nem érhető el.                                                                                                              |
| `ResourceManager`Webes felhasználói felület                   | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a `ResourceManager` webes felhasználói felület nem érhető el.                                                                                                             |
| NodeManager állapotának összegzése               | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha nem kifogástalan állapotú Csomópontkezelők van                                                                                                                    |
| Alkalmazás idővonalának webes felhasználói felülete                      | Ez a gazdagép szintű riasztás akkor aktiválódik, ha az alkalmazás idővonal-kiszolgáló webes felhasználói felülete nem érhető el.                                                                                                         |
| DataNode állapotának összegzése                  | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha nem kifogástalan állapotú Adatcsomópontok van                                                                                                                       |
| NameNode webes felhasználói felület                          | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a NameNode webes felhasználói felülete nem érhető el.                                                                                                                    |
| ZooKeeper feladatátvételi vezérlő folyamata    | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a ZooKeeper feladatátvételi vezérlő folyamatát nem lehet megerősíteni a hálózatban való működéshez és figyeléshez.                                                   |
| Oozie-kiszolgáló webes felhasználói felülete                      | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a Oozie-kiszolgáló webes FELÜLETe nem érhető el.                                                                                                                |
| Oozie-kiszolgáló állapota                      | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a Oozie-kiszolgáló nem határozható meg, és nem válaszol az ügyfelek kéréseire.                                                                      |
| Struktúra Metaadattár folyamata                   | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a struktúra Metaadattár folyamata nem határozható meg a hálózaton való működéshez és figyeléshez.                                                                 |
| HiveServer2 folyamat                      | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a HiveServer nem lehet meghatározni, és nem válaszol az ügyfelek kéréseire.                                                                        |
| Webhcaten-kiszolgáló állapota                    | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a `templeton` kiszolgáló állapota nem kifogástalan.                                                                                                            |
| Rendelkezésre álló ZooKeeper-kiszolgálók százalékos aránya      | Ez a riasztás akkor aktiválódik, ha a fürtön lefelé ZooKeeper kiszolgálók száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a ZooKeeper folyamat-ellenőrzésének eredményeit.     |
| Spark2 Livy-kiszolgáló                       | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a Livy2-kiszolgáló nem határozható meg.                                                                                                        |
| Spark2-előzmények kiszolgálója                    | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a Spark2-előzmények kiszolgálója nem határozható meg.                                                                                               |
| Metrikák gyűjtői folyamata                | Ez a riasztás akkor aktiválódik, ha a metrikák gyűjtőjét nem lehet megerősíteni, hogy a beállított porton a küszöbértéket meghaladó másodpercek száma legyen.                                 |
| Metrikák gyűjtője – HBase Master folyamat | Ez a riasztás akkor aktiválódik, ha a metrikák gyűjtője HBase fő folyamatai nem állíthatók be és nem figyelhető a hálózatban a beállított kritikus küszöbérték esetében (másodpercben megadva). |
| Rendelkezésre álló százalékos metrikai figyelők       | Ez a riasztás akkor aktiválódik, ha a metrikák figyelési folyamatainak százalékos aránya nem működik, és a beállított figyelmeztetési és kritikus küszöbértékeket a hálózat figyeli.                             |
| Rendelkezésre álló Csomópontkezelők százalékos aránya           | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Csomópontkezelők száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a NodeManager folyamat-ellenőrzésének eredményeit.        |
| NodeManager állapota                       | Ez a gazdagép szintű riasztás ellenőrzi a NodeManager összetevőben elérhető Node Health tulajdonságot.                                                                                              |
| NodeManager webes felhasználói felület                       | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a NodeManager webes felhasználói felülete nem érhető el.                                                                                                                 |
| NameNode magas rendelkezésre állási állapota        | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha az aktív NameNode vagy a készenléti NameNode nem fut.                                                                                     |
| DataNode folyamat                         | Ez a gazdagép szintű riasztás akkor aktiválódik, ha az egyes DataNode folyamatok nem hozhatók létre és nem lesznek figyelve a hálózaton.                                                         |
| DataNode webes felhasználói felület                          | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a DataNode webes felhasználói felülete nem érhető el.                                                                                                                    |
| Rendelkezésre álló Naplócsomópontok százalékos aránya           | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Naplócsomópontok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a JournalNode folyamat-ellenőrzésének eredményeit.        |
| Rendelkezésre álló Adatcsomópontok százalékos aránya              | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Adatcsomópontok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a DataNode folyamat-ellenőrzésének eredményeit.              |
| Zeppelin-kiszolgáló állapota                   | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a Zeppelin-kiszolgáló nem határozható meg, és nem válaszol az ügyfelek kéréseire.                                                                   |
| HiveServer2 interaktív folyamat          | Ez a gazdagép szintű riasztás akkor aktiválódik, ha a HiveServerInteractive nem lehet meghatározni, és nem válaszol az ügyfelek kéréseire.                                                             |
| LLAP-alkalmazás                         | Ez a riasztás akkor aktiválódik, ha a LLAP alkalmazás nem határozható meg, és nem válaszol a kérelmekre.                                                                                    |

Az egyes szolgáltatások lehetőség kiválasztásával további információkat tekinthet meg.

Míg a szolgáltatás oldala információt nyújt az egyes szolgáltatások állapotáról és konfigurációjáról, nem nyújt olyan információt, amelyen a szolgáltatás fut. Ezen információk megtekintéséhez használja a **gazdagépek** hivatkozást az oldal tetején. Ezen a lapon láthatók a fürtön belüli gazdagépek, beleértve a fő csomópontokat is.

![Apache Ambari átjárócsomóponthoz-gazdagépek listája](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Az egyik fő csomóponthoz tartozó hivatkozás kiválasztásával megjeleníti az adott csomóponton futó szolgáltatásokat és összetevőket.

![Apache Ambari-összetevő állapota](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

A Ambari használatával kapcsolatos további információkért lásd: [HDInsight figyelése és kezelése az Apache Ambari webes felhasználói felületén](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

A Ambari REST API elérhető az interneten keresztül. A HDInsight nyilvános átjáró átirányítja az útválasztási kérelmeket a REST API jelenleg üzemeltető fő csomópontra.

A következő paranccsal ellenőrizhető a szolgáltatás állapota a Ambari REST API használatával:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Cserélje le a **jelszót** a http-felhasználó (admin) fiók jelszavára.
* Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
* Cserélje le a **szolgáltatásnév** nevet annak a szolgáltatásnak a nevére, amelynek az állapotát ellenőriznie szeretné.

Ha például a **HDFS** szolgáltatás állapotát egy **mycluster**nevű fürtön szeretné megtekinteni, **a jelszó jelszavával, használja**a következő parancsot:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

A válasz a következő JSON-hoz hasonló:

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

Az URL-cím azt jelzi, hogy a szolgáltatás jelenleg egy **mycluster. wutj3h4ic1zejluqhxzvckxq0g**nevű Head csomóponton fut.

Az állapot azt jelzi, hogy a szolgáltatás jelenleg fut vagy **elindult**.

Ha nem tudja, hogy mely szolgáltatások vannak telepítve a fürtön, a következő paranccsal kérhet le egy listát:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

További információ a Ambari REST API használatáról: a [HDInsight figyelése és kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Szolgáltatás-összetevők

A szolgáltatások olyan összetevőket tartalmazhatnak, amelyekben egyenként szeretné megtekinteni az állapotot. Például a HDFS tartalmazza a NameNode összetevőt. Egy összetevő információinak megtekintéséhez a parancs a következő lesz:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Ha nem tudja, milyen összetevőket biztosít a szolgáltatás, a következő paranccsal kérhet le egy listát:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>A naplófájlok elérése a fő csomópontokon

### <a name="ssh"></a>SSH

Amikor SSH-n keresztül csatlakozik egy fő csomóponthoz, a naplófájlok a **/var/log**alatt találhatók. Például a **/var/log/Hadoop-yarn/yarn** a fonalat tartalmazó naplókat tartalmaz.

Minden egyes főcsomópont egyedi naplóbejegyzést tartalmazhat, ezért mindkét oldalon ellenőriznie kell a naplókat.

### <a name="sftp"></a>SFTP

Az SSH File Transfer Protocol vagy a Secure File Transfer Protocol (SFTP) használatával is csatlakozhat a fő csomóponthoz, és közvetlenül is letöltheti a naplófájlokat.

Az SSH-ügyfelekhez hasonlóan a fürthöz való csatlakozáskor meg kell adnia a fürt SSH-felhasználói fiókjának nevét és SSH-címeit. Például: `sftp username@mycluster-ssh.azurehdinsight.net`. Ha a rendszer kéri, adja meg a fiók jelszavát, vagy adjon meg egy nyilvános kulcsot a `-i` paraméter használatával.

A csatlakozás után megjelenik egy `sftp>` üzenet. Ebből a kérésből megváltoztathatja a címtárakat, a feltöltési és a letöltési fájlokat. Az alábbi parancsok például megváltoztatják a címtárakat a **/var/log/Hadoop/hdfs** könyvtárba, majd az összes fájlt letöltik a címtárból.

```bash
cd /var/log/hadoop/hdfs
get *
```

Az elérhető parancsok listáját `help` a `sftp>` parancssorba írja be.

> [!NOTE]  
> Vannak olyan grafikus felületek is, amelyek lehetővé teszik a fájlrendszer megjelenítését az SFTP használatával való csatlakozáskor. Például a [MobaXTerm](https://mobaxterm.mobatek.net/) lehetővé teszi a fájlrendszer tallózását a Windows Intézőhöz hasonló felület használatával.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Ha a naplófájlokat a Ambari használatával szeretné elérni, SSH-alagutat kell használnia. Az egyes szolgáltatások webes felülete nem nyilvánosan elérhető az interneten. Az SSH-alagutak használatával kapcsolatos információkért tekintse meg az [SSH-alagút használata](hdinsight-linux-ambari-ssh-tunnel.md) című dokumentumot.

A Ambari webes felhasználói felületén válassza ki azt a szolgáltatást, amelyre vonatkozóan meg szeretné tekinteni a naplókat (például a FONALat). Ezután a **gyors hivatkozások** használatával kiválaszthatja, hogy melyik főcsomóponton szeretné megtekinteni a naplókat.

![Gyors hivatkozások használata a naplók megtekintéséhez](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>A csomópont méretének konfigurálása

A csomópontok mérete csak a fürt létrehozásakor választható ki. A HDInsight elérhető különböző virtuálisgép-méretek listáját a [HDInsight díjszabása lapon](https://azure.microsoft.com/pricing/details/hdinsight/)találja.

Fürt létrehozásakor megadhatja a csomópontok méretét. A következő információk útmutatást nyújtanak a méret megadásához az [Azure Portal](https://portal.azure.com/), [Azure PowerShell modul](/powershell/azureps-cmdlets-docs)az és az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával:

* **Azure Portal**: fürt létrehozásakor beállíthatja a fürt által használt csomópontok méretét:

    ![A fürt létrehozási varázslójának képe a csomópontok méretének kiválasztásával](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: a parancs használatakor beállíthatja a [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) fej, a feldolgozó és a ZooKeeper-csomópontok méretét a `--headnode-size` , `--workernode-size` , és paraméterek használatával `--zookeepernode-size` .

* **Azure PowerShell**: a [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsmag használatakor beállíthatja a fej, a feldolgozó és a ZooKeeper csomópont méretét a `-HeadNodeSize` , `-WorkerNodeSize` , és `-ZookeeperNodeSize` paraméterek használatával.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a cikkben tárgyalt elemekről, tekintse meg a következőt:

* [Apache Ambari – REST-dokumentáció](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Az Azure CLI telepítése és konfigurálása](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure PowerShell modul telepítése és konfigurálása az](/powershell/azure/overview)
* [HDInsight-kezelés az Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)
* [Linux-alapú HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md)
