---
title: A telepítő az Apache Hadoop, az Apache Spark, az Apache Kafka, az Apache HBase vagy az R Server – Azure HDInsight-fürt
description: Beállítani az Hadoop, a Kafka, a Spark, HBase, R Server vagy a Storm-fürtöket a HDInsight egy böngészőt, a klasszikus Azure CLI, Azure PowerShell, REST vagy SDK-t.
keywords: hadoop-fürt beállítása, a kafka-fürt beállítása, a spark-fürt beállítása, mi a hadoop-fürt
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 01/28/2019
ms.openlocfilehash: 0878fc4b069f7c1ca34f8954320af6e69ceea717
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299864"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Az Apache Hadoop, az Apache Spark, az Apache Kafka és több HDInsight-fürtök beállítása

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Megtudhatja, hogyan állíthatja be, és a HDInsight Apache Hadoop, az Apache Spark, az Apache Kafka, interaktív lekérdezés, az Apache HBase, Machine Learning-szolgáltatások vagy az Apache Storm-fürtök konfigurálása. Ezenkívül útmutató-fürtök testre szabása, és adja hozzá a biztonsági egy tartományhoz való csatlakoztatásával.

Hadoop-fürt több virtuális gépek (csomópontok), elosztott feldolgozási feladatok által használt áll. Azure HDInsight megvalósítási részletei telepítési és konfigurációs egyes csomópontjainak kezeli, így csak meg kell adnia az általános konfigurációs adatait. 

> [!IMPORTANT]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ismerje meg, hogyan [fürt törlése.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Fürt telepítési módszer
Az alábbi táblázat a különböző módszerek használatával egy HDInsight-fürt beállítása.

| Létrehozott | Webböngésző | Parancssor | REST API | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Az Azure klasszikus parancssori felület](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Az Azure Resource Manager-sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Gyors létrehozás: Alapszintű fürt beállítása
Ez a cikk végigvezeti a telepítés a [az Azure portal](https://portal.azure.com), ahol létrehozhat egy HDInsight fürt a *Gyorslétrehozás* vagy *egyéni*. 

![hdinsight létrehozása a Gyorslétrehozás egyéni beállítások](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

Kövesse az utasításokat egy alapszintű fürt beállítása ehhez a képernyőn. Részletek az alábbiak:

* [Erőforráscsoport neve](#resource-group-name)
* [Fürttípusokat és konfiguráció](#cluster-types) 
* [Fürt bejelentkezési adatai és SSH-felhasználónév](#cluster-login-and-ssh-username)
* [Hely](#location)

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight 3.3-as kivezetési](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a name="resource-group-name"></a>Erőforráscsoport neve 

[Az Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) Azure-erőforráscsoport néven segítséget nyújt az erőforrásokkal való munka megkezdése egy csoportként az alkalmazásban. Központi telepítése, frissítése, figyelése, vagy törli az összes erőforrást az alkalmazás egyetlen összehangolt művelettel.

## <a name="cluster-types"></a> Fürttípusokat és konfiguráció
Az Azure HDInsight jelenleg a következő fürttípusokat, rendelkező összetevők biztosít bizonyos funkciókat biztosít.

> [!IMPORTANT]  
> HDInsight-fürtök különböző típusainak használatát, minden egyes egyetlen számítási feladat vagy technológia érhető el. Nincs támogatott módszer, amely ötvözi az több típus, például a Storm és a egy fürtön a HBase-fürt létrehozásához. Ha a megoldáshoz szükséges technológiákról, amelyeket több HDInsight-fürttípusok, vannak elosztva egy [az Azure virtual network](https://docs.microsoft.com/azure/virtual-network) csatlakozni tud a szükséges típusokat. 

| Fürttípus | Funkció |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch-lekérdezés és a tárolt adatok elemzése |
| [HBase](hbase/apache-hbase-overview.md) |A nagy mennyiségű séma nélküli, NoSQL-adatok feldolgozása |
| [Interaktív lekérdezés](./interactive-query/apache-interactive-query-get-started.md) |A memóriában történő gyorsítótárazás az interaktív és gyorsabb Hive-lekérdezések |
| [Kafka](kafka/apache-kafka-introduction.md) | Egy elosztott streamelési platform streamadatfolyamatok és alkalmazások készítéséhez használható |
| [Machine Learning-szolgáltatások](r-server/r-server-overview.md) |Különféle big data típusú statisztikai, prediktív modellezési és gépi tanulási funkciókat |
| [Spark](spark/apache-spark-overview.md) |Memórián belüli feldolgozáshoz, interaktív lekérdezések irányuló mikrokötegelt adatfolyam-feldolgozás |
| [Storm](storm/apache-storm-overview.md) |Valós idejű eseményfeldolgozás |


### <a name="hdinsight-version"></a>HDInsight-verzió
Válassza ki a HDInsight fürt verzióját. További információkért lásd: [HDInsight támogatott verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).


## <a name="cluster-login-and-ssh-username"></a>Fürt bejelentkezési adatai és SSH-felhasználónév
A HDInsight-fürtökkel a fürt létrehozásakor két felhasználói fiókot konfigurálhat:

* A HTTP-felhasználó: Az alapértelmezett felhasználónév az *admin*. Az alapkonfiguráció használ az Azure Portalon. Más néven "Fürt a felhasználó."
* SSH-felhasználót: Csatlakozhat a fürthöz ssh-n keresztül. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A vállalati biztonsági csomag lehetővé teszi, hogy a HDInsight integrálható az Active Directory és az Apache Ranger. Több felhasználó is létrehozható, a vállalati biztonsági csomag használatával.

## <a name="location"></a>Fürtök és tárolási hely (régió)

Nem kell explicit módon adja meg a fürt helye: A fürt van ugyanazon a helyen az alapértelmezett tárolóként. Kattintson a támogatott régiók listáját a **régió** legördülő listából válassza ki a [HDInsight díjszabása](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Tárolási végpontok fürtök

Bár a Hadoop helyszíni telepítve a Hadoop elosztott fájlrendszer (HDFS) használ a tároló a fürtre, a felhőben használhatja tárolási végpontok fürthöz csatlakozik. HDInsight-fürtök bármelyikkel [Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md) vagy [az Azure Storage-blobok](hdinsight-hadoop-use-blob-storage.md). Az Azure Storage vagy a Data Lake Storage azt jelenti, hogy biztonságosan törölheti a továbbra is az adatok megőrzése törölje a számításhoz használt HDInsight fürtöket. 

> [!WARNING]  
> A HDInsight-fürtről egy másik helyen található egy tárfiókot használ nem támogatott.

Konfigurálása során az alapértelmezett tároló végpontjaként, adja meg az Azure Storage-fiók vagy a Data Lake Storage blobtárolójára. Az alapértelmezett tárolóként tartalmazza az alkalmazás- és naplókat. Szükség esetén további társított Azure Storage-fiókok és a fürt által elérhető Data Lake Storage-fiókok is megadhat. A HDInsight-fürt és a függő tárfiókot ugyanazon Azure-beli helyen kell lennie.

![Fürt-tárolási beállítások: HDFS-kompatibilis tárolási végpontok](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Nem kötelező metaadattárak
Nem kötelező Hive- vagy Apache Oozie metaadattárakat hozhat létre. Azonban nem minden fürttípus támogatja metaadattárakat, és az Azure SQL Data Warehouse nem kompatibilis az metaadattárak. 

További információkért lásd: [külső metaadat-tárolók az Azure HDInsight használata](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Amikor létrehoz egy egyéni metaadattár, ne használjon kötőjeleket tartalmazhat, kötőjeleket vagy szóközöket az adatbázis nevét. Emiatt a Fürtlétrehozási folyamat sikertelen lesz.

### <a name="use-hiveoozie-metastore"></a>Hive-metaadattár

Ha szeretné megőrizni a Hive-táblákat, egy HDInsight-fürt törlése után, használja az egyéni metaadattár. A metaadattár majd csatlakoztathat egy másik HDInsight-fürtöt.

Egy HDInsight-metaadattár egy HDInsight-fürt verziója számára létrehozott HDInsight-fürt különböző verzióin átívelő nem oszthatók meg. HDInsight-verziók listáját lásd: [HDInsight támogatott verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Oozie-metaadattár

Az Oozie használata a teljesítmény növelése érdekében használja az egyéni metaadattár. A metaadattár a fürt törlése után is megadhatja az Oozie feladat adatokhoz való hozzáférést. 

> [!IMPORTANT]  
> Egyéni Oozie-metaadattár nem használhat újra. Egyéni Oozie-metaadattár használatához meg kell adnia egy üres Azure SQL Database, a HDInsight-fürt létrehozásakor.


## <a name="custom-cluster-setup"></a>Egyéni fürt beállítása
Egyéni fürterőforrás telepítő buildelési a gyors üzembe beállítások létrehozása, és hozzáadja a következő beállításokat:
- [Vállalati biztonsági csomag](#enterprise-security-package)
- [HDInsight-alkalmazások](#install-hdinsight-applications-on-clusters)
- [Fürt mérete](#configure-cluster-size)
- [A Parancsfájlműveletek](#advanced-settings-script-actions)
- [Virtuális hálózat](#advanced-settings-extend-clusters-with-a-virtual-network)
 
## <a name="enterprise-security-package"></a>Vállalati biztonsági csomag

Hadoop, Spark, HBase, a Kafka és interaktív lekérdezési fürt esetében lehet váltani, engedélyezze a **vállalati biztonsági csomag**. Ez a csomag biztosít a biztonságosabb a fürttelepítés Apache Ranger segítségével, illetve integrálása az Azure Active Directoryval opciót. További információkért lásd: [az Azure HDInsight vállalati biztonsági csomag](./domain-joined/apache-domain-joined-introduction.md).

![hdinsight létrehozása a beállítások vállalati biztonsági csomag kiválasztása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

További információ a tartományhoz csatlakoztatott HDInsight létrehozása a fürt, lásd: [létrehozás tartományhoz csatlakoztatott HDInsight próbakörnyezetben](./domain-joined/apache-domain-joined-configure.md). 

## <a name="install-hdinsight-applications-on-clusters"></a>HDInsight-alkalmazások telepítése fürtökön

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. A Microsoft, külső felek vagy, amelyek, fejlesztése, saját magának a megadott alkalmazások használhatja. További információkért lásd: [külső Apache Hadoop-alkalmazások telepítése az Azure HDInsight](hdinsight-apps-install-applications.md).

A HDInsight-alkalmazások többsége üres élcsomópontot van telepítve.  Üres élcsomópontot ügyfél eszközök telepítését és konfigurálását a fő csomópontot mint Linuxos virtuális gép. A peremhálózati csomópont a fürthöz hozzáférő, az ügyfél alkalmazásokat kíván tesztelni, és az ügyfélalkalmazásokat üzemeltető is használhatja. További információkért lásd: [üres élcsomópontok használata a HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="configure-cluster-size"></a>Fürt méretének konfigurálása

Mindaddig, amíg a fürt létezik számlázása a csomópont használatra. A számlázás akkor kezdődik, amikor egy fürt jön létre, és leállítja a fürt törlésekor. Fürtök nem fel lesznek szabadítva, és nem helyezhető tartásba.

### <a name="number-of-nodes-for-each-cluster-type"></a>Mindegyik fürttípus csomópontok száma
Mindegyik fürttípus rendelkezik a saját csomópontok, a csomópontok és a virtuális gépek alapértelmezett mérete terminológiája száma. Az alábbi táblázatban az egyes csomóponttípusok csomópontok számát zárójelek között van.

| Typo | Csomópontok | Ábra |
| --- | --- | --- |
| Hadoop |A fő csomópont (2), adatcsomópont (1 +) |![HDInsight Hadoop-fürt csomópontjain](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |A fő kiszolgáló (2), régióbeli kiszolgálók (1 +), mester/ZooKeeper-csomópont (3) |![HDInsight HBase-fürt csomópontjain](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus csomóponttal (2) felügyeleti kiszolgáló (1 +), ZooKeeper-csomópont (3) |![HDInsight Storm-fürt csomópontjain](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |A fő csomópont (2), munkavégző csomópont (1 +), ZooKeeper-csomópont (3) (a1-es ZooKeeper Virtuálisgép-méret ingyenes) |![HDInsight Spark-fürt csomópontjain](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

További információkért lásd: [csomópont konfigurációs és virtuális gépek méretei fürtök alapértelmezett](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) a "Mik azok a Hadoop-összetevők és verziók a HDInsight?"

A HDInsight-fürtök költségének csomópontok és a virtuális gépek méretei a csomópontok száma határozza meg. 

Különböző fürttípusok különböző típusok, csomópontot, és a csomópontok méretei számok rendelkezik:
* Hadoop-fürt típusa alapértelmezett: 
    * Két *átjárócsomópontokhoz*  
    * Négy *adatcsomópontok*
* Storm-fürt típusa alapértelmezett: 
    * Két *Nimbus-csomópontok*
    * Három *ZooKeeper-csomópontok*
    * Négy *felügyeleti csomópontok* 

Próbálja ki a HDInsight csak, azt javasoljuk egy adatcsomópont használja. HDInsight díjszabással kapcsolatos további információkért lásd: [HDInsight díjszabása](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> A fürt méretének korlátja az Azure-előfizetések között változik. Kapcsolattartó [Azure számlázási ügyfélszolgálatát](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) korlát növelését.
>

Az Azure portal használatával konfigurálja a fürtöt, amikor a csomópont méretét érhető el – a **csomópont díjcsomagokat** panelen. A portálon a másik csomópont méretek kapcsolódó költségek is láthatja. 

![HDInsight VM csomópontok méretei](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek 
Fürt központi telepítése esetén válassza ki a számítási erőforrásokat tervez üzembe helyezni, a megoldás alapján. HDInsight-fürtök a következő virtuális gépek használják:
* A és a D1-4-sorozat virtuális gépei: [Általános célú Linuxos Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14-es sorozatú virtuális gépek: [A memóriaoptimalizált Linux Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Ismerje meg, mi az értékre kell használatával adja meg a virtuális gép méretét a különböző SDK-k használatával fürt létrehozása közben, vagy során Azure PowerShell-lel, tekintse meg [Virtuálisgép-méretek használata a HDInsight-fürtök](../cloud-services/cloud-services-sizes-specs.md#size-tables). Az értéket használja a hivatkozott cikk a **mérete** a táblázatok oszlopát.

> [!IMPORTANT]  
> Ha egy fürtben több mint 32 feldolgozó csomópontokat, jelöljön ki egy fő csomópontméretet legalább 8 maggal és 14 GB RAM.
>
>

További információkért lásd: [virtuális gépek méretei](../virtual-machines/windows/sizes.md). A különböző fürtméretekkel járó díjszabással kapcsolatos információkért lásd: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="advanced-settings-script-actions"></a>Speciális beállítások: Szkriptműveletek

További összetevők, vagy testre szabhatja a fürtkonfiguráció létrehozása során-parancsfájlok használatával. Az ilyen parancsprogramok keresztül kerül meghívásra **parancsfájlművelet**, azaz olyan konfigurációs beállítást, az Azure Portalon, a HDInsight Windows PowerShell-parancsmagok vagy a HDInsight .NET SDK használható. További információkért lásd: [Szkriptműveletek használatával testre szabhatja a HDInsight-fürt](hdinsight-hadoop-customize-cluster-linux.md).

Néhány natív Java-összetevők, például az Apache Mahout és kaszkádolás, a Java-archívum (JAR) fájlok formájában futtathatja a fürtön. A JAR-fájlok az Azure Storage terjeszthető és elküldve a HDInsight-fürtök Hadoop-feladat beküldése jelentésére. További információkért lásd: [küldje el az Apache Hadoop-feladatok programozott módon](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Ha problémákba ütközik a HDInsight-fürtök üzembe helyezése a JAR-fájlok, vagy meghívásakor JAR-fájlok a HDInsight-fürtökön, forduljon [Support](https://azure.microsoft.com/support/options/).
>
> Lépcsőzetes HDInsight által nem támogatott és nem jogosult a Microsoft Support. Támogatott összetevők listáját lásd: [a HDInsight által biztosított fürtverziók újdonságai](hdinsight-component-versioning.md).
>
>

Egyes esetekben szeretné a létrehozási folyamat során a következő konfigurációs fájlok konfigurálása:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-hely
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

További információkért lásd: [Bootstrap használatával testre szabhatja a HDInsight-fürtök](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Speciális beállítások: Fürtök és a virtuális hálózat kiterjesztése
Ha a megoldáshoz szükséges technológiákról, amelyeket több HDInsight-fürttípusok, vannak elosztva egy [az Azure virtual network](https://docs.microsoft.com/azure/virtual-network) csatlakozni tud a szükséges típusokat. Ez a konfiguráció lehetővé teszi, hogy a fürtöket, és bármilyen kód telepít őket közvetlenül kommunikálni egymással.

Az Azure-beli virtuális hálózathoz használata a HDInsight további információkért lásd: [HDInsight kiterjesztése az Azure virtual Network szolgáltatással](hdinsight-extend-hadoop-virtual-network.md).

Az Azure virtuális hálózatban két fürttípusok használatának példájáért lásd [használata Apache Spark strukturált Stream az Apache kafka platformmal](hdinsight-apache-kafka-spark-structured-streaming.md). HDInsight használatával egy virtuális hálózathoz, beleértve a virtuális hálózathoz megadott konfigurációs követelményekkel kapcsolatos további információk: [HDInsight kiterjesztése képességek az Azure Virtual Network használatával](hdinsight-extend-hadoop-virtual-network.md).


## <a name="next-steps"></a>További lépések

- [Mi a HDInsight, az Apache Hadoop-ökoszisztéma és Hadoop-fürtök?](hadoop/apache-hadoop-introduction.md)
- [A HDInsight Apache Hadoop használatának első lépései](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Apache Hadoop on HDInsight, a Windows PC-s verziójában](hdinsight-hadoop-windows-tools.md)
