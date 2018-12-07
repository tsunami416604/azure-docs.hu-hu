---
title: A helyszíni Apache Hadoop-fürtök áttelepítése Azure HDInsight - infrastruktúra ajánlott eljárások
description: Ismerje meg az infrastruktúra áttelepítését a helyszíni Hadoop-fürtöket az Azure HDInsight ajánlott eljárásai.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 6a1641a76d43cdbac6253e00ea35f70325870853
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993383"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>A helyszíni Apache Hadoop-fürtök áttelepítése Azure HDInsight - infrastruktúra ajánlott eljárások

Ez a cikk az Azure HDInsight-fürtök-infrastruktúra kezelésére alkalmas javaslatok biztosít. Ez azt egy olyan sorozat részét, amely ajánlott eljárásokat, amelyek segítik az Azure HDInsight áttelepítése a helyszíni Apache Hadoop-rendszerekhez biztosít.

## <a name="plan-for-hdinsight-cluster-capacity"></a>HDInsight-fürt kapacitásának tervezése

A legfontosabb választási lehetőségek, hogy a HDInsight-fürtök kapacitástervezése a következők:

- **Válassza ki a régiót** – az Azure-régió határozza meg, ahol a fürt fizikailag van kiépítve. Az olvasási és írási késés minimalizálása érdekében a fürt és az adatokat ugyanabban a régióban kell lennie.
- **Válassza ki a tároló helye és mérete** – az alapértelmezett tároló és a fürt ugyanabban a régióban kell lennie. 48 csomópontos fürt esetén azt javasoljuk, hogy a tárfiókok 4 – 8. Már elegendő tárterület teljes lehet, bár egyes tárfiók további hálózati sávszélességet biztosít a számítási csomópontok. Ha több tárfiók, véletlenszerű nevet minden olyan tárfiókhoz előtag nélkül használhat. Véletlenszerű elnevezési célját összes fiók csökkenthető a tárolási szűk keresztmetszetek (szabályozás) vagy a közös módú hibák esélyét. A jobb teljesítmény érdekében használja a storage-fiókonként csak egy tároló.
- **Válassza ki a virtuális gép méretét és típusát (mostantól támogatja a G-sorozat)** – minden egyes fürttípus csomóponttípusok készletével rendelkezik, és mindegyik csomóponttípus van konkrét beállításokat a virtuális gép méretét és típusát. A virtuális gép méretét és típusát határozza meg a Processzor feldolgozási, a RAM-méretétől és a hálózati késés. Egy szimulált számítási feladat az optimális Virtuálisgép-méretet, és írja be az egyes csomóponttípusok meghatározására használható.
- **Válassza ki a munkavégző csomópontok számát** – a munkavégző csomópontok kezdeti száma a szimulált számítási feladatok használatával kell meghatározni. A fürt később méretezhetők, ha betöltés megnövekedett igényeket kell kielégíteni további feldolgozó csomópontokat ad hozzá. A fürt később vissza, ha a további feldolgozó csomópontokat nem szükségesek skálázhatók.

További információkért tekintse meg a cikket [HDInsight-fürtök kapacitástervezése](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Az ajánlott a fürt virtuális gép típusa

Lásd: [csomópont konfigurációs és virtuális gépek méretei fürtök alapértelmezett](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) ajánlott virtuálisgép-típusok az egyes HDInsight-fürt.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>A HDInsight Hadoop-összetevők elérhetőségének ellenőrzése

Minden HDInsight-verzió felhőalapú terjesztése egy verziójának megfelelő Hortonworks Data Platform (HDP), és a egy Hadoop-ökoszisztéma összetevők készlete áll. Lásd: [HDInsight Component-Versioning](../hdinsight-component-versioning.md) az összes HDInsight-összetevők és azok aktuális verziók.

Az Ambari felhasználói felületén vagy az Ambari REST API-t használhatja a Hadoop-összetevők és a HDInsight-verziók ellenőrzéséhez.

Alkalmazások és összetevők, amelyek nem voltak elérhetők a helyi fürtökben, de nem a HDInsight-fürtök részét is hozzáadhatók, az élcsomóponton, vagy virtuális gépen az ugyanabban a virtuális hálózatban, mint a HDInsight-fürtöt. Egy külső Hadoop-alkalmazásokat, amelyek nem érhető el az Azure HDInsight rendszeren is telepíthető, az "Alkalmazások" lehetőséggel a HDInsight-fürt. Egyéni Hadoop-alkalmazások HDInsight-fürtön "szkriptműveletek" használatával telepíthető. Az alábbi táblázat néhány gyakori alkalmazások és a HDInsight-integrálási lehetőségek:

|**Alkalmazás**|**Integráció**
|---|---|
|Légmozgás|IaaS-vagy HDInsight Élcsomóponthoz
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Egyik sem (csak HDP)
|Datameer|HDInsight élcsomóponthoz
|A Datastax (Cassandra)|IaaS (helyett az Azure cosmos DB)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (helyett az Azure cosmos DB)
|NiFi|IaaS 
|Presto|IaaS-vagy HDInsight Élcsomóponthoz
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW helyett az Azure-ban)
|Tableau|IaaS 
|Vízvonallal|HDInsight élcsomóponthoz
|StreamSets|HDInsight Edge 
|Palantir|IaaS 
|Sailpoint|Iaas 

További információkért tekintse meg a cikket [elérhető különböző HDInsight-verziók az Apache Hadoop-összetevők](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>A Parancsfájlműveletek segítségével HDInsight-fürtök testre szabása

A fürtkonfiguráció nevű módszert biztosít a HDInsight egy **műveleti parancsfájl**. Szkriptműveletet Bash-szkript, amely egy HDInsight-fürt csomópontjain fut, és további összetevők és konfigurációs beállítások módosítását is használható.

Szkriptműveletek érhető el a HDInsight-fürt URI-t kell tárolni. Azok során vagy a fürt létrehozása után is használhatók, és is korlátozható egyes csomóponttípusok futtathatók.

A parancsfájl megőrzött vagy végre több alkalommal is. A megőrzött szkriptek segítségével testre szabhatja a fürthöz méretezés műveletek során hozzáadott új munkavégző csomópontok. Egy megőrzött parancsfájl is vonatkozhatnak módosításokat egy másik csomópont típusa, például a fő csomópontot, a méretezési műveletek esetén.

HDInsight biztosítja a következő összetevők telepíthetők a HDInsight-fürtök előre megírt parancsfájlok:

- Azure Storage-fiók hozzáadása
- A Hue telepítése
- A Presto telepítése
- A Solr telepítése
- A Giraph telepítése
- Hive-kódtárak előzetes betöltése
- Mono telepítése vagy frissítése

> [!Note]
> HDInsight biztosít az egyéni hadoop-összetevők és a Parancsfájlműveletek segítségével telepített összetevőket közvetlen támogatást.

A szkriptműveletek HDInsight alkalmazásként is az Azure piactéren tehetők közzé.

További információkért tekintse át a következő cikkeket:

- [A HDInsight külső Apache Hadoop-alkalmazások telepítése](../hdinsight-apps-install-applications.md)
- [A Parancsfájlműveletek segítségével HDInsight-fürtök testre szabása](../hdinsight-hadoop-customize-cluster-linux.md)
- [Egy HDInsight-alkalmazás közzététele az Azure Marketplace-en](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Testreszabása a Bootstrap használatával HDInsight-konfigurációkat

Például a konfigurációs fájlokban configs vált `core-site.xml`, `hive-site.xml` és `oozie-env.xml` Bootstrap használatával módosíthatók. A következő parancsfájlt a következő példa a Powershell használatával:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzureRmHDInsightClusterConfig '
    | Set—AzureRmHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzureRmHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzureRmHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

További információkért tekintse meg a cikket [Bootstrap használatával testre szabhatja a HDInsight-fürtök](../hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Hozzáférés ügyféleszközök a HDInsight Hadoop-fürt élcsomópontok

Üres élcsomópontot egy Linuxos virtuális gép telepítve és konfigurálva, a fő csomópontok azonos ügyféleszközök, de nincs futó Hadoop-szolgáltatásokhoz. Az élcsomópont a következő célokra használhatók:

- a fürt eléréséhez
- ügyfél alkalmazások tesztelése
- ügyfél-alkalmazások üzemeltetése

Élcsomópontok hozhatók létre és törlése az Azure Portalon keresztül, és során is használható vagy után a fürt létrehozása. Az élcsomópont létrehozása után az élcsomóponthoz SSH használatával csatlakozhat, és futtassa az ügyfél eszközök eléréséhez a HDInsight Hadoop-fürtöt. Az élcsomópont ssh végpont van `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.

További információkért tekintse meg a cikket [üres élcsomópontok használata a HDInsight Apache Hadoop-fürtök](../hdinsight-apps-use-edge-node.md).

## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Fürtök felfelé és lefelé méretezési funkciójával

HDInsight biztosítja a rugalmasságot felkínálva a lehetőséget az növelheti vagy csökkentheti a feldolgozó csomópontok a fürtben. Ez a funkció lehetővé teszi, hogy egy fürt zsugorítani óra múlva, vagy a hétvégeken, és bontsa ki a üzleti megnövekedett igényeket kell kielégíteni során.

Fürtméretezés automatizálható a következő módszerekkel:

### <a name="powershell-cmdlet"></a>PowerShell-parancsmag

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

### <a name="azure-cli"></a>Azure CLI

```powershell
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```

### <a name="azure-portal"></a>Azure Portal

Ha csomópontot ad hozzá a futó HDInsight-fürt, a folyamatban lévő vagy futó feladatok nem befolyásolja. Új feladatok biztonságosan küldheti a skálázási művelet végrehajtása közben. Ha a méretezési műveletek bármilyen okból nem sikerül, a hiba szabályosan történik, a fürt és a egy működési állapotot.

Azonban ha a fürt csomópontjai eltávolításával leskálázódott, folyamatban lévő vagy futó feladat sikertelen lesz a skálázási művelet befejeződése után. Ezt a hibát az okozza a szolgáltatások újraindítása a folyamat során. A probléma megoldására, várja meg, a feladat befejeződését, mielőtt a fürt vertikális, manuálisan a feladatok leállítása vagy küldje el újra a feladatok, miután a skálázási művelet lezárult.

Ha csökkenti a méretét a fürt egy feldolgozó csomópontok minimális le, HDFS előfordulhat, hogy letöltés állapottal csökkentett módban, ha a munkavégző csomópontok javítás céljából, vagy közvetlenül a skálázási művelet után indulnak újra. Ahhoz, hogy a HDFS biztonságos módból a következő parancsot futtathatja:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

A csökkentett mód elhagyása, után manuálisan eltávolíthatja az ideiglenes fájlokat, vagy várja meg, Hive idővel törölni őket automatikusan.

További információkért tekintse meg a cikket [méretezési HDInsight-fürtök](../hdinsight-scaling-best-practices.md).

## <a name="use-hdinsight-with-azure-virtual-network"></a>HDInsight használata az Azure Virtual Network

Az Azure virtuális hálózatok lehetővé teszik a Azure-erőforrások, például az Azure Virtual Machines, a biztonságosan kommunikálnak egymással, az internethez, és a helyszíni hálózatok, szűréssel és az Útválasztás a hálózati forgalmat.

Azure Virtual Network használata a HDInsight lehetővé teszi, hogy a következő esetekben:

- Csatlakozás a HDInsight közvetlenül a helyi hálózatról.
- HDInsight-adatokhoz való csatlakozásról tárolja egy Azure-beli virtuális hálózathoz.
- Közvetlen hozzáférés a Hadoop-szolgáltatásokhoz, amelyek nem érhetők el nyilvánosan az interneten keresztül. Ha például Kafka API-k vagy a HBase Java API-t.

HDInsight vagy hozzáadhat egy új vagy meglévő Azure virtuális hálózatban. HDInsight ad hozzá egy meglévő virtuális hálózatot, ha a meglévő hálózati biztonsági csoportok és a felhasználó által megadott útvonalak frissítenie kell, hogy a korlátlan hozzáférés [több IP-címek](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) az Azure-adatközpontban. Győződjön meg arról, hogy nem blokkolja a forgalmat is, a [portok](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ports) HDInsight szolgáltatásokat használják.

> [!Note]
> HDInsight jelenleg nem támogatja a kényszerített bújtatás. Kényszerített bújtatás az alhálózat-beállítással, amely kényszeríti a kimenő internetes forgalmat egy eszközön, az ellenőrzés és naplózás. Kényszerített bújtatás egyik alhálózatában HDInsight telepítése előtt távolítsa el, vagy hozzon létre egy új alhálózatot a HDInsight. HDInsight még nem támogatja a kimenő hálózati kapcsolat korlátozása.

További információkért tekintse át a következő cikkeket:

- [Az Azure virtuális hálózatok – áttekintés](../../virtual-network/virtual-networks-overview.md)
- [Azure virtuális hálózat használatával Azure HDInsight kiterjesztése](../hdinsight-extend-hadoop-virtual-network.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Azure virtuális hálózati Szolgáltatásvégpontok az Azure-szolgáltatásokhoz való biztonságos kapcsolódás

HDInsight támogatja [virtuális hálózati Szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) Ez lehetővé teszi annak biztonságosan csatlakozhat az Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB és SQL-adatbázisok. Egy szolgáltatásvégpont engedélyezésével az Azure HDInsight, a forgalom egy biztonságos útvonalat az Azure adatközponton belül keresztül. A megnövelt biztonság, a hálózati rétegben zárolását, így a megadott virtuális hálózatok (Vnetek) tárfiókok big Data típusú adatok, és továbbra is használni HDInsight-fürtök zökkenőmentesen eléréséhez, és az adatok feldolgozásához.

További információkért tekintse át a következő cikkeket:

- [Virtuális hálózati szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [A Szolgáltatásvégpontok HDInsight biztonságának növelése](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/.md)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>HDInsight csatlakozni a helyszíni hálózathoz

HDInsight az Azure Virtual Networkökhöz és VPN-átjáró segítségével csatlakoztathatók a helyszíni hálózathoz. A következő lépések segítségével kapcsolatot:

- Használja a HDInsight egy Azure virtuális hálózatban, amely kapcsolódik a helyszíni hálózathoz.
- Konfigurálja a DNS-névfeloldás a virtuális hálózat és helyszíni hálózat között.
- Konfigurálja a hálózati biztonsági csoportok vagy a felhasználó által megadott útvonalak (UDR) szabályozhatja a hálózati forgalmat.

További információkért tekintse meg a cikket [HDInsight csatlakoztatása a helyszíni hálózathoz](../connect-on-premises-network.md)

## <a name="next-steps"></a>További lépések

Olvassa el az oktatóanyag-sorozatban a következő cikkben:

- [A helyszíni Azure HDInsight Hadoop-áttelepítés Storage ajánlott eljárásai](apache-hadoop-on-premises-migration-best-practices-storage.md)