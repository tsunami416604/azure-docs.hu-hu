---
title: "A telepítő a Hadoop, Spark, Kafka, HBase vagy R Server - Azure HDInsight fürt |} Microsoft Docs"
description: "Hadoop, Kafka, Spark, HBase, R Server, vagy beállítása Storm-fürtök a HDInsight egy böngészőt, az Azure parancssori felület, Azure PowerShell, REST vagy SDK."
keywords: "hadoop-fürt beállítása, kafka fürtbeállítás, spark-fürt beállítása, mi az hadoop-fürt"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: c0f89f98c26b80e8b71c58fc89ea7ecebe734f71
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-clusters-in-hdinsight-with-hadoop-spark-kafka-and-more"></a>Hdinsight Hadoop, Spark, Kafka és több fürt beállítása

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Megtudhatja, hogyan telepítheti és konfigurálhatja a fürtök hdinsight Hadoop, Spark, Kafka, interaktív lekérdezés, HBase, R Server, vagy a Storm. Emellett útmutató fürtök testreszabása és biztonsági hozzáadása egy tartományhoz való csatlakozás őket.

A Hadoop fürtök több virtuális gépek (csomópontok), a feladatok elosztott feldolgozásához használt áll. Az Azure HDInsight kezeli a megvalósítás részletei telepítési és konfigurációs az egyes csomópontokon, csak meg kell adnia az általános konfigurációs információkat. 

> [!IMPORTANT]
>A HDInsight fürt számlázás követően egy fürt jön létre, és leállítja a fürt törlésekor indul. Számlázási, egyenletesen percenként történik, így mindig törölnie kell a fürthöz, amikor már nincs használatban. Megtudhatja, hogyan [törölheti a fürtöt.](hdinsight-delete-cluster.md)
>

## <a name="cluster-setup-methods"></a>Fürt telepítési módszer
A következő táblázat segítségével állítsa be a HDInsight-fürtök különböző módszereket.

| Létrehozott fürtök | Webböngésző | Parancssor | REST API | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Az Azure Resource Manager-sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Gyorslétrehozás: alapvető fürt beállítása
Ez a cikk végigvezeti a telepítő a [Azure-portálon](https://portal.azure.com), ahol hozhat létre egy HDInsight fürt *Gyorslétrehozás* vagy *egyéni*. 

Kövesse a alapvető fürttelepítés ehhez a képernyőn megjelenő utasításokat. Részletek az alább:

* [Az erőforráscsoport neve](#resource-group-name)
* [Fürt típusa és konfigurálása](#cluster-types) 
* [Fürt bejelentkezési és az SSH-felhasználónév](#cluster-login-and-ssh-username)
* [Hely](#location)

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight 3.3 használatból való kivonást](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a name="resource-group-name"></a>Az erőforráscsoport neve 

[Az Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) egy Azure-erőforráscsoportot néven a munkát az erőforrásokkal egy csoportként az alkalmazásban. Telepítéséhez, frissítéséhez, figyelése, vagy törli az összes erőforrást egyetlen, koordinált műveletben az alkalmazáshoz.

## <a name="cluster-types"></a>Fürt típusa és konfigurálása
Az Azure HDInsight jelenleg a következő fürttípusok, az összetevők arra, hogy bizonyos funkciók biztosít.

> [!IMPORTANT]
> A HDInsight-fürtök különböző egyetlen számítási feladat vagy technológia típusok érhetők el. Nincs támogatott módszer, amely többféle, például a Storm és HBase egy fürtön fürt létrehozásához. Ha a megoldást igényel, amely több HDInsight-fürttípusok, vannak elosztva egy [Azure-beli virtuális hálózat](https://docs.microsoft.com/azure/virtual-network) a szükséges fürttípusok kapcsolódhatnak. 
>
>

| Fürttípus | Funkció |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Kötegelt lekérdezés és a tárolt adatok elemzése |
| [HBase](hbase/apache-hbase-overview.md) |Nagy mennyiségű séma nélküli, nosql típusú adatok számára történő feldolgozásakor. |
| [A Storm](storm/apache-storm-overview.md) |Valós idejű eseményfeldolgozás |
| [Spark](spark/apache-spark-overview.md) |A memórián belüli feldolgozást, interaktív lekérdezések micro-kötegelt adatfolyam feldolgozása |
| [Kafka (előzetes verzió)](kafka/apache-kafka-introduction.md) | A valós idejű streamelési adatok folyamatok és alkalmazások készítéséhez használható elosztott adatfolyam platform |
| [R Server](r-server/r-server-overview.md) |Különböző big Data típusú adatok statisztika, prediktív modellezési és gépi tanulási képességek |
| [Interaktív lekérdezés](./interactive-query/apache-interactive-query-get-started.md) |A memóriában történő gyorsítótárazás gyorsabb és interaktív Hive-lekérdezések |

### <a name="number-of-nodes-for-each-cluster-type"></a>Az egyes fürt a csomópontok száma
Minden egyes fürttípus csomópontok, terminológia csomópontokat, és az alapértelmezett Virtuálisgép-méretet a saját számú tartalmaz. Az alábbi táblázatban az egyes csomópont csomópontok száma zárójelek között van.

| Típus | Csomópontok | Ábra |
| --- | --- | --- |
| Hadoop |Átjárócsomópont (2) adatok csomópont (1 +) |![HDInsight Hadoop-fürt csomópontjain](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |HEAD kiszolgáló (2), a régióban (1 +), fő/ZooKeeper csomópont (3) |![HDInsight HBase fürt csomópontjain](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus csomóponttal (2), felügyeleti kiszolgáló (1 +), ZooKeeper csomópont (3) |![A HDInsight alatt futó Storm-fürt csomópontjain](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Átjárócsomópont (2), munkavégző csomópont (1 +), ZooKeeper csomópont (3) (szabad A1 ZooKeeper Virtuálisgép-méretet a) |![HDInsight Spark-fürt csomópontjain](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

További információkért lásd: [csomópont konfigurációs és virtuális gépek méretei fürtök alapértelmezett](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) a "Mik azok a Hadoop-összetevők és a hdinsight-verziók?"

### <a name="hdinsight-version"></a>HDInsight-verzió
Ehhez a fürthöz HDInsight verziójának kiválasztása. További információkért lásd: [támogatott HDInsight-verziókról](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-tiers"></a>Fürt réteg: HDInsight szolgáltatásszintek

Az Azure HDInsight a big data felhőajánlatokat két szolgáltatásrétegekben biztosít: Standard és Premium.  További információkért lásd: [HDInsight Standard és HDInsight prémium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).

Az alábbi képernyőfelvételen a fürt kiválasztása az Azure portál információit tartalmazza.

![HDInsight prémium konfiguráció](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


## <a name="cluster-login-and-ssh-user-name"></a>A fürt bejelentkezési és az SSH-felhasználónév
HDInsight-fürtökkel fürt létrehozása során két felhasználói fiókokat konfigurálhatja:

* Felhasználói HTTP: az alapértelmezett felhasználónév *admin*. Az alapszintű konfigurációs használja az Azure portálon. Más néven "Fürt felhasználói."
* SSH-felhasználó (Linux-fürtök): a fürtön SSH-n keresztül való kapcsolódáshoz használt. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="location"></a>Fürtök és a tárolási helyét (régió)

Nem kell explicit módon adja meg a fürt helyétől: A fürt van ugyanazon a helyen az alapértelmezett tárolóként. A támogatott régiók felsorolása, kattintson a **régió** legördülő listából válassza ki a [HDInsight árképzési](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Tárolási végpontok fürtök

Bár a Hadoop egy helyszíni telepítését a Hadoop elosztott fájlrendszerrel (HDFS) a fürtön tárhelyet használ, a felhőben használhat fürthöz kapcsolódó tároló végpontok. A HDInsight-fürtök bármelyikével [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) vagy [az Azure Storage blobs](hdinsight-hadoop-use-blob-storage.md). Az Azure Storage vagy a Data Lake Store azt jelenti, hogy biztonságosan törölhető, ugyanakkor megmaradnak helyreállításra a adatok számításhoz használt HDInsight fürtöket. 

> [!WARNING]
> Egy további storage-fiók egy másik helyen található a HDInsight-fürtök használata nem támogatott.

Konfigurálása során az alapértelmezett tároló végpont, adja meg egy Azure Storage-fiókot vagy egy Data Lake Store egy blob-tároló. Az alapértelmezett tároló tartalmazza az alkalmazás- és naplókat. Szükség esetén további csatolt Azure Storage-fiókok és a fürt által elérhető Data Lake Store-fiókok is megadhat. A HDInsight-fürt és a függő storage-fiókok Azure ugyanazon a helyen kell lennie.

![Tárolási beállításai: HDFS-kompatibilis tárolási végpontok](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Nem kötelező a metaadattárakat
Nem kötelező a Hive vagy az Oozie metaadattárakat hozhat létre. Azonban nem minden fürttípus támogatja a metaadattárakat, és Azure SQL Data Warehouse nem kompatibilis a metaadattárakat a. 

> [!IMPORTANT]
> Amikor létrehoz egy egyéni metaadattárhoz, ne használjon kötőjeleket, kötőjeleket vagy szóközöket az adatbázis nevét. Ennek hatására a Fürtlétrehozási folyamat sikertelen lesz.

### <a name="use-hiveoozie-metastore"></a>Hive metaadattárhoz

Ha meg szeretné őrizni a Hive táblák, HDInsight-fürtök törlése után, használjon egy egyéni metaadattárhoz. A metaadattárhoz majd csatolhat egy másik HDInsight-fürthöz.

A HDInsight-fürt különböző verzióin átívelő egy HDInsight metaadattárhoz, létrejön egy HDInsight-fürt verziószáma nem osztható meg. A HDInsight-verziók listáját lásd: [támogatott HDInsight-verziókról](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Oozie metaadattárhoz

Egy egyéni metaadattárhoz segítségével Oozie használata esetén a teljesítmény növelése érdekében. A metaadattárhoz a fürt törlése után is megadhatja Oozie feladat adatainak eléréséhez. 

> [!IMPORTANT]
> Egy egyéni Oozie metaadattárhoz nem használhat újra. Egy egyéni Oozie metaadattárhoz használatához meg kell adnia egy üres Azure SQL Database, a HDInsight-fürt létrehozásakor.

## <a name="configure-cluster-size"></a>Fürt méretének beállítása

Mindaddig, amíg a fürt létezik számlázása a csomópont használatát. Számlázási kezdődik, amikor a fürt jön létre, és leállítja a fürt törlésekor. Fürtök nem vonja lefoglalt és nem helyezhető tartásba.

A HDInsight-fürtök költségét csomópontok és a virtuális gépek a csomópontok száma határozza meg. 

Különböző fürttípussal érhető rendelkezik különböző csomóponttípusok, csomópontot, és a csomópont méretek száma:
* Hadoop-fürt típusa alapértelmezett: 
    * Két *átjárócsomópontokat*  
    * Négy *adatcsomópontokat*
* Storm-fürt típusa alapértelmezett: 
    * Két *Nimbus csomópontot*
    * Három *ZooKeeper csomópontok*
    * Négy *felügyelő csomópontok* 

Ha csak próbálja ki a HDInsight, ajánlott adatok egy csomópont. HDInsight árazással kapcsolatos további információkért lásd: [HDInsight árképzési](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> A fürt méretkorlátot Azure-előfizetések függően változik. Ügyfél [Azure számlázási támogatás segítségét](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) a korlát növeléséhez.
>

Ha az Azure portál segítségével konfigurálja a fürtöt, a csomópont mérete keresztül elérhető a **csomópontok árképzési szintjeinek** panelen. A portálon a költség, a másik csomópont méretek társított is megtekintheti. 

![HDInsight Virtuálisgép-csomópont méretek](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek 
Fürtök telepítésekor válassza ki a számítási erőforrásokat tervez telepíteni a megoldás alapján. A következő virtuális gépek HDInsight-fürthöz is használtak:
* A és a D1-4 adatsorozat virtuális gépeken: [General-purpose Linux Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* VM D11-14 adatsorozat: [memóriaoptimalizált Linux Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Tudja meg értéket által használandó adjon meg egy Virtuálisgép-méretet a különböző SDK-k a fürt létrehozása során, vagy Azure PowerShell használata esetén lásd: [a HDInsight-fürtök esetén használandó Virtuálisgép-méretek](../cloud-services/cloud-services-sizes-specs.md#size-tables). A csatolt cikkből, használja a értéket a **mérete** a táblázatok oszlopát.

> [!IMPORTANT]
> Ha több mint 32 munkavégző csomópontokhoz fürtben, ki kell választania egy átjárócsomóponttal mérete legalább 8 maggal és 14 GB RAM-mal.
>
>

További információkért lásd: [virtuális gépek méretei](../virtual-machines/windows/sizes.md). A különböző méretű árazással kapcsolatos információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="custom-cluster-setup"></a>Egyéni fürt beállítása
A gyors telepítés épít egyéni fürt beállítások létrehozása, és hozzáadja a következő beállításokat:
- [A HDInsight-alkalmazások](#hdinsight-applications)
- [Fürt mérete](#cluster-size)
- Speciális beállítások
  - [A Parancsfájlműveletek](#customize-clusters-using-script-action)
  - [Virtuális hálózat](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>HDInsight-alkalmazások telepítése fürtökön

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. Alkalmazások is használhat, feltéve Microsoft, a harmadik felek vagy, hogy most kialakított, saját magának. További információkért lásd: [külső Hadoop-alkalmazások telepítése Azure hdinsight](hdinsight-apps-install-applications.md).

A HDInsight-alkalmazások többsége egy üres élcsomópontot vannak telepítve.  Egy üres élcsomópontot ügyfél eszközök telepítse és konfigurálja az átjárócsomópont hasonlóan a Linux virtuális gép. A fürt eléréséhez, az ügyfél alkalmazások tesztelése és az ügyfélalkalmazások üzemeltető élcsomópont is használhatja. További információkért lásd: [üres peremhálózati csomópontok használata a Hdinsightban](hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Speciális beállítások: parancsfájl-műveletek

További összetevők, vagy testre szabhatja a fürtkonfiguráció létrehozása során-parancsfájlok használatával. Az ilyen parancsprogramok keresztül kerül meghívásra **parancsfájlművelet**, ami használható az Azure-portálon, a HDInsight a Windows PowerShell-parancsmagokkal vagy a HDInsight .NET SDK konfigurációs beállítás. További információkért lásd: [testreszabása HDInsight-fürtjéhez parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md).

Néhány natív Java-összetevők, például Mahout és kaszkádolás, Java archív (JAR) fájlként futtathatja a fürtön. Ezek a JAR-fájlok Azure Storage terjeszthető és a HDInsight-fürtök Hadoop-feladat elküldése mechanizmusokkal elküldve. További információkért lásd: [nyújt Hadoop feladatok programozott módon](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Ha problémába ütközik JAR-fájlok telepítése a HDInsight-fürtök, vagy hívja a JAR-fájlok a HDInsight-fürtökön, forduljon a [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Egymásra épülő HDInsight nem támogatja, és nincs for Microsoft Support jogosult. Támogatott összetevők listáját lásd: [What's new in HDInsight által biztosított fürt verziók](hdinsight-component-versioning.md).
>
>

Egyes esetekben konfigurálni szeretné a következő konfigurációs fájlokat a létrehozási folyamat során:

* clusterIdentity.xml
* Core-site.xml
* Gateway.XML
* a hbase-env.xml
* a hbase-site.xml
* hdfs-site.xml
* Hive-env.xml
* Hive-site.xml
* mapred-hely
* oozie-site.xml
* oozie-env.xml
* a Storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

További információkért lásd: [testreszabása HDInsight-fürtök használata rendszerindítási](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Speciális beállítások: kiterjesztése a fürtök virtuális hálózattal
Ha a megoldást igényel, amely több HDInsight-fürttípusok, vannak elosztva egy [Azure-beli virtuális hálózat](https://docs.microsoft.com/azure/virtual-network) a szükséges fürttípusok kapcsolódhatnak. Ez a konfiguráció lehetővé teszi, hogy a fürtök, és azokat közvetlenül kommunikálnak egymással telepít kódok.

További információ az Azure virtuális hálózat használatával a hdinsight eszközzel, lásd: [kiterjesztése HDInsight az Azure virtuális hálózatokkal](hdinsight-extend-hadoop-virtual-network.md).

Például egy Azure virtuális hálózaton belül, kétféle fürt használatával, [elemezhet érzékelőadatokat a Storm és HBase](storm/apache-storm-sensor-data-analysis.md). HDInsight használatával egy virtuális hálózattal, beleértve a virtuális hálózat, megadott konfigurációs követelményekkel kapcsolatos további információk: [kiterjesztése HDInsight képességek az Azure Virtual Network használatával](hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Hozzáférés-vezérlő elhárítása

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Következő lépések

- [Mik azok a HDInsight, a Hadoop ökoszisztémájának, és a Hadoop-fürtök?](hadoop/apache-hadoop-introduction.md)
- [A Hadoop első lépései a HDInsightban](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Működik a Hadoop on HDInsight from Windows-számítógépek](hdinsight-hadoop-windows-tools.md)
