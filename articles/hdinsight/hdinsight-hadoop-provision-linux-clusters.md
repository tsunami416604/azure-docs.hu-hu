---
title: Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával
description: Állítsa be a Hadoop, a Kafka, a Spark, a HBase, az R Server vagy a Storm-fürtöket a HDInsight hoz egy böngészőből, az Azure klasszikus CLI-ből, az Azure PowerShellből, a RESTből vagy az SDK-ból.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 246ec08e9b4edb33fa49318b68cc4364534282b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064652"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ismerje meg, hogyan állíthatja be és konfigurálhatja az Apache Hadoop, az Apache Spark, az Apache Kafka, az Interactive Query, az Apache HBase, az ML-szolgáltatások vagy az Apache Storm szolgáltatást a HDInsightban. Azt is megtudhatja, hogyan szabhatja testre a fürtöket, és hogyan adhat hozzá biztonságot azáltal, hogy csatlakozik egy tartományhoz.

A Hadoop-fürt több virtuális gépből (csomópontból) áll, amelyek a feladatok elosztott feldolgozásához használatosak. Az Azure HDInsight kezeli az egyes csomópontok telepítésének és konfigurálásának végrehajtási részleteit, így csak általános konfigurációs információkat kell megadnia.

> [!IMPORTANT]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. További információ a [fürt törléséről.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Fürt telepítési módszerei

Az alábbi táblázat a HDInsight-fürt beállításához használható különböző módszereket mutatja be.

| A létrehozott fürtök | Webböngésző | Parancssor | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure-portál](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure-adatgyár](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager-sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Ez a cikk végigvezeti az [Azure Portalon,](https://portal.azure.com)ahol hdinsight-fürtöt hozhat létre.

## <a name="basics"></a>Alapvető beállítások

![hdinsight hozzon létre beállításokat egyéni gyors](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Projekt részletei

[Az Azure Resource Manager](../azure-resource-manager/management/overview.md) segítségével az alkalmazás erőforrásait csoportként, úgynevezett [Azure-erőforráscsoportként](../azure-resource-manager/management/overview.md#resource-groups)dolgozhat. Egyetlen koordinált műveletben üzembe helyezheti, frissítheti, figyelheti vagy törölheti az alkalmazás összes erőforrását.

### <a name="cluster-details"></a>Fürt részletei

#### <a name="cluster-name"></a>Fürt neve

A HDInsight-fürtnevekre a következő korlátozások vonatkoznak:

* Megengedett karakterek: a-z, 0-9, A-Z
* Maximális hossz: 59
* Fenntartott nevek: alkalmazások
* A fürt elnevezési hatóköre az összes Azure-ra, az összes előfizetésre vonatkozóan. Így a fürt nevének világszerte egyedinek kell lennie.
* Az első hat karakternek egyedinek kell lennie a virtuális hálózaton belül

#### <a name="region"></a>Régió

Nem kell explicit módon megadnia a fürt helyét: A fürt ugyanazon a helyen található, mint az alapértelmezett tároló. A támogatott régiók listájáért válassza a [HDInsight-díjszabás](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409) **Terület** legördülő listáját.

#### <a name="cluster-type"></a>Fürt típusa

Az Azure HDInsight jelenleg a következő fürttípusokat biztosítja, amelyek mindegyike rendelkezik összetevők készletével bizonyos funkciók biztosításához.

> [!IMPORTANT]  
> A HDInsight-fürtök különböző típusú akta, amelyek mindegyike egyetlen számítási feladathoz vagy technológiához érhető el. Nincs támogatott módszer olyan fürt létrehozására, amely több típust egyesít, például a Stormot és a HBase-t egy fürtön. Ha a megoldás olyan technológiákat igényel, amelyek több HDInsight-fürttípusra vannak elosztva, az [Azure virtuális hálózat](https://docs.microsoft.com/azure/virtual-network) csatlakoztathatja a szükséges fürttípusokat.

| Fürt típusa | Funkció |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Kötegelt lekérdezés és a tárolt adatok elemzése |
| [HBase](hbase/apache-hbase-overview.md) |Feldolgozása nagy mennyiségű séma nélküli, NoSQL adatok |
| [Interaktív lekérdezés](./interactive-query/apache-interactive-query-get-started.md) |Memórián belüli gyorsítótárazás interaktív és gyorsabb Hive-lekérdezésekhez |
| [Kafka](kafka/apache-kafka-introduction.md) | Elosztott streamelési platform, amely valós idejű streamelési adatfolyamatok és alkalmazások létrehozásához használható |
| [ML-szolgáltatások](r-server/r-server-overview.md) |Különböző big data-statisztikák, prediktív modellezés és gépi tanulási képességek |
| [Spark](spark/apache-spark-overview.md) |Memórián belüli feldolgozás, interaktív lekérdezések, mikrokötegű adatfolyam-feldolgozás |
| [Storm](storm/apache-storm-overview.md) |Valós idejű eseményfeldolgozás |

#### <a name="version"></a>Verzió

Válassza ki a HDInsight verzióját ehhez a fürthöz. További információt a [Támogatott HDInsight-verziók című témakörben](hdinsight-component-versioning.md#supported-hdinsight-versions)talál.

### <a name="cluster-credentials"></a>Fürt hitelesítő adatai

A HDInsight-fürtökkel két felhasználói fiókot konfigurálhat a fürt létrehozása során:

* Fürt bejelentkezési felhasználóneve: Az alapértelmezett felhasználónév *a rendszergazda*. Az Azure Portal alapkonfigurációját használja. Néha "Fürtfelhasználó" vagy "HTTP-felhasználó".
* Secure Shell (SSH) felhasználónév: A fürthöz sSH-n keresztül történő csatlakozásra szolgál. További információ: [SSH használata a HDInsight segítségével.](hdinsight-hadoop-linux-use-ssh-unix.md)

A HTTP-felhasználónév a következő korlátozásokkal rendelkezik:

* Megengedett speciális `_` karakterek: és`@`
* Karakterek nem engedélyezettek: #;."",\/:'!*?$(){}[]<>|&-==+%~^szóköz
* Maximális hossz: 20

Az SSH-felhasználónév a következő korlátozásokkal rendelkezik:

* Megengedett speciális`_` karakterek: és`@`
* Karakterek nem engedélyezettek: #;."",\/:'!*?$(){}[]<>|&-==+%~^szóköz
* Maximális hossz: 64
* Fenntartott nevek: hadoop, felhasználók, oozie, struktúra, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, fonal, hcat, ams, hbase, vihar, rendszergazda, admin, felhasználó, user1, teszt, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, konzol, David, vendég, John, tulajdonos, gyökér, szerver, sql, támogatás, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>Storage

![Fürttárolási beállítások: HDFS-kompatibilis végpontok](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Bár a Hadoop helyszíni telepítése a Hadoop elosztott fájlrendszert (HDFS) használja a fürtön való tároláshoz, a felhőben fürthöz csatlakoztatott tárolóvégpontokat használ. A felhőalapú tárolás használata azt jelenti, hogy biztonságosan törölheti a számítási hdinsight-fürtöket, miközben megőrzi az adatokat.

A HDInsight-fürtök a következő tárolási lehetőségeket használhatják:

* 2. generációs Azure Data Lake Storage
* 1. generációs Azure Data Lake Storage
* Általános célú Azure-tárhely v2
* Általános célú Azure-tárhely 1.
* Azure Storage Block blob **(csak másodlagos tárolóként támogatott)**

A HDInsight használatával kapcsolatos tárolási lehetőségekről az [Azure HDInsight-fürtökkel való tárolási lehetőségek összehasonlítása](hdinsight-hadoop-compare-storage-options.md)című témakörben talál további információt.

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen egy további tárfiók használata nem támogatott.

A konfiguráció során az alapértelmezett tárolási végpont egy Azure Storage-fiók vagy a Data Lake Storage blobtárolóját adja meg. Az alapértelmezett tároló alkalmazás- és rendszernaplókat tartalmaz. Szükség esetén további összekapcsolt Azure Storage-fiókokat és Data Lake Storage-fiókokat adhat meg, amelyekhez a fürt hozzáférhet. A HDInsight-fürtnek és a függő tárfiókoknak ugyanabban az Azure-helyen kell lenniük.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="metastore-settings"></a>Metaáruház beállításai

Opcionális Hive vagy Apache Oozie metaáruházakat hozhat létre. Azonban nem minden fürttípus támogatja a metaboltokat, és az Azure SQL Data Warehouse nem kompatibilis a metaáruházakkal.

További információ: [Külső metaadat-tárolók használata az Azure HDInsightban című témakörben.](./hdinsight-use-external-metadata-stores.md)

> [!IMPORTANT]  
> Egyéni metatároló létrehozásakor ne használjon kötőjeleket, kötőjeleket vagy szóközöket az adatbázis nevében. Ez a fürt létrehozási folyamatának sikertelensedését okozhatja.

#### <a name="sql-database-for-hive"></a>SQL-adatbázis a Hive-hoz

Ha meg szeretné őrizni a Hive-táblák at egy HDInsight-fürt törlése után, használjon egyéni metatárolót. Ezután csatolhatja a metastore egy másik HDInsight-fürthöz.

Egy HDInsight-fürthöz létrehozott HDInsight-metatár nem osztható meg a HDInsight-fürt különböző verziói között. A HDInsight-verziók listáját a [Támogatott HDInsight-verziók című témakörben tésiterületen tetszésszerint.](hdinsight-component-versioning.md#supported-hdinsight-versions)

#### <a name="sql-database-for-oozie"></a>SQL adatbázis Oozie-hoz

Az Oozie használata kor a teljesítmény növelése érdekében használjon egyéni metatárolót. A metatár is hozzáférést biztosít az Oozie-feladat adataihoz a fürt törlése után.

#### <a name="sql-database-for-ambari"></a>SQL-adatbázis az Ambari számára

Az Ambari a HDInsight-fürtök figyelésére, a konfigurációk módosítására és a fürtkezelési információk, valamint a feladatelőzmények tárolására szolgál. Az egyéni Ambari DB szolgáltatás lehetővé teszi egy új fürt telepítését és az Ambari telepítését egy külső adatbázisban, amelyet Ön kezel. További információ: [Custom Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Egyéni Oozie metatároló nem használható fel újra. Egyéni Oozie metatároló használatához meg kell adnia egy üres Azure SQL-adatbázist a HDInsight-fürt létrehozásakor.

## <a name="security--networking"></a>Biztonság + hálózatépítés

![hdinsight létrehozási beállítások válassza a vállalati biztonsági csomagot](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Vállalati biztonsági csomag

A Hadoop, spark, HBase, Kafka és Interactive Query fürttípusok esetén engedélyezheti az **Enterprise Security Package csomagot.** Ez a csomag lehetőséget biztosít egy biztonságosabb fürtbeállításhoz az Apache Ranger használatával és az Azure Active Directoryval való integrációval. További információt az [Azure HDInsight vállalati biztonságának áttekintése című témakörben talál.](./domain-joined/hdinsight-security-overview.md)

Az Enterprise biztonsági csomag lehetővé teszi a HDInsight integrálását az Active Directoryval és az Apache Rangerrel. A vállalati biztonsági csomaggal több felhasználó is létrehozható.

A tartományhoz csatlakozó HDInsight-fürt létrehozásáról a [Tartományhoz csatlakozott HDInsight-sandbox-környezet létrehozása című témakörben](./domain-joined/apache-domain-joined-configure.md)talál további információt.

### <a name="tls"></a>TLS

További információ: [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)

### <a name="virtual-network"></a>Virtuális hálózat

Ha a megoldás olyan technológiákat igényel, amelyek több HDInsight-fürttípusra vannak elosztva, az [Azure virtuális hálózat](https://docs.microsoft.com/azure/virtual-network) csatlakoztathatja a szükséges fürttípusokat. Ez a konfiguráció lehetővé teszi, hogy a fürtök és a rájuk telepített kódok közvetlenül kommunikáljanak egymással.

Az Azure virtuális hálózat HDInsight-alapú használatáról a [HDInsight virtuális hálózatának megtervezése](hdinsight-plan-virtual-network-deployment.md)című témakörben talál további információt.

Az Azure virtuális hálózaton belüli két fürttípus használatát az [Apache Spark strukturált streamelésének használata az Apache Kafka használatával](hdinsight-apache-kafka-spark-structured-streaming.md)című témakörben ismeri el. A HDInsight virtuális hálózattal való használatáról, beleértve a virtuális hálózat ra vonatkozó konkrét konfigurációs követelményeket, a [HDInsight virtuális hálózatának megtervezése](hdinsight-plan-virtual-network-deployment.md)című témakörben talál további információt.

### <a name="disk-encryption-setting"></a>Lemeztitkosítási beállítás

További információt az [Ügyfél által kezelt kulcslemez-titkosítás](./disk-encryption.md)című témakörben talál.

### <a name="kafka-rest-proxy"></a>Kafka REST proxy

Ez a beállítás csak a Kafka fürttípusesetén érhető el. További információt a [REST-proxy használata](./kafka/rest-proxy.md)című témakörben talál.

### <a name="identity"></a>Identitás

További információ: [Felügyelt identitások az Azure HDInsightban.](./hdinsight-managed-identities.md)

## <a name="configuration--pricing"></a>Konfiguráció + árképzés

![A HDInsight válassza ki a csomópont méretét](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

A fürt létezésének ideje nélkül a csomópont használatáért díjat számítunk fel. A számlázás a fürt létrehozásakor kezdődik, és a fürt törlésekor leáll. A fürtöket nem lehet lefoglalni vagy várakoztatni.

### <a name="node-configuration"></a>Csomópont-konfiguráció

Minden fürttípus saját számú csomók, a csomópontok terminológiája és az alapértelmezett virtuális gép mérete rendelkezik. Az alábbi táblázatban az egyes csomóponttípusok csomópontjainak száma zárójelben van.

| Típus | Csomópontok | Ábra |
| --- | --- | --- |
| Hadoop |Fő csomópont (2), Munkavégző csomópont (1+) |![HDInsight Hadoop-fürtcsomópontok](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Főkiszolgáló (2), régiókiszolgáló (1+), fő/ZooKeeper csomópont (3) |![HDInsight HBase fürttípus beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus csomópont (2), felügyeleti kiszolgáló (1+), ZooKeeper csomópont (3) |![HDInsight storm fürttípus beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Fő csomópont (2), Feldolgozó csomópont (1+), ZooKeeper csomópont (3) (ingyenes Az A1 ZooKeeper virtuális gép méret) |![HDInsight spark-fürttípus beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

További információ: [Alapértelmezett csomópont konfigurációja és virtuálisgép-méretek fürtök](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) "Mik a Hadoop-összetevők és -verziók a HDInsight?"

A HDInsight-fürtök költségét a csomópontok száma és a csomópontok virtuális gépmérete határozza meg.

A különböző fürttípusok különböző csomóponttípusokkal, csomópontszámokkal és csomópontméretekkel rendelkeznek:
* Hadoop fürttípus alapértelmezett:
    * Két *fejcsomópont*  
    * Négy *munkavégző csomópont*
* A Storm cluster típusalapértelmezett:
    * Két *Nimbus csomópont*
    * Három *ZooKeeper csomópont*
    * Négy *felügyeleti csomópont*

Ha csak a HDInsightkipróbálása, azt javasoljuk, hogy egy feldolgozócsomópontot használjon. A HDInsight-díjszabásról a [HDInsight-díjszabás](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)című témakörben talál további információt.

> [!NOTE]  
> A fürt méretkorlátja azure-előfizetések között változik. Lépjen kapcsolatba az [Azure számlázási támogatásával](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) a korlát növeléséhez.

Ha az Azure Portalon konfigurálja a fürtöt, a csomópont mérete elérhető a **Konfiguráció + díjszabás** lapon keresztül. A portálon a különböző csomópontméretekhez kapcsolódó költségeket is láthatja.

### <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek

A fürtök telepítésekor válassza a számítási erőforrásokat az üzembe helyezni kívánt megoldás alapján. A hdinsight-fürtökhöz a következő virtuális gépek et használják:

* A és D1-4 sorozatú virtuális gépek: [Általános célú Linux virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14 sorozatú virtuális gép: [Memóriaoptimalizált Linux virtuális gépméretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Ha meg szeretné tudni, hogy milyen értéket kell használnia a virtuális gép méretének megadásához a fürt különböző SDK-k használatával vagy az Azure PowerShell használatával történő létrehozása során, olvassa el [a HDInsight-fürtökhez használandó virtuálisgép-méretek című témakört.](../cloud-services/cloud-services-sizes-specs.md#size-tables) Ebből a csatolt cikkből használja a táblák **Méret** oszlopának értékét.

> [!IMPORTANT]  
> Ha egy fürtben 32-nél több feldolgozócsomópontra van szüksége, ki kell választania egy legalább 8 maggal és 14 GB RAM-mal rendelkező főcsomópontméretet.

További információ: [Méretek a virtuális gépekhez.](../virtual-machines/windows/sizes.md) A különböző méretű árakról a [HDInsight-díjszabás](https://azure.microsoft.com/pricing/details/hdinsight)című témakörben talál további információt.

### <a name="add-application"></a>Alkalmazás hozzáadása

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. Használhatja a Microsoft által biztosított, harmadik felek által biztosított vagy saját maga által kifejlesztett alkalmazásokat. További információ: [Külső gyártótól származó Apache Hadoop-alkalmazások telepítése az Azure HDInsight szolgáltatásban.](hdinsight-apps-install-applications.md)

A HDInsight-alkalmazások többsége egy üres peremhálózati csomópontra van telepítve.  Az üres peremhálózati csomópont egy Linux virtuális gép, amely ugyanazokat az ügyféleszközöket telepíti és konfigurálja, mint a fő csomópontban. A peremhálózati csomópont segítségével elérheti a fürtöt, tesztelheti az ügyfélalkalmazásokat, és üzemeltetheti az ügyfélalkalmazásokat. További információ: [Üres peremhálózati csomópontok használata a HDInsightban.](hdinsight-apps-use-edge-node.md)

### <a name="script-actions"></a>Parancsfájl-műveletek

A létrehozás során további összetevőket telepíthet, vagy parancsfájlok használatával testreszabhatja a fürtkonfigurációt. Az ilyen parancsfájlok meghívása **script action**, amely egy konfigurációs beállítás, amely használható az Azure Portalon, HDInsight Windows PowerShell-parancsmagok, vagy a HDInsight .NET SDK. További információt a [HDInsight-fürt testreszabása parancsfájlművelettel című témakörben talál.](hdinsight-hadoop-customize-cluster-linux.md)

Egyes natív Java-összetevők, például az Apache Mahout és a Cascading, Java Archive (JAR) fájlként futtathatók a fürtön. Ezek a JAR-fájlok terjeszthetők az Azure Storage-ba, és elküldhetők a HDInsight-fürtöknek a Hadoop feladatbeküldési mechanizmusokkal. További információ: [Submit Apache Hadoop jobs programmatically](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Ha problémái vannak a JAR-fájlok HDInsight-fürtökbe való telepítésével vagy a JAR-fájlok HDInsight-fürtökön történő hívásával, forduljon a [Microsoft támogatási szolgálatához.](https://azure.microsoft.com/support/options/)
>
> A HDInsight nem támogatja a kaszkádolást, és nem jogosult a Microsoft támogatására. A támogatott összetevők listáját a [HDInsight által biztosított fürtverziók újdonságai című témakörben található.](hdinsight-component-versioning.md)

Néha a létrehozási folyamat során a következő konfigurációs fájlokat szeretné konfigurálni:

* clusterIdentity.xml
* core-site.xml
* átjáró.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* struktúra-env.xml
* struktúra-site.xml
* térképpel-telek
* oozie-site.xml
* oozie-env.xml
* vihar-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

További információt a [HDInsight-fürtök testreszabása a Bootstrap használatával című](hdinsight-hadoop-customize-cluster-bootstrap.md)témakörben talál.

## <a name="next-steps"></a>További lépések

* [Fürtlétrehozási hibák elhárítása az Azure HDInsight segítségével](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Mik azok a HDInsight, az Apache Hadoop-ökoszisztéma és a Hadoop-fürtök?](hadoop/apache-hadoop-introduction.md)
* [Az Apache Hadoop használatának első lépései a HDInsightban](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Munka az Apache Hadoop-ban a HDInsight-on Windows rendszerű számítógépről](hdinsight-hadoop-windows-tools.md)
