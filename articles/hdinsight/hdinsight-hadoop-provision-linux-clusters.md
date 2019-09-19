---
title: Fürt beállítása Apache Hadoop, Spark, Kafka, HBase vagy R Server – Azure
description: Hadoop-, Kafka-, Spark-, HBase-, R Server-vagy Storm-fürtöket állíthat be a HDInsight böngészőből, a klasszikus Azure CLI-ből, a Azure PowerShell, a REST-ból vagy az SDK-ból.
keywords: Hadoop-fürt beállítása, Kafka-fürt beállítása, Spark-fürt beállítása, mi a fürt a Hadoop-ben
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: cf276f3a0b14658d6c0bc10a138e814f30561cc9
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104505"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Fürtök beállítása a HDInsight-ben Apache Hadoop, Apache Spark, Apache Kafka és más szolgáltatásokkal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Megtudhatja, hogyan állíthatja be és konfigurálhatja a HDInsight-fürtöket a Apache Hadoop, a Apache Spark, a Apache Kafka, az interaktív lekérdezés, az Apache HBase, a ML-szolgáltatások vagy a Apache Storm használatával. Azt is megtudhatja, hogyan szabhatja testre a fürtöket, és hogyan adhat hozzá a biztonsághoz egy tartományhoz való csatlakoztatással.

A Hadoop-fürtök több virtuális gépről (csomópontból) állnak, amelyek a feladatok elosztott feldolgozásához használatosak. Az Azure HDInsight az egyes csomópontok telepítésének és konfigurálásának megvalósítási részleteit kezeli, így csak általános konfigurációs adatokat kell megadnia.

> [!IMPORTANT]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. További információ a [fürtök törléséről.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Fürt beállítási módszerei
A következő táblázat a HDInsight-fürtök beállításához használható különböző módszereket mutatja be.

| A-val létrehozott fürtök | Webböngésző | Parancssor | REST API | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Azure Resource Manager sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Gyors létrehozás: Alapszintű fürt beállítása
Ez a cikk végigvezeti a telepítőn a [Azure Portalon](https://portal.azure.com), ahol létrehozhat egy HDInsight-fürtöt a *gyors létrehozás* vagy az *Egyéni*lehetőség használatával.

![hdinsight-létrehozási beállítások egyéni gyors létrehozás](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

Kövesse a képernyőn megjelenő utasításokat egy alapszintű fürt telepítésének elvégzéséhez. A részleteket az alábbi táblázat ismerteti:

* [Erőforráscsoport neve](#resource-group-name)
* [Fürtök típusai és konfigurációja](#cluster-types)
* [Fürt neve](#cluster-name)
* [Fürt bejelentkezési és SSH-felhasználóneve](#cluster-login-and-ssh-username)
* [Location](#location)

## <a name="resource-group-name"></a>Erőforráscsoport neve

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) segít az alkalmazásban lévő erőforrásokkal való munkavégzésben, amely Azure-erőforráscsoport néven is ismert. Az alkalmazás összes erőforrását egyetlen összehangolt műveletben telepítheti, frissítheti, figyelheti vagy törölheti.

## <a name="cluster-types"></a>Fürtök típusai és konfigurációja
Az Azure HDInsight jelenleg a következő típusú fürtöket biztosítja, amelyek mindegyike tartalmaz bizonyos funkciókat biztosító összetevők készletét.

> [!IMPORTANT]  
> A HDInsight-fürtök különböző típusokban érhetők el, amelyek mindegyike egyetlen munkaterheléshez vagy technológiához használható. Nincs támogatott módszer a több típust egyesítő fürt létrehozására, például a Storm és a HBase egy fürtön. Ha a megoldás olyan technológiákat igényel, amelyek több HDInsight között oszlanak el, az Azure-beli [virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network) kapcsolódhatnak a szükséges fürtökhöz.

| Fürt típusa | Funkció |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |A tárolt adatmennyiségek kötegelt lekérdezése és elemzése |
| [HBase](hbase/apache-hbase-overview.md) |Nagy mennyiségű séma nélküli, NoSQL-adatbázis feldolgozása |
| [Interaktív lekérdezés](./interactive-query/apache-interactive-query-get-started.md) |Memóriában történő gyorsítótárazás az interaktív és gyorsabb kaptár-lekérdezésekhez |
| [Kafka](kafka/apache-kafka-introduction.md) | Elosztott streaming platform, amely valós idejű adatfolyam-továbbítási adatcsatornák és alkalmazások létrehozására használható |
| [ML szolgáltatások](r-server/r-server-overview.md) |Különböző big data statisztikák, prediktív modellezés és gépi tanulási képességek |
| [Spark](spark/apache-spark-overview.md) |Memóriabeli feldolgozás, interaktív lekérdezések, mikro-köteg adatfolyam-feldolgozás |
| [Storm](storm/apache-storm-overview.md) |Valós idejű eseményfeldolgozás |

### <a name="hdinsight-version"></a>HDInsight verziója
Válassza ki a fürthöz tartozó HDInsight verzióját. További információ: [támogatott HDInsight-verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="cluster-name"></a>Fürt neve

A HDInsight-fürtök neve a következő korlátozásokkal rendelkezhet:
- Engedélyezett karakterek: a-z, 0-9, A-Z 
- Maximális hossz: 59
- Fenntartott nevek: alkalmazások
- A fürt elnevezési hatóköre az összes Azure-ra kiterjed az összes előfizetésben. Így a fürt nevének egyedinek kell lennie világszerte.
- Az első 6 karakternek egyedinek kell lennie egy VNET belül

## <a name="cluster-login-and-ssh-username"></a>Fürt bejelentkezési és SSH-felhasználóneve
A HDInsight-fürtök esetében két felhasználói fiókot is beállíthat a fürt létrehozása során:

* HTTP-felhasználó: Az alapértelmezett felhasználónév az *admin*. A Azure Portal alapszintű konfigurációját használja. Néha a "fürt felhasználó" néven is ismert.
* SSH-felhasználó: Az SSH-n keresztül a fürthöz való kapcsolódásra szolgál. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A HTTP-Felhasználónév a következő korlátozásokkal rendelkezik:
- Engedélyezett speciális karakterek: _ és @ 
- Nem engedélyezett karakterek: #;. "",\/: "! *? $ (){}[] < > | &--= +% ~ ^ szóköz
- Maximális hossz: 20

Az SSH-Felhasználónév a következő korlátozásokkal rendelkezik:
- Engedélyezett speciális karakterek: _ és @ 
- Nem engedélyezett karakterek: #;. "",\/: "! *? $ (){}[] < > | &--= +% ~ ^ szóköz
- Maximális hossz: 64
- Fenntartott nevek: Hadoop, felhasználók, oozie, kaptár, mapred, ambari-QA, Zookeeper, TEZ, hdfs, sqoop, fonal, hcat, AMS, hbase, Storm, Administrator, admin, user, Felhasználó1, test, Felhasználó2, test1, user3, Rendszergazda1, 1, 123, a, actuser, adm, admin2, ASPNET,, a konzol, David, vendég, János, tulajdonos, gyökér, kiszolgáló, SQL, támogatás, support_388945a0, sys, teszt2, test3, user4, user5, Spark

A nagyvállalati biztonsági csomag lehetővé teszi a HDInsight integrálását Active Directory és Apache Ranger használatával. A vállalati biztonsági csomag használatával több felhasználó is létrehozható.

## <a name="location"></a>A fürtök és a tároló helye (régiói)

Nem kell explicit módon megadnia a fürt helyét: A fürt az alapértelmezett tárolóval megegyező helyen található. A támogatott régiók listájának megtekintéséhez kattintson a **régió** legördülő listára a [HDInsight díjszabásában](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Fürtök tárolási végpontjai

Bár a Hadoop helyszíni telepítése a elosztott fájlrendszer Hadoop (HDFS) használja a fürtön lévő tároláshoz, a felhőben a fürthöz csatlakozó tárolási végpontokat használja. A felhőalapú tárolás eszközzel biztonságosan törölheti a számításhoz használt HDInsight-fürtöket, miközben továbbra is megőrizheti az adatait. 

A HDInsight-fürtök a következő tárolási lehetőségeket használhatják:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage általános célú v2
* Azure Storage általános célú v1
* Azure Storage Block-blob (**csak másodlagos tárolóként támogatott**)

További információ a tárolási lehetőségekről a HDInsight használatával: a [tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használatra](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen található további Storage-fiók használata nem támogatott.

A konfiguráció során az alapértelmezett tárolási végponthoz meg kell adnia egy Azure Storage-fiók vagy Data Lake Storage blob-tárolóját. Az alapértelmezett tároló alkalmazás-és rendszernaplókat tartalmaz. Megadhat további társított Azure Storage-fiókokat és Data Lake Storage fiókokat is, amelyeket a fürt elérhet. A HDInsight-fürtnek és a függő tárolási fiókoknak ugyanabban az Azure-helyen kell lenniük.

![Fürt tárolási beállításai: HDFS-kompatibilis tárolási végpontok](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Opcionális metaadattárak

Létrehozhat opcionális struktúrát vagy Apache Oozie metaadattárak is. Azonban nem minden típusú fürt támogatja a metaadattárak-t, és Azure SQL Data Warehouse nem kompatibilis a metaadattárak-mel.

További információ: [külső metaadat-tárolók használata az Azure HDInsight-ben](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Egyéni metaadattár létrehozásakor ne használjon kötőjeleket, kötőjeleket és szóközöket az adatbázis nevében. Ennek hatására a fürt létrehozási folyamata sikertelen lehet.

### <a name="use-hiveoozie-metastore"></a>Hive-metaadattár

Ha a HDInsight-fürt törlése után szeretné megőrizni a kaptár-táblákat, egyéni metaadattár használjon. Ezután a metaadattár egy másik HDInsight-fürthöz is csatolhatja.

Az egyik HDInsight létrehozott metaadattár nem oszthatók meg különböző HDInsight-fürt verziói között. An méretű HDInsight A HDInsight-verziók listáját lásd: [támogatott HDInsight-verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Oozie metaadattár

A Oozie használatakor a teljesítmény növeléséhez használjon egyéni metaadattár. A metaadattár a fürt törlése után is biztosíthat hozzáférést a Oozie-feladatok adataihoz.

> [!IMPORTANT]  
> Egyéni Oozie-metaadattár nem használható fel újra. Ha egyéni Oozie-metaadattár szeretne használni, üres Azure SQL Database kell megadnia a HDInsight-fürt létrehozásakor.

## <a name="custom-cluster-setup"></a>Egyéni fürt beállítása
Az egyéni fürt beállítása a gyors létrehozási beállításokon alapul, és a következő beállításokat adja meg:
- [Vállalati biztonsági csomag](#enterprise-security-package)
- [HDInsight-alkalmazások](#install-hdinsight-applications-on-clusters)
- [Fürt mérete](#configure-cluster-size)
- [Parancsfájlok műveletei](#advanced-settings-script-actions)
- [Virtuális hálózat](#advanced-settings-extend-clusters-with-a-virtual-network)

## <a name="enterprise-security-package"></a>Vállalati biztonsági csomag

A Hadoop, a Spark, a HBase, a Kafka és az interaktív lekérdezési fürt típusainál engedélyezheti a **Enterprise Security Package**. Ez a csomag lehetővé teszi, hogy az Apache Ranger használatával biztonságosabb fürtöt lehessen beállítani, és integrálja Azure Active Directory. További információ: [a vállalati biztonság áttekintése az Azure HDInsight-ben](./domain-joined/hdinsight-security-overview.md).

![hdinsight-létrehozási beállítások válassza a vállalati biztonsági csomag lehetőséget](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

A tartományhoz csatlakoztatott HDInsight-fürtök létrehozásával kapcsolatos további információkért lásd: [tartományhoz csatlakoztatott HDInsight homokozó környezet létrehozása](./domain-joined/apache-domain-joined-configure.md).

## <a name="install-hdinsight-applications-on-clusters"></a>HDInsight-alkalmazások telepítése fürtökön

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. Használhatja a Microsoft vagy harmadik felek által biztosított alkalmazásokat, illetve saját maga is fejlesztheti azokat. További információ: [külső féltől származó Apache Hadoop alkalmazások telepítése az Azure HDInsight](hdinsight-apps-install-applications.md).

A HDInsight alkalmazások többsége üres peremhálózati csomópontra van telepítve.  Az üres peremhálózati csomópont egy Linux rendszerű virtuális gép, amelyen ugyanaz az ügyféleszközök van telepítve és konfigurálva, mint a fő csomópontban. Használhatja a peremhálózati csomópontot a fürt eléréséhez, az ügyfélalkalmazások teszteléséhez és az ügyfélalkalmazások üzemeltetéséhez. További információ: [üres peremhálózati csomópontok használata a HDInsight-ben](hdinsight-apps-use-edge-node.md).

## <a name="configure-cluster-size"></a>Fürt méretének konfigurálása

A csomópont-használat számlázása addig történik, amíg a fürt létezik. A számlázás akkor kezdődik, amikor létrejön egy fürt, és leáll, amikor a fürt törlődik. A fürtöket nem lehet kiosztani vagy lefoglalni.

### <a name="number-of-nodes-for-each-cluster-type"></a>Csomópontok száma az egyes fürtök típusainál

Mindegyik fürt típusa saját számú csomóponttal, csomópontok terminológiával és alapértelmezett virtuálisgép-mérettel rendelkezik. Az alábbi táblázatban az egyes csomópont-típusok csomópontjainak száma zárójelben van.

| Type | Csomópontok | Diagram |
| --- | --- | --- |
| Hadoop |Fő csomópont (2), munkavégző csomópont (1 +) |![HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Head Server (2), régió-kiszolgáló (1 +), fő/ZooKeeper csomópont (3) |![HDInsight HBase-fürt típusának beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus csomópont (2), felügyeleti kiszolgáló (1 +), ZooKeeper csomópont (3) |![HDInsight Storm-fürt típusának beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Fő csomópont (2), munkavégző csomópont (1 +), ZooKeeper-csomópont (3) (az A1-es ZooKeeper VM-mérethez ingyenes) |![HDInsight Spark-fürt típusának beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

További információ: az [alapértelmezett csomópont-konfiguráció és a fürtök virtuálisgép-mérete](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) a "mi a Hadoop-összetevők és-verziók a HDInsight-ben?"

A HDInsight-fürtök költségeit a csomópontok száma és a csomópontok virtuális gépei mérete határozza meg.

A különböző típusú fürtök eltérő csomópont-típusokkal, csomópont-számokkal és csomópont-méretekkel rendelkeznek:
* Hadoop-fürt típusának alapértelmezett értéke:
    * Két *fő csomópont*  
    * Négy *munkavégző csomópont*
* Storm-fürt típusa alapértelmezett:
    * Két *Nimbus-csomópont*
    * Három *ZooKeeper-csomópont*
    * Négy *felügyeleti csomópont*

Ha csak kipróbálja a HDInsight, javasoljuk, hogy használjon egy feldolgozói csomópontot. További információ a HDInsight díjszabásáról: [HDInsight díjszabása](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> A fürt méretének korlátozása az Azure-előfizetések között változhat. A korlát növeléséhez vegye fel a kapcsolatot az [Azure számlázási támogatási szolgálatával](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) .

Ha a Azure Portal a fürt konfigurálására használja, a csomópont mérete a csomópontok díjszabási **szintjei** panelen érhető el. A portálon megtekintheti a különböző csomópont-méretekhez kapcsolódó költségeket is.

![HDInsight válassza ki a csomópont méretét](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek 

Fürtök telepítésekor a telepítendő megoldás alapján válassza a számítási erőforrások lehetőséget. A következő virtuális gépek használatosak a HDInsight-fürtökhöz:
* A és D1-4 sorozatú virtuális gépek: [Általános célú linuxos virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14 sorozatú virtuális gép: [Memóriára optimalizált linuxos virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Annak megállapításához, hogy milyen értéket kell használnia a virtuális gép méretének megadásához a különböző SDK-k használatával vagy a Azure PowerShell használata közben, lásd: [HDInsight-fürtökhöz használandó](../cloud-services/cloud-services-sizes-specs.md#size-tables)virtuálisgép-méretek. Ebből a csatolt cikkből a táblázatok **méret** oszlopában található értéket használja.

> [!IMPORTANT]  
> Ha a fürtben több mint 32 feldolgozó csomópontra van szüksége, ki kell választania egy fő csomópont-méretet legalább 8 maggal és 14 GB RAM-mal.

További információkért lásd: [virtuális gépek méretei](../virtual-machines/windows/sizes.md). További információ a különböző méretek díjszabásáról: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight).

## <a name="advanced-settings-script-actions"></a>Speciális beállítások: Szkriptműveletek

További összetevőket is telepíthet, vagy testre szabhatja a fürt konfigurációját parancsfájlok használatával a létrehozás során. Az ilyen parancsfájlokat parancsfájl- **művelettel**hívja meg, amely a Azure Portal, a HDInsight Windows PowerShell-parancsmagok vagy a HDINSIGHT .net SDK használatával használható konfigurációs lehetőség. További információ: a [HDInsight-fürt testreszabása parancsfájl](hdinsight-hadoop-customize-cluster-linux.md)-művelettel.

Egyes natív Java-összetevők, például az Apache Mahout és a kaszkádolt, Java Archive (JAR) fájlokként futtathatók a fürtön. Ezek a JAR-fájlok eloszthatók az Azure Storage-ba, és a Hadoop-feladatok beküldési mechanizmusaival HDInsight-fürtökbe elküldve További információt a [Apache Hadoop feladatok programozott beküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)című témakörben talál.

> [!NOTE]  
> Ha problémába ütközik a JAR-fájlok HDInsight-fürtökre való telepítésekor vagy JAR-fájlok HDInsight-fürtökön való meghívásakor, forduljon a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).
>
> A HDInsight nem támogatja a kaszkádolt használatát, és nem jogosult Microsoft ügyfélszolgálatara. A támogatott összetevők listáját a [HDInsight által biztosított fürtcsomópontok újdonságai](hdinsight-component-versioning.md)című témakörben tekintheti meg.

Időnként a következő konfigurációs fájlokat szeretné konfigurálni a létrehozási folyamat során:

* clusterIdentity.xml
* Core-site. XML
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – hely
* oozie-site. XML
* oozie-env.xml
* Storm-site. XML
* tez-site.xml
* webhcat-site.xml
* yarn-site. XML

További információ: HDInsight- [fürtök testreszabása a bootstrap használatával](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Speciális beállítások: Fürtök kiterjesztése virtuális hálózattal

Ha a megoldás olyan technológiákat igényel, amelyek több HDInsight között oszlanak el, az Azure-beli [virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network) kapcsolódhatnak a szükséges fürtökhöz. Ez a konfiguráció lehetővé teszi, hogy a fürtök és a rájuk telepített kódok közvetlenül kommunikálhassanak egymással.

További információ az Azure-beli virtuális hálózatok HDInsight használatával történő használatáról: [virtuális hálózat megtervezése HDInsight számára](hdinsight-plan-virtual-network-deployment.md).

Az Azure-beli virtuális hálózaton belüli két fürt típusának használatával kapcsolatban lásd: [Apache Spark strukturált adatfolyam használata Apache Kafka használatával](hdinsight-apache-kafka-spark-structured-streaming.md). További információ a HDInsight virtuális hálózattal történő használatáról, beleértve a virtuális hálózat speciális konfigurációs követelményeit: [virtuális hálózat megtervezése HDInsight számára](hdinsight-plan-virtual-network-deployment.md).

## <a name="next-steps"></a>További lépések

- [Fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
- [Mik azok a HDInsight, a Apache Hadoop ökoszisztémák és a Hadoop-fürtök?](hadoop/apache-hadoop-introduction.md)
- [A Apache Hadoop használatának első lépései a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Munkahelyi Apache Hadoop HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL](hdinsight-hadoop-windows-tools.md)
