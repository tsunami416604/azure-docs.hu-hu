---
title: "Magas rendelkezésre állás a Hadoop - Azure HDInsight |} Microsoft Docs"
description: "Ismerje meg, hogyan HDInsight-fürtök javítása a megbízhatóság és rendelkezésre állás további központi csomópontra. Ismerje meg, milyen hatással van ez Hadoop szolgáltatások például az Ambari és a Hive, és csatlakozni külön-külön minden átjárócsomópont SSH használatával."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
keywords: "hadoop magas rendelkezésre állás"
ms.assetid: 99c9f59c-cf6b-4529-99d1-bf060435e8d4
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/08/2018
ms.author: larryfr
ms.openlocfilehash: 0822502bcd3e2b0d20f718a158d6db489a650bde
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>A HDInsight-beli Hadoop-fürtök rendelkezésre állása és megbízhatósága

A HDInsight-fürtök két központi csomópont a rendelkezésre állás és a Hadoop-szolgáltatás és a futó feladatok megbízhatóságának növelése érdekében adja meg.

Hadoop éri el a magas rendelkezésre állást és megbízhatóságot által egy fürt több csomópontja replikálása szolgáltatásaikat és adataikat. Standard disztribúciók Hadoop azonban általában csak egy átjárócsomóponttal rendelkeznek. Bármely, a egy átjárócsomóponttal kimaradás a fürt működésének leállását okozhatja. HDInsight Hadoop által rendelkezésre állásának és megbízhatóságának növelése érdekében két headnodes biztosít.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="availability-and-reliability-of-nodes"></a>Rendelkezésre állásának és megbízhatóságának csomópontok

HDInsight-fürtök csomópontjai Azure virtuális gépek segítségével lehet létrehozni. Az alábbi szakaszok ismertetik az egyes csomóponttípusok HDInsight használt. 

> [!NOTE]
> A fürt típusa nem minden csomópont-típusok használhatók. Például egy Hadoop-fürt típusa nem rendelkezik egyetlen Nimbus csomópontot. A HDInsight-fürttípusok által használt csomópontok további információkért lásd: a fürt típusok szakasza a [hdinsight létrehozása Linux-alapú Hadoop-fürtök](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentum.

### <a name="head-nodes"></a>HEAD csomópontok

Ahhoz, hogy a magas rendelkezésre állású Hadoop szolgáltatásokat, a HDInsight két átjárócsomópontokkal biztosít. Mindkét átjárócsomópontokkal egyidejűleg aktív és a HDInsight-fürtön belül futnak. Egyes szolgáltatások, például a HDFS vagy YARN, egy adott időpontban csak "active", egy központi csomóponton. Más szolgáltatásokon, például a hiveserver2-n vagy a Hive Metaadattárhoz aktívak mindkét központi csomóponton egyidejűleg.

HEAD csomópontok (és más csomópontok a Hdinsightban) rendelkezik egy numerikus érték, a csomópont állomásnév részeként. Például `hn0-CLUSTERNAME` vagy `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> Ne társítson a numerikus értéket-e a csomópont a elsődleges vagy másodlagos. A numerikus értéke csak elérhető adjon egyedi nevet az egyes csomópontok.

### <a name="nimbus-nodes"></a>Nimbus-csomópontok

Nimbus csomópont alatt futó Storm-fürtökkel érhetők el. A Nimbus csomópontot ellátni hasonló a Hadoop JobTracker terjesztése és feldolgozási munkavégző csomópontokhoz átívelő figyelésére is alkalmas. A HDInsight két Nimbus csomóponttal rendelkezik a Storm-fürtök

### <a name="zookeeper-nodes"></a>Zookeeper csomópontok

[ZooKeeper](http://zookeeper.apache.org/) csomópontok használt fő szolgáltatást az átjárócsomópontokkal vezető választás. Akkor is használhatók biztosítását, hogy szolgáltatások, a adatcsomópontokat (munkavégző) és az átjárók tudja, hogy mely átjárócsomópont szolgáltatás főkulcsának aktív. Alapértelmezés szerint a HDInsight nyújt három ZooKeeper csomópontok.

### <a name="worker-nodes"></a>Munkavégző csomópontokhoz

Munkavégző csomópontokhoz hajtsa végre a tényleges adatok elemzéséhez, amikor egy feladatot a fürt. Ha egy feldolgozó csomópont leáll, a feladat azt végző elküldve a worker egy másik csomópontra. Alapértelmezés szerint a HDInsight létrehoz négy munkavégző csomópontokhoz. Ez a szám a igényeinek alatt és a fürt létrehozása után módosíthatja.

### <a name="edge-node"></a>Határcsomópont

Egy élcsomópontot aktívan nem vesz részt a fürtön belül adatelemzés. Amikor olyan Hadoop fejlesztők vagy adatszakértőkön szolgál. Élcsomópont él, mint a fürt többi csomópontjának azonos Azure virtuális hálózatban, és közvetlenül hozzáférhet az összes csomóponton. Élcsomópont kritikus Hadoop-szolgáltatás vagy a feladatok távol erőforrások anélkül is használható.

Az R Server on HDInsight jelenleg az egyetlen fürt típus, amely alapértelmezés szerint egy élcsomópontot biztosít. Az R Server on HDInsight, az élcsomópont használatos teszt R-kód helyileg a csomóponton a fürt elosztott feldolgozásra való továbbítás előtt.

Egy élcsomópontot fürt típusa nem R Server használatáról információkért lásd: a [peremhálózati csomópontok használata a Hdinsightban](hdinsight-apps-use-edge-node.md) dokumentum.

## <a name="accessing-the-nodes"></a>A csomópontok elérése

A fürt az interneten keresztül való hozzáférés a nyilvános átjárón keresztül valósul meg. Hozzáférés korlátozódik az átjárócsomópontokkal csatlakozik, és (ha van ilyen) a peremhálózati csomópont. A head csomópontokon futó szolgáltatásokhoz való hozzáférés nem azzal, hogy több átjárócsomópontokkal történik. A nyilvános átjáró irányítja a kérelmeket az átjárócsomóponthoz, amelyen a kért szolgáltatást. Például Ambari kiszolgálóvá a másodlagos átjárócsomópont, ha az átjáró irányítja a bejövő kéréseket az Ambari ahhoz a csomóponthoz.

Hozzáférés a nyilvános átjárón keresztül port 443-as (HTTPS), a 22-es és 23 korlátozódik.

* Port __443-as__ Ambari és egyéb webes felhasználói felületén vagy a head csomópontokon futó REST API-k elérésére szolgál.

* Port __22__ elérni az elsődleges átjárócsomópont vagy élcsomópont az SSH használatával.

* Port __23__ az SSH a másodlagos átjárócsomópont eléréséhez használt. Például `ssh username@mycluster-ssh.azurehdinsight.net` nevű fürt elsődleges átjárócsomópontjához csatlakozik **sajátfürt**.

További információ az SSH használatával, tekintse meg a [az SSH a Hdinsighttal](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentum.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Belső teljes tartománynevek (FQDN)

HDInsight-fürtök csomópontjai rendelkeznek, egy belső IP-cím és a teljes tartománynév, csak elérhető a fürtből. A belső FQDN vagy IP-címet használ a fürt szolgáltatásai elérésekor a Ambari ellenőrzése a a szolgáltatás eléréséhez használt IP vagy FQDN Formátumban kell használnia.

Például az Oozie-szolgáltatás csak futtathatja egy átjárócsomópont, és használja a `oozie` SSH-munkamenetet a parancshoz szükséges, a szolgáltatás URL-CÍMÉT. Az URL-cím a következő paranccsal olvasható be a Ambari:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Ez a parancs értéket ad vissza a következő parancsot, amely tartalmazza a belső URL-cím használata hasonlít a `oozie` parancs:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Az Ambari REST API használatával további információkért lásd: [figyelése és az Ambari REST API használatával kezelése HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Egyéb elérése

Csatlakozhat a csomópontot, amely nem érhető el közvetlenül az interneten keresztül az alábbi módszerekkel:

* **SSH**: Miután csatlakozott egy átjárócsomóponttal SSH használatával, majd használhatja SSH az átjárócsomópont kapcsolódni a fürt többi tagján. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

* **SSH-alagút**: egy webszolgáltatás-bővítmény egy olyan csomópontot, amely nem érhető el az internet eléréséhez szükséges, ha az SSH-alagút kell használnia. További információkért lásd: a [használhat SSH-alagutat a hdinsight eszközzel](hdinsight-linux-ambari-ssh-tunnel.md) dokumentum.

* **Azure-beli virtuális hálózat**: a HDInsight-fürt része egy Azure virtuális hálózatra, ha minden erőforrást azonos virtuális hálózaton közvetlenül érhetik el a fürt összes csomópontján. További információkért lásd: a [kiterjesztése HDInsight az Azure Virtual Network a](hdinsight-extend-hadoop-virtual-network.md) dokumentum.

## <a name="how-to-check-on-a-service-status"></a>Az a szolgáltatás állapotának ellenőrzése

Az átjárócsomópontokkal futó szolgáltatások állapotának ellenőrzéséhez használja az Ambari webes felhasználói felületén vagy az Ambari REST API-t.

### <a name="ambari-web-ui"></a>Ambari webes felhasználói felület

Az Ambari webes felhasználói felületén, https://CLUSTERNAME.azurehdinsight.net megtekinthető. Cserélje le a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg a HTTP-felhasználó hitelesítő adatait a fürt számára. Az alapértelmezett HTTP felhasználónév **admin** , a jelszó pedig a fürt létrehozásakor megadott jelszó.

Amikor megérkezik az Ambari oldalon, a telepített szolgáltatások szerepelnek a lap bal oldalon található.

![Telepített szolgáltatások](./media/hdinsight-high-availability-linux/services.png)

A szolgáltatás állapotának mellett megjelenő ikonok több van. Az a szolgáltatással kapcsolatos riasztások használatával tekinthetők a **riasztások** az oldal tetején. További információk megjelenítéséhez a minden egyes szolgáltatás választhatja ki.

A szolgáltatás lapján információt nyújt az egyes szolgáltatások konfigurációja és állapota, amíg azt nem szolgál információval melyik központi csomóponton a szolgáltatás fut a. Ez az információ megtekintéséhez használja a **állomások** az oldal tetején. Ez a lap megjeleníti a fürtben, beleértve az átjárócsomópontokkal állomások.

![állomások listájához](./media/hdinsight-high-availability-linux/hosts.png)

A hivatkozás egyik az átjárócsomópontokkal látható értesítések valamelyikének kiválasztásakor a szolgáltatások és a leállt csomóponton összetevőket.

![Összetevő-állapot](./media/hdinsight-high-availability-linux/nodeservices.png)

További információ az Ambari használatával, lásd: [figyelése és kezelése az Ambari webes felhasználói felület használata a HDInsight](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API-n

Az Ambari REST API-t az interneten keresztül érhető el. A HDInsight nyilvános átjáró az átjárócsomóponthoz, amely a REST API-t tartalmazó útválasztási kérelmeket kezeli.

A következő paranccsal ellenőrizze az Ambari REST API-n keresztül egy szolgáltatás állapotát:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Cserélje le **jelszó** az a HTTP (rendszergazda) felhasználói fiók jelszavát.
* Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
* Cserélje le **szolgáltatásnév** tekintse meg a kívánt szolgáltatás nevét.

Például tekintse meg a **HDFS** szolgáltatás nevű fürt **sajátfürt**, a jelszó **jelszó**, akkor használja a következő parancsot:

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

Az URL-cím közli velünk, hogy a szolgáltatás jelenleg fut egy átjárócsomópont nevű **hn0-CLUSTERNAME**.

Az állapot közli velünk, hogy a szolgáltatás jelenleg fut, vagy **elindítva**.

Ha nem tudja, milyen szolgáltatások telepítve vannak a fürtön, a következő parancs használatával kérdezheti le:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Az Ambari REST API használatával további információkért lásd: [figyelése és az Ambari REST API használatával kezelése HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Szolgáltatás-összetevők

Szolgáltatások tartalmazhatnak, tekintse meg a külön-külön kívánt összetevőket. Például a HDFS a NameNode összetevőt tartalmaz. A parancs információk megtekintéséhez kattintson egy összetevő a következő lesz:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Ha nem tudja, milyen összetevők szolgáltatás által biztosított, a következő parancs használatával kérdezheti le:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Hogyan érhetők el az átjárócsomópontokkal naplófájlok

### <a name="ssh"></a>SSH

Ha egy SSH-n keresztül. átjárócsomópontjához csatlakozik, naplófájlok alatt található **/var/log**. Például **/var/log/hadoop-yarn/yarn** YARN naplók tartalmazzák.

Minden átjárócsomópont egyedi naplóbejegyzések, van így ellenőrizni kell mind a naplókat.

### <a name="sftp"></a>SFTP

Az SSH File Transfer Protocol vagy biztonságos fájl Transfer Protocol (SFTP) átjárócsomópontjához csatlakozik, és töltse le a naplófájlok közvetlenül is.

Hasonló egy SSH-ügyfélprogrammal, amikor a fürthöz csatlakozik biztosítania kell az SSH-felhasználói fiók nevét és az SSH-cím, a fürt. Például: `sftp username@mycluster-ssh.azurehdinsight.net`. Adja meg a fiók, amikor a rendszer kéri a jelszót, vagy adja meg a nyilvános kulcs használatával a `-i` paraméter.

Miután csatlakozott, lehetősége lesz a `sftp>` kérdés. A parancssorból módosíthatja, könyvtárak, fájlok feltöltését és letöltését. Például a következő parancsok lépjen a **/var/log/hadoop/hdfs** directory és az összes olyan fájl a könyvtárban, majd letöltése.

    cd /var/log/hadoop/hdfs
    get *

Elérhető parancsok listáját, írja be a `help` , a `sftp>` kérdés.

> [!NOTE]
> Van grafikus felületek, amelyek lehetővé teszik a fájlrendszer esetén SFTP használatával jelenítheti meg. Például [MobaXTerm](http://mobaxterm.mobatek.net/) lehetővé teszi, hogy keresse meg a fájlrendszer hasonló a Windows Explorer-felületet segítségével.

### <a name="ambari"></a>Ambari

> [!NOTE]
> Ambari naplófájlokat eléréséhez az SSH-alagút kell használnia. A webes felületek, az egyes szolgáltatások nem érhetők el nyilvánosan az interneten. Az SSH-alagúton keresztül információkért lásd: a [használata SSH Tunneling](hdinsight-linux-ambari-ssh-tunnel.md) dokumentum.

Az Ambari webes felhasználói felületén jelölje ki a kívánt naplók megtekintése (például YARN) szolgáltatás. Ezután **Gyorshivatkozások** a naplók megtekintéséhez melyik központi csomópont kiválasztásához.

![Gyorshivatkozások használ a naplók megtekintéséhez](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>A csomópont méretének beállítása

A csomópont mérete csak akkor jelölhető ki, fürt létrehozása során. A HDInsight a különböző elérhető Virtuálisgép-méretek listáját megtalálhatja a [árképzést ismertető oldalra HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

A fürt létrehozásakor megadhatja a csomópontok méretét. Az alábbi információk megadása a méret használatával nyújt útmutatást a [Azure-portálon][preview-portal], [Azure PowerShell][azure-powershell], és a [Azure CLI][azure-cli]:

* **Azure-portálon**: hoz létre egy fürtöt, beállíthatja a csomópontok a fürt által használt mérete:

    ![A méret kiválasztása a fürt létrehozása varázsló képe](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Az Azure CLI**: használata esetén a `azure hdinsight cluster create` parancs használatával beállíthatja a head munkavégző és ZooKeeper csomópontok méretét a `--headNodeSize`, `--workerNodeSize`, és `--zookeeperNodeSize` paraméterek.

* **Az Azure PowerShell**: használata esetén a `New-AzureRmHDInsightCluster` parancsmag, beállíthatja a head munkavégző és ZooKeeper csomópontok méretének használatával a `-HeadNodeVMSize`, `-WorkerNodeSize`, és `-ZookeeperNodeSize` paraméterek.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozások segítségével további ebben a dokumentumban említett szempontot.

* [Ambari REST-referencia](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Telepítse és konfigurálja az Azure parancssori felület](../cli-install-nodejs.md)
* [Telepítse és konfigurálja az Azure PowerShellt](/powershell/azure/overview)
* [HDInsight a Ambari kezelése](hdinsight-hadoop-manage-ambari.md)
* [Linux-alapú HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
