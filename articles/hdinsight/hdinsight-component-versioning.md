---
title: Összetevők és verziók Apache Hadoop – Azure HDInsight
description: Ismerje meg az Azure HDInsight Apache Hadoop összetevőit és verzióit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/09/2020
ms.openlocfilehash: e27b65346ba8d48e36d080d66754db9605259e2b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144406"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Milyen Apache Hadoop-összetevők és-verziók érhetők el a HDInsight-ben?

Ismerkedjen meg a [Apache Hadoop](https://hadoop.apache.org/) környezeti összetevőkkel és verziókkal Microsoft Azure HDInsight és a Enterprise Security Package. Azt is megtudhatja, hogyan tekintheti meg a Hadoop összetevő-verzióit a HDInsight-ben.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop különböző HDInsight-verziókkal elérhető összetevők

Az Azure HDInsight több Hadoop-fürtöt is támogat, amelyek bármikor üzembe helyezhetők. 2017. április 4-én az Azure HDInsight által használt alapértelmezett fürt verziója 3,6.

A HDInsight-fürtökhöz társított verziók az alábbi táblázatban láthatók:

> [!NOTE]  
> Előfordulhat, hogy a HDInsight szolgáltatás alapértelmezett verziója értesítés nélkül változhat. Ha a verzió-függőséggel rendelkezik, adja meg a HDInsight verzióját, amikor létrehozza a fürtöket a .NET SDK-val a Azure PowerShell és a klasszikus Azure CLI használatával.

| Összetevő              | HDInsight 4.0 | HDInsight 3,6 (alapértelmezett)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop és fonal | 3.1.1         | 2.7.3                       |
| Apache TEZ             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 az ESP interaktív lekérdezésen) |
| Apache TEZ Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (lásd az alábbi megjegyzést) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> A rendszer teljesítményével kapcsolatos megfontolások miatt a Kafka 0,10-es verziójának támogatása 2019 márciusában lejár.

## <a name="check-for-current-hadoop-component-version-information"></a>Az aktuális Hadoop-összetevő verziószámával kapcsolatos információk keresése

A HDInsight-Hadoop társított környezet-összetevők verzióit a HDInsight frissítéseivel lehet megváltoztatni. A Hadoop-összetevők ellenőrzéséhez és a fürthöz használt verziók ellenőrzéséhez használja a Ambari REST API. A **GetComponentInformation** parancs adatokat kér le a szolgáltatás összetevőiről. Részletekért tekintse meg az [Apache Ambari dokumentációját](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Kibocsátási megjegyzések

A HDInsight legújabb verzióiban további kibocsátási megjegyzéseket a [HDInsight kibocsátási megjegyzései](hdinsight-release-notes.md) című témakörben talál.

## <a name="supported-hdinsight-versions"></a>Támogatott HDInsight-verziók

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>A HDInsight-verziók lejáratának és kivonulásának támogatása

A **támogatás lejárata** azt jelenti, hogy a Microsoft többé nem nyújt támogatást a megadott HDInsight-verzióhoz. A továbbiakban nem lesz elérhető az Azure Portal a fürt létrehozásához. Ezek a verziók azonban továbbra is létrehozhatók az Azure CLI-vel vagy a különböző SDK-k használatával.

**A HDInsight** -verziók kivonása azt jelenti, hogy a meglévő fürtök továbbra is futni fognak. Azonban a verzió új fürtök nem hozhatók létre semmilyen módon (beleértve a CLI-t és az SDK-kat is). Az egyéb vezérlési sík funkciók (például a manuális skálázás és az automatikus skálázás) a nyugdíjazás után is nem működnek. A támogatás nem érhető el a kivont verziókhoz.

A következő táblázat a HDInsight verzióját sorolja fel. A támogatási lejárati és a nyugdíjazási dátumok is megadhatók, ha ismertek.

### <a name="available-versions"></a>Elérhető verziók

A következő táblázat felsorolja azokat a HDInsight-verziókat, amelyek elérhetők a Azure Portal és egyéb központi telepítési módszerekben, például a PowerShell és a .NET SDK.

| HDInsight verziója | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |  Rendelkezésre állás a Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24, 2018. szeptember | | |Igen |Igen |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |2017. április 4. | December 31., 2020 |December 31., 2020 |Igen |Igen |

A Spark 2,1, 2,2 & Kafka 1,0 támogatásának érvényessége 2020. június 30-án lejár.

> [!NOTE]  
> A verzió támogatásának lejártakor előfordulhat, hogy nem érhető el a Microsoft Azure Portalon keresztül. A fürt verziói azonban továbbra is elérhetők a `Version` Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsában és a .net SDK-ban található paraméterrel, amíg meg nem történik a verzió kivonulási dátuma.

### <a name="retired-versions"></a>Kivont verziók

A következő táblázat felsorolja azokat a HDInsight-verziókat, amelyek **nem** érhetők el a Azure Portalban.

| HDInsight verziója | HDP verziója | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |  Rendelkezésre állás a Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |Szeptember 30., 2016 |Szeptember 5., 2017 |Június 28., 2018 |Igen |Nem |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |Március 29., 2016 |December 29., 2016 |2018. január 9. |Igen |Nem |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2015. december 2. |2016. június 27. |2018. július 31. |Igen |Nem |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2015. december 2. |2016. június 27. |2017. július 31. |Igen |Nem |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS vagy Windows Server 2012 R2 |Február 18., 2015 |2016. március 1. |2017. április 1. |Igen |Nem |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 R2 |Június 24., 2014 |2015. május 18. |Június 30., 2016 |Igen |Nem |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 R2 |Február 11., 2014 |Szeptember 17., 2014 |2015. június 30. |Igen |Nem |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |Október 28., 2013 |2014. május 12. |2015. május 31. |Igen |Nem |
| HDInsight 1,6 |HDP 1,1 | |Október 28., 2013 |Április 26., 2014 |2015. május 31. |Nem |Nem |

> [!NOTE]  
> A HDInsight 2,1-es és újabb verzióiban a két fő csomóponttal rendelkező, magasan elérhető fürtök üzembe helyezése alapértelmezés szerint megtörténik. A HDInsight 1,6-es verziójú fürtök esetében nem érhetők el.

## <a name="enterprise-security-package-for-hdinsight"></a>HDInsight Enterprise Security Package

A vállalati biztonság egy opcionális csomag, amelyet hozzáadhat a HDInsight-fürthöz a fürt létrehozása munkafolyamat részeként. A Enterprise Security Package a következőket támogatja:

- Active Directory integrációja a hitelesítéshez.

    Korábban létrehozta a HDInsight-fürtöket a helyi rendszergazda felhasználóval és a helyi SSH-felhasználóval. A helyi rendszergazda felhasználó elérheti az összes fájlt, mappát, táblát és oszlopot.  A Enterprise Security Package lehetővé teszi a szerepköralapú hozzáférés-vezérlést a HDInsight és a Active Directory integrálásával. Ide tartozik a helyszíni Active Directory, Azure Active Directory Domain Services. Vagy Active Directory a IaaS virtuális gépen. A fürt tartományi rendszergazdája engedélyezheti a felhasználóknak, hogy saját vállalati (tartományi) felhasználónevet és jelszót használjanak.

    További információkért lásd:

    - [Bevezetés a Apache Hadoop biztonságba a tartományhoz csatlakoztatott HDInsight-fürtökkel](./domain-joined/hdinsight-security-overview.md)
    - [Azure-tartományhoz csatlakoztatott Apache Hadoop-fürtök tervezése a HDInsight-ben](./domain-joined/apache-domain-joined-architecture.md)
    - [Tartományhoz csatlakoztatott tesztkörnyezet konfigurálása](./domain-joined/apache-domain-joined-configure.md)
    - [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása Azure Active Directory Domain Services használatával](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Adathozzáférés engedélyezése

  - Integráció az Apache Ranger-nal a kaptár, a Spark SQL és a fonalas várólisták engedélyezéséhez.
  - Megadhatja a fájlok és mappák hozzáférés-vezérlését.

    További információkért lásd:

  - [Apache Hive házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight](./domain-joined/apache-domain-joined-run-hive.md)

- Tekintse meg a naplókat a hozzáférések és a konfigurált szabályzatok figyeléséhez.

### <a name="supported-cluster-types"></a>Támogatott fürtök típusai

Jelenleg csak a következő típusú fürtök támogatják a Enterprise Security Package:

- Hadoop (csak HDInsight 3,6)
- Spark
- Kafka
- HBase
- Interaktív lekérdezés

### <a name="support-for-azure-data-lake-storage"></a>Azure Data Lake Storage támogatása

A Enterprise Security Package az elsődleges tárolóként és a kiegészítő tárolóként is támogatja a Azure Data Lake Storage használatát.

### <a name="pricing-and-service-level-agreement-sla"></a>Díjszabás és szolgáltatói szerződés (SLA)

További információ a Enterprise Security Package díjszabásáról és SLA-ról: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight-fürt verzióinak szolgáltatói szerződése

A szolgáltatói szerződés (SLA) _támogatási ablakként_van meghatározva. A támogatási ablak az a időszak, amelyet `Microsoft Customer Service and Support`a HDInsight-verzió támogat. Ha a verziónak átadott _támogatás lejárati dátuma_van, a HDInsight-fürt a támogatási ablakon kívül esik. A HDInsight X verziójának támogatása (újabb X + 1 verzió használata esetén) a későbbi:  

- 1. képlet: 180 nap hozzáadása a HDInsight-fürt X verziójának kiadási dátumához.
- 2. képlet: adja hozzá a 90 nappal azt a dátumot, amikor a HDInsight-fürt X + 1 verziója elérhetővé válik Azure Portalban.

A lejárati _dátum_ az a dátum, amely után a fürt verziója nem hozható létre a HDInsight. 2017. július 31-ig nem lehet átméretezni egy HDInsight-fürtöt a kivonulási dátum után.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>A csomópontok alapértelmezett konfigurációja és a virtuális gépek méretei a fürtökhöz

További információ arról, hogy mely virtuálisgép-SKU-ket szeretné kijelölni a fürt számára: az [Azure HDInsight-fürt konfigurációjának részletei](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>További lépések

- [Fürt beállítása Apache Hadoop, Spark és más HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Munkahelyi Apache Hadoop HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL](hdinsight-hadoop-windows-tools.md)
- [Az Azure HDInsight-verziókhoz tartozó Hortonworks kibocsátási megjegyzések](./hortonworks-release-notes.md)
