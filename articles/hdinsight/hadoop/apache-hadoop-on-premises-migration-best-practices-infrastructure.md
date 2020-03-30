---
title: 'Infrastruktúra: Helyszíni Apache Hadoop az Azure HDInsight'
description: Ismerje meg az infrastruktúra gyakorlati tanácsait a helyszíni Hadoop-fürtök Azure HDInsightba való áttelepítéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: d7ee8ae121e3cbb9760a87c95d12109a9b05e0c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951513"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Telepítse át a helyszíni Apache Hadoop-fürtöket az Azure HDInsightba – az infrastruktúra ajánlott eljárások

Ez a cikk az Azure HDInsight-fürtök infrastruktúrájának kezeléséhez nyújt javaslatokat. Ez egy olyan sorozat része, amely gyakorlati tanácsokkal segíti a helyszíni Apache Hadoop-rendszerek Azure HDInsightba való áttelepítését.

## <a name="plan-for-hdinsight-cluster-capacity"></a>A HDInsight-fürt kapacitásának megtervezése

A HDInsight-fürt kapacitástervezéséhez a legfontosabb döntéseket a következőknek kell meghoznia:

**Régió**  
Az Azure-régió határozza meg, ahol a fürt fizikailag kiépített. Az olvasások és írások késésének minimalizálása érdekében a fürtnek ugyanabban a régióban kell lennie, mint az adatoknak.

**Tárolási hely és méret**  
Az alapértelmezett tárolónak ugyanabban a régióban kell lennie, mint a fürtnek.48 csomós fürt esetén 4–8 tárfiók használata ajánlott. Bár lehet, hogy már elegendő teljes tárterület, minden tárfiók további hálózati sávszélességet biztosít a számítási csomópontok számára. Ha több tárfiók van, használjon véletlenszerű nevet minden tárfiókhoz előtag nélkül. A véletlenszerű elnevezés célja a tárolási szűk keresztmetszetek (szabályozás) vagy a gyakori módú hibák értékének csökkentése az összes fiókban. A jobb teljesítmény érdekében tárfiókonként csak egy tárolót használjon.

**Virtuális gép mérete és típusa (most már támogatja a G-sorozatot)**  
Minden fürttípus rendelkezik egy csomó csomóponttípusok, és minden csomópont típus speciális beállításokat a virtuális gép méretét és típusát. A virtuális gép méretét és típusát a PROCESSZOR feldolgozási teljesítménye, a RAM mérete és a hálózati késés határozza meg. A szimulált számítási feladatok segítségével meghatározhatja az optimális virtuális gép méretét és típusát az egyes csomóponttípusokhoz.

**Dolgozó csomópontok száma**  
A munkavégző csomópontok kezdeti száma a szimulált munkaterhelések használatával határozható meg. A fürt később skálázható további munkavégző csomópontok hozzáadásával, hogy megfeleljen a csúcsterhelési igényeknek. A fürt később lehet kicsinyített vissza, ha a további munkavégző csomópontok nem szükséges.

További információt a [HDInsight-fürtök kapacitástervezése című témakörben talál.](../hdinsight-capacity-planning.md)

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Ajánlott virtuálisgép-típus használata a fürthöz

Lásd: [Alapértelmezett csomópontkonfiguráció és a fürtök virtuálisgép-méretek](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) az egyes HDInsight-fürtök höz ajánlott virtuálisgép-típusokhoz.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>A Hadoop-összetevők elérhetőségének ellenőrzése a HDInsightban

Minden HDInsight-verzió a Hadoop-környezet-rendszer összetevőinek felhőalapú disztribúciója. Az összes HDInsight-összetevővel és azok jelenlegi verzióival kapcsolatos részleteket a [HDInsight-összetevőverziók](../hdinsight-component-versioning.md) című témakörben találja.

Az Apache Ambari UI vagy az Ambari REST API segítségével is ellenőrizheti a Hadoop-összetevőket és -verziókat a HDInsightban.

Alkalmazások vagy összetevők, amelyek elérhetők voltak a helyszíni fürtök, de nem részei a HDInsight-fürtök adhatók hozzá egy peremhálózati csomópont, vagy egy virtuális gép ugyanabban a virtuális hálózatban, mint a HDInsight-fürt. Egy külső gyártótól származó Hadoop-alkalmazás, amely nem érhető el az Azure HDInsight-on, telepíthető a HDInsight-fürt "Alkalmazások" beállításával. Az egyéni Hadoop-alkalmazások "parancsfájlműveletek" használatával telepíthetők a HDInsight-fürtre. Az alábbi táblázat néhány gyakori alkalmazást és hdinsight-integrációs beállításokat sorol fel:

|**Alkalmazás**|**Integráció**
|---|---|
|Légáramlás|IaaS vagy HDInsight peremhálózati csomópont
|Alluxio között|IaaS  
|Arcadia|IaaS 
|Atlas|Nincs (csak HDP)
|Datameer között|HDInsight peremhálózati csomópont
|Datastax (Cassandra)|IaaS (CosmosDB alternatíva az Azure-ban)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador között|IaaS 
|Mongo között|IaaS (CosmosDB alternatíva az Azure-ban)
|NiFi között|IaaS 
|Presto|IaaS vagy HDInsight peremhálózati csomópont
|Piton 2|PaaS 
|Piton 3|PaaS 
|R|PaaS 
|Sas|IaaS 
|Vertica|IaaS (SQLDW alternatíva az Azure-ban)
|Tableau|IaaS 
|Vízvonal|HDInsight peremhálózati csomópont
|Streamsets (StreamSets)|HDInsight perem 
|Palantir között|IaaS 
|Vitorlapont|Iaas között 

További információt az [Apache Hadoop különböző HDInsight-verziókkal elérhető összetevői](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) című cikkben talál.

## <a name="customize-hdinsight-clusters-using-script-actions"></a>A HDInsight-fürtök testreszabása parancsfájlműveletek használatával

A HDInsight **parancsfájlműveletnek**nevezett fürtkonfigurációs módszert biztosít. A parancsfájlművelet a Bash parancsfájl, amely egy HDInsight-fürt csomópontjain fut, és további összetevők telepítésére és a konfigurációs beállítások módosítására használható.

A parancsfájlműveleteket a HDInsight-fürtről elérhető URI-n kell tárolni. A fürt létrehozása során vagy azt követően is használhatók, és csak bizonyos csomóponttípusokon futtathatók.

A parancsfájl egy alkalommal megőrizhető vagy végrehajtható. A megőrzött parancsfájlok a fürthöz méretezési műveleteken keresztül hozzáadott új munkavégző csomópontok testreszabására szolgálnak. A megőrzött parancsfájl egy másik csomóponttípusra, például egy főcsomópontra is alkalmazhat módosításokat, ha méretezési műveletek történnek.

A HDInsight előre megírt parancsfájlokat biztosít a következő összetevők HDInsight-fürtökön való telepítéséhez:

- Azure Storage-fiók hozzáadása
- Színárnyalat telepítése
- Presto telepítése
- Solr telepítése
- Giraph telepítése
- Hive-könyvtárak előzetes betöltése
- Mono telepítése vagy frissítése

> [!Note]  
> A HDInsight nem nyújt közvetlen támogatást a parancsfájlműveletek használatával telepített egyéni hadoop-összetevőkhöz vagy összetevőkhöz.

Parancsfájl-műveletek is közzétehető az Azure Marketplace HDInsight-alkalmazásként.

További információkért tekintse át a következő cikkeket:

- [Külső gyártótól származó Apache Hadoop alkalmazások telepítése a HDInsight-ra](../hdinsight-apps-install-applications.md)
- [A HDInsight-fürtök testreszabása parancsfájlműveletek használatával](../hdinsight-hadoop-customize-cluster-linux.md)
- [HDInsight-alkalmazás közzététele az Azure Piactéren](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>A HDInsight-konfigurációk testreszabása a Bootstrap használatával

Változások a konfigurációs konfigurációk a `core-site.xml`config fájlokat, mint például a , `hive-site.xml` és `oozie-env.xml` lehet a Bootstrap használatával. A következő parancsfájl egy példa a Powershell [AZ modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) [új-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)parancsmaghasználatával:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
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

További információt a [HDInsight-fürtök testreszabása a Bootstrap használatával](../hdinsight-hadoop-customize-cluster-bootstrap.md)című cikkben talál.  Lásd még: [HDInsight-fürtök kezelése az Apache Ambari REST API használatával.](../hdinsight-hadoop-manage-ambari-rest-api.md)

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Ügyféleszközök elérése a HDInsight Hadoop-fürt peremhálózati csomópontjaiból

Az üres peremhálózati csomópont egy Linux virtuális gép, amelynek ugyanazokat az ügyféleszközöket telepíti és konfigurálja, mint a fő csomópontokon, de hadoop-szolgáltatások futtatása nélkül. Az élcsomópont a következő célokra használható:

- hozzáférés a fürthöz
- tesztelés, ügyfélalkalmazások
- ügyfélalkalmazások üzemeltetése

Peremhálózati csomópontok hozhatók létre, és törölhetők az Azure Portalon keresztül, és a fürt létrehozása során vagy azt követően is használható. A peremhálózati csomópont létrehozása után csatlakozhat a peremhálózati csomóponthoz az SSH használatával, és futtathat ügyféleszközöket a Hadoop-fürt HDInsight-ban való eléréséhez. A peremcsomópont végpontja `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.


További információt az [Üres peremhálózati csomópontok használata az Apache Hadoop-fürtökön a HDInsightban című témakörben talál.](../hdinsight-apps-use-edge-node.md)


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Fürtök felskálázási és lekicsinylési funkciójának használata

A HDInsight rugalmasságot biztosít azáltal, hogy lehetővé teszi a fürtökben lévő munkavégző csomópontok számának skálázását és leskálázását. Ez a funkció lehetővé teszi a fürt zsugorítását munkaidő vagy hétvégén, és kibonthatja azt csúcsüzleti igények alatt. További információkért lásd:

* [HDInsight-fürtök méretezése](../hdinsight-scaling-best-practices.md).
* [Fürtök méretezése](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>A HDInsight használata az Azure virtuális hálózattal

Az Azure virtuális hálózatok lehetővé teszik, hogy az Azure-erőforrások, például az Azure virtuális gépek, biztonságosan kommunikáljanak egymással, az interneten és a helyszíni hálózatokon a hálózati forgalom szűrésével és útválasztásával.

Az Azure Virtual Network hdinsight használatával a következő forgatókönyvek használhatók:

- Csatlakozás a HDInsighthoz közvetlenül egy helyszíni hálózatról.
- A HDInsight csatlakoztatása az Azure virtuális hálózat adattáraihoz.
- Közvetlenül elérhető hadoop-szolgáltatások, amelyek nem érhetők el nyilvánosan az interneten keresztül. Például a Kafka API-k vagy a HBase Java API.

A HDInsight hozzáadható egy új vagy meglévő Azure virtuális hálózathoz. Ha a HDInsight egy meglévő virtuális hálózathoz van hozzáadva, a meglévő hálózati biztonsági csoportokat és a felhasználó által definiált útvonalakat frissíteni kell, hogy korlátlan hozzáférést lehessen biztosítani [több IP-címhez](../hdinsight-management-ip-addresses.md) az Azure adatközpontban. Ügyeljen arra is, hogy ne blokkolja a forgalmat a HDInsight-szolgáltatások által használt [portok.](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports)

> [!Note]  
> A HDInsight jelenleg nem támogatja a kényszerített bújtatást. A kényszerített bújtatás olyan alhálózati beállítás, amely a kimenő internetes forgalmat egy eszközre kényszeríti ellenőrzésre és naplózásra. Távolítsa el a kényszerített bújtatást, mielőtt a HDInsightot alhálózatba telepítené, vagy hozzon létre egy új alhálózatot a HDInsight számára. A HDInsight nem támogatja a kimenő hálózati kapcsolat korlátozását sem.

További információkért tekintse át a következő cikkeket:

- [Az Azure virtuális hálózatok áttekintése](../../virtual-network/virtual-networks-overview.md)
- [Azure HDInsight kiterjesztése Azure virtuális hálózat használatával](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Biztonságos csatlakozás az Azure-szolgáltatásokhoz az Azure virtuális hálózati szolgáltatás végpontjainak használatával

A HDInsight támogatja a [virtuális hálózati szolgáltatás végpontjait,](../../virtual-network/virtual-network-service-endpoints-overview.md)amelyek lehetővé teszik az Azure Blob Storage, az Azure Data Lake Storage Gen2, a Cosmos DB és az SQL-adatbázisok biztonságos csatlakoztatását. Az Azure HDInsight szolgáltatásvégpontjának engedélyezésével a forgalom biztonságos útvonalon halad az Azure adatközponton belülről. Ezzel a fokozott biztonsági szinttel a hálózati rétegen lezárhatja a big data storage-fiókokat a megadott virtuális hálózatokhoz (VNET), és továbbra is zökkenőmentesen használhatja a HDInsight-fürtöket az adatok eléréséhez és feldolgozásához.

További információkért tekintse át a következő cikkeket:

- [Virtuális hálózati szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [A HDInsight biztonságának növelése szolgáltatásvégekkel](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>A HDInsight csatlakoztatása a helyszíni hálózathoz

A HDInsight az Azure virtuális hálózatok és egy VPN-átjáró használatával csatlakoztatható a helyszíni hálózathoz. A következő lépésekkel lehet létrehozni a kapcsolatot:

- A HDInsight használata olyan Azure virtuális hálózatban, amely a helyszíni hálózathoz való kapcsolódással rendelkezik.
- Konfigurálja a DNS-névfeloldást a virtuális hálózat és a helyszíni hálózat között.
- Konfigurálja a hálózati biztonsági csoportokat vagy a felhasználó által definiált útvonalakat (UDR) a hálózati forgalom szabályozására.

További információt a [HDInsight csatlakoztatása a helyszíni hálózathoz](../connect-on-premises-network.md) című cikkben talál.

## <a name="next-steps"></a>További lépések

Olvassa el a sorozat következő cikkét: A helyszíni Azure [HDInsight Hadoop-áttelepítéssel kapcsolatos gyakorlati tanácsok.](apache-hadoop-on-premises-migration-best-practices-storage.md)
