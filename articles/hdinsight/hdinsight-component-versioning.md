---
title: Apache Hadoop-összetevők és -verziók – Azure HDInsight
description: Ismerje meg az Apache Hadoop összetevőit és verzióit az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 337d36d8f5e7a7b69b98de2f68ff372d4914f905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084770"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Milyen apache Hadoop-összetevők és -verziók érhetők el a HDInsight segítségével?

Ismerje meg az [Apache Hadoop](https://hadoop.apache.org/) környezet összetevőit és verzióit a Microsoft Azure HDInsightban és az Enterprise Security Package csomagban. Azt is megtudhatja, hogyan ellenőrizheti a Hadoop-összetevő-verziókat a HDInsightban.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Az Apache Hadoop összetevői különböző HDInsight-verziókkal érhetők el

Az Azure HDInsight több Hadoop-fürtverziót is támogat, amelyek bármikor telepíthetők. 2017. április 4-én az Azure HDInsight által használt alapértelmezett fürtverzió a 3.6.

A HDInsight-fürtverzióihoz társított összetevő-verziók az alábbi táblázatban találhatók:

> [!NOTE]  
> A HDInsight-szolgáltatás alapértelmezett verziója előzetes értesítés nélkül változhat. Ha verziófüggő, adja meg a HDInsight-verziót, amikor létrehozza a fürtöket a .NET SDK-val az Azure PowerShell és az Azure Classic CLI használatával.

| Összetevő              | HDInsight 4.0 | HDInsight 3.6 (alapértelmezett)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop és YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache sertés             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0 (nem ESP-fürtök), 1.2.1 (ESP-fürtök)                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apacs Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Azaknak a Mahout-ja          | -             | 0,9,0+                      |
| Apache Főnix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (lásd az alábbi megjegyzést) |
| Az Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> A rendszer teljesítményével kapcsolatos megfontolások miatt a Kafka 0.10-es verziójának támogatása 2019 márciusában lejárt.

## <a name="check-for-current-hadoop-component-version-information"></a>A Hadoop-összetevő aktuális verzióadatainak ellenőrzése

A Hadoop-környezet HDInsight-fürtverzióihoz társított összetevő-verziók a HDInsight frissítéseivel módosíthatók. A Hadoop-összetevők ellenőrzéséhez és a fürthöz használt verziók ellenőrzéséhez használja az Ambari REST API-t. A **GetComponentInformation** parancs a szolgáltatásösszetevőkkel kapcsolatos információkat olvassa be. További információt az [Apache Ambari dokumentációjában](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)talál.

### <a name="release-notes"></a>Kibocsátási megjegyzések

A HDInsight legújabb verzióival kapcsolatos további kiadási megjegyzéseket a [HDInsight](hdinsight-release-notes.md) legújabb verzióival kapcsolatos további kiadási megjegyzések ben láthatja.

## <a name="supported-hdinsight-versions"></a>Támogatott HDInsight-verziók

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Támogatás lejárata és kivonása a HDInsight-verziókhoz

**A támogatás lejárata** azt jelenti, hogy a Microsoft a továbbiakban nem nyújt támogatást a megadott HDInsight-verzióhoz. És a továbbiakban nem lesz elérhető az Azure Portalon keresztül a fürt létrehozása. Ezek a verziók azonban továbbra is létrehozható az Azure CLI vagy a különböző SDK-k használatával.

A HDInsight-verzió **kivonása** azt jelenti, hogy a meglévő fürtök továbbra is a jelenlegi állapotban fognak futni. Ennek a verziónak az új fürtjei azonban nem hozhatók létre semmilyen módon (beleértve a CLI-ket és az SDK-kat). Más vezérlősík-funkciók (például a manuális skálázás és az automatikus skálázás) szintén nem működnek a verzió-kivonás után. A támogatás nem érhető el a kivisszavonult verziókhoz.

Az alábbi táblázatok a HDInsight verzióit sorolják fel. A támogatás lejárati és levonási dátumok is biztosított, ha ők ismertek.

### <a name="available-versions"></a>Elérhető verziók

Az alábbi táblázat a HDInsight azOn verzióját sorolja fel, amelyek az Azure Portalon érhetők el, valamint más telepítési módszereket, például a PowerShellt és a .NET SDK-t.

| HDInsight verzió | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |  Elérhetőség az Azure Portalon |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24, 2018. szeptember | | |Igen |Igen |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017. április 4.April 4, 2017 | 2020. augusztus 25. |2020. augusztus 25. |Igen |Igen |

Spark 2.1, 2.2 & Kafka 1.0 támogatás lejár június 30, 2020.

> [!NOTE]  
> Miután egy verzió támogatása lejárt, előfordulhat, hogy nem érhető el a Microsoft Azure portalon keresztül. A fürtverziók azonban továbbra `Version` is elérhetők a Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parancs és a .NET SDK paraméterhasználatával a verzió kivonási dátumáig.

### <a name="retired-versions"></a>Kivezett verziók

Az alábbi táblázat a HDInsight **azon** verzióit sorolja fel, amelyek nem érhetők el az Azure Portalon.

| HDInsight verzió | HDP verzió | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |  Elérhetőség az Azure Portalon |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2,5 |Ubuntu 16.0.4 LTS |2016. szeptember 30. |2017. szeptember 5.September 5, 2017 |2018. június 28.June 28, 2018 |Igen |Nem |
| HDInsight 3.4 |HDP 2,4 |Ubuntu 14.0.4 LTS |2016. március 29. |2016. január 25. |2018. január 9. |Igen |Nem |
| HDInsight 3.3 |HDP 2,3 |Windows Server 2012 R2 |2015. augusztus 25. |2016. június 27. |2018. július 31. |Igen |Nem |
| HDInsight 3.3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2015. augusztus 25. |2016. június 27. |2017. július 31.July 31, 2017 |Igen |Nem |
| HDInsight 3.2 |HDP 2,2 |Ubuntu 12.04 LTS vagy Windows Server 2012 R2 |2015. február 18. |2016. március 1. |2017. április 1. |Igen |Nem |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014. június 24. |2015. május 18. |2016. június 30. |Igen |Nem |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014. február 11. |2014. szeptember 17. |2015. június 30. |Igen |Nem |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013. október 28. |2014. május 12. |2015. augusztus 25. |Igen |Nem |
| HDInsight 1.6 |HDP 1.1 | |2013. október 28. |2014. április 26. |2015. augusztus 25. |Nem |Nem |

> [!NOTE]  
> A két főcsomózóval rendelkező, magas rendelkezésre állású fürtök alapértelmezés szerint a HDInsight 2.1-es és újabb verziójához vannak telepítve. Ezek nem érhetők el a HDInsight 1.6-os verziójában.

## <a name="enterprise-security-package-for-hdinsight"></a>Vállalati biztonsági csomag a HDInsighthoz

Az Enterprise Security egy választható csomag, amelyet a FÜRTlétrehozása munkafolyamat részeként adhat hozzá a HDInsight-fürthöz. A vállalati biztonsági csomag a következőket támogatja:

- Integráció az Active Directoryval a hitelesítéshez.

    A múltban hdinsight-fürtöket hozott létre helyi rendszergazdai és helyi SSH-felhasználóval. A helyi rendszergazda i a fájlokat, mappákat, táblázatokat és oszlopokat is elérheti.  A Vállalati biztonsági csomag gal engedélyezheti a szerepköralapú hozzáférés-vezérlést a HDInsight integrálásával az Active Directoryval. Amely magában foglalja a helyszíni Active Directory, az Azure Active Directory tartományi szolgáltatások. Vagy az Active Directory iaaS virtuális gépen. A fürt tartományi rendszergazdája saját vállalati (tartomány) felhasználónevet és jelszót adhat a felhasználóknak.

    További információkért lásd:

    - [Bevezetés az Apache Hadoop biztonságába a tartományhoz csatlakozó HDInsight-fürtökkel](./domain-joined/hdinsight-security-overview.md)
    - [Az Azure-tartományhoz csatlakozó Apache Hadoop-fürtök tervezése a HDInsightban](./domain-joined/apache-domain-joined-architecture.md)
    - [Tartományhoz csatlakoztatott tesztkörnyezet konfigurálása](./domain-joined/apache-domain-joined-configure.md)
    - [Tartományhoz csatlakozott HDInsight-fürtök konfigurálása az Azure Active Directory tartományi szolgáltatások használatával](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Adatok engedélyezése

  - Integráció az Apache Rangerrel a Hive, a Spark SQL és a Fonalvárólisták engedélyezéséhez.
  - A fájlok és mappák hozzáférés-vezérlését beállíthatja.

    További információkért lásd:

  - [Apache Hive-házirendek konfigurálása a tartományhoz csatlakozó HDInsightban](./domain-joined/apache-domain-joined-run-hive.md)

- Tekintse meg a naplókat a hozzáférések és a konfigurált házirendek figyeléséhez.

### <a name="supported-cluster-types"></a>Támogatott fürttípusok

Jelenleg csak a következő fürttípusok támogatják a vállalati biztonsági csomagot:

- Hadoop (csak HDInsight 3.6 esetén)
- Spark
- Kafka
- HBase
- Interaktív lekérdezés

### <a name="support-for-azure-data-lake-storage"></a>Az Azure Data Lake Storage támogatása

Az Enterprise Security Package támogatja az Azure Data Lake Storage használatát mind az elsődleges tároló, mind a bővítménytároló ként.

### <a name="pricing-and-service-level-agreement-sla"></a>Árképzési és szolgáltatásiszint-szerződés (SLA)

A vállalati biztonsági csomag díjszabásáról és SLA-járól a [HDInsight-díjszabás című](https://azure.microsoft.com/pricing/details/hdinsight/)témakörben talál további információt.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Szolgáltatásiszint-szerződés a HDInsight-fürtverzióihoz

A szolgáltatásiszint-szerződés (SLA) _támogatási ablakként_van definiálva. A támogatási ablak az az időszak, `Microsoft Customer Service and Support`amely alatt a HDInsight-verziót támogatja. Ha a verzió egy átadott _támogatási lejárati dátum,_ a HDInsight-fürt kívül esik a támogatási ablakon. A HDInsight X verziójának lejárati ideje (az újabb X+1 verzió után) a következő:  

- Forma-1: Adjon hozzá 180 napot a HDInsight-fürt X verziójának kiadásának dátumához.
- 2. képlet: Adjon hozzá 90 napot ahhoz a dátumhoz, amikor a HDInsight-fürt X+1 verziója elérhetővé válik az Azure Portalon.

A _megszüntetési dátum_ az a dátum, amely után a fürtverziója nem hozható létre a HDInsight.The retirement date is the date that which the cluster version can't be created on HDInsight. 2017. július 31-től kezdődően nem méretezhet át egy HDInsight-fürtöt a megszüntetési dátum után.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Alapértelmezett csomópontkonfiguráció és virtuálisgép-méretek fürtökhöz

Ha többet szeretne tudni arról, hogy mely virtuálisgép-skus-okat kell kiválasztani a fürthöz, olvassa el az [Azure HDInsight fürt konfigurációs részletei című témakört.](hdinsight-supported-node-configuration.md)

## <a name="next-steps"></a>További lépések

- [Fürttelepítő az Apache Hadoophoz, a Sparkhoz és egyebekhez a HDInsight-on](hdinsight-hadoop-provision-linux-clusters.md)
- [Munka az Apache Hadoop-ban a HDInsight-on Windows rendszerű számítógépről](hdinsight-hadoop-windows-tools.md)
- [A Hortonworks az Azure HDInsight-verzióihoz kapcsolódó kibocsátási megjegyzések](./hortonworks-release-notes.md)
