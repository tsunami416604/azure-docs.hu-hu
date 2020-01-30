---
title: Összetevők és verziók Apache Hadoop – Azure HDInsight
description: Ismerje meg az Azure HDInsight Apache Hadoop összetevőit és verzióit.
keywords: Hadoop-verziók, Hadoop ökoszisztéma-összetevők, Hadoop-összetevők, Hadoop-verzió ellenőrzési módja
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/21/2019
ms.openlocfilehash: ed4175d55d3c462c75dad7305f95e2ec4f84baf9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764467"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Milyen Apache Hadoop-összetevők és-verziók érhetők el a HDInsight-ben?

Ismerje meg a Microsoft Azure HDInsight [Apache Hadoop](https://hadoop.apache.org/) ökoszisztémájának összetevőit és verzióit, valamint a Enterprise Security Package. Azt is megtudhatja, hogyan tekintheti meg a Hadoop összetevő-verzióit a HDInsight-ben.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop különböző HDInsight-verziókkal elérhető összetevők

Az Azure HDInsight több Hadoop-fürtöt is támogat, amelyek bármikor üzembe helyezhetők. 2017. április 4-én az Azure HDInsight által használt alapértelmezett fürt verziója 3,6.

A HDInsight-fürtökhöz társított verziók az alábbi táblázatban láthatók:

> [!NOTE]  
> Előfordulhat, hogy a HDInsight szolgáltatás alapértelmezett verziója értesítés nélkül változhat. Ha a verzió-függőséggel rendelkezik, adja meg a HDInsight verzióját, amikor létrehozza a fürtöket a .NET SDK-val a Azure PowerShell és a klasszikus Azure CLI használatával.

| Component (Összetevő)              | HDInsight 4.0 | HDInsight 3,6 (alapértelmezett)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop és fonal | 3.1.1         | 2.7.3                       |
| Apache TEZ             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0, 1.2.1                |
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
| Monó                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> A rendszer teljesítményével kapcsolatos megfontolások miatt a Kafka 0,10-es verziójának támogatása 2019 márciusában lejár.

## <a name="check-for-current-hadoop-component-version-information"></a>Az aktuális Hadoop-összetevő verziószámával kapcsolatos információk keresése

A Hadoop ökoszisztéma-összetevők HDInsight társított verziói a HDInsight frissítéseit is megváltoztathatják. A Hadoop-összetevők ellenőrzéséhez és a fürthöz használt verziók ellenőrzéséhez használja a Ambari REST API. A **GetComponentInformation** parancs adatokat kér le a szolgáltatás összetevőiről. Részletekért tekintse meg az [Apache Ambari dokumentációját](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Kibocsátási megjegyzések

A HDInsight legújabb verzióiban további kibocsátási megjegyzéseket a [HDInsight kibocsátási megjegyzései](hdinsight-release-notes.md) című témakörben talál.

## <a name="supported-hdinsight-versions"></a>Támogatott HDInsight-verziók

A következő táblázat a HDInsight verzióját sorolja fel. A támogatási lejárati és a nyugdíjazási dátumok is megadhatók, ha ismertek.

### <a name="available-versions"></a>Elérhető verziók

A következő táblázat felsorolja a Azure Portalban elérhető HDInsight-verziókat, valamint az egyéb központi telepítési módszereket, például a PowerShellt és a .NET SDK-t.

| HDInsight verziója | VM OPERÁCIÓS RENDSZER | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |  Rendelkezésre állás a Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018. szeptember 24. | | |Igen |Igen |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |2017. április 4. | December 31., 2020 |December 31., 2020 |Igen |Igen |

A Spark 2,1, 2,2 & Kafka 1,0 támogatásának érvényessége 2020. június 30-án lejár.

> [!NOTE]  
> A verzió támogatásának lejártakor előfordulhat, hogy nem érhető el a Microsoft Azure Portalon keresztül. A fürtcsomópontok azonban továbbra is elérhetők a Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsában és a .net SDK-ban lévő `Version` paraméterrel a verzió kivonulásának dátumánál.

### <a name="retired-versions"></a>Kivont verziók

A következő táblázat felsorolja azokat a HDInsight-verziókat, amelyek **nem** érhetők el a Azure Portalban.

| HDInsight verziója | HDP verziója | VM OPERÁCIÓS RENDSZER | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |  Rendelkezésre állás a Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |Szeptember 30., 2016 |Szeptember 5., 2017 |2018. június 28. |Igen |Nem |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |Március 29., 2016 |December 29., 2016 |2018. január 9. |Igen |Nem |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2015. december 2. |2016. június 27. |2018. július 31. |Igen |Nem |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2015. december 2. |2016. június 27. |2017. július 31. |Igen |Nem |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS vagy Windows Server 2012 R2 |Február 18., 2015 |2016. március 1. |2017. április 1. |Igen |Nem |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 R2 |Június 24., 2014 |2015. május 18. |Június 30., 2016 |Igen |Nem |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 R2 |Február 11., 2014 |Szeptember 17., 2014 |Június 30., 2015 |Igen |Nem |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |Október 28., 2013 |2014. május 12. |2015. május 31. |Igen |Nem |
| HDInsight 1,6 |HDP 1,1 | |Október 28., 2013 |Április 26., 2014 |2015. május 31. |Nem |Nem |

> [!NOTE]  
> A HDInsight 2,1-es és újabb verzióiban a két fő csomóponttal rendelkező, magasan elérhető fürtök üzembe helyezése alapértelmezés szerint megtörténik. A HDInsight 1,6-es verziójú fürtök esetében nem érhetők el.

## <a name="enterprise-security-package-for-hdinsight"></a>HDInsight Enterprise Security Package

A vállalati biztonság egy opcionális csomag, amelyet hozzáadhat a HDInsight-fürthöz a fürt létrehozása munkafolyamat részeként. A Enterprise Security Package a következőket támogatja:

- Active Directory integrációja a hitelesítéshez.

    A múltban csak helyi rendszergazdai felhasználóval és helyi SSH-felhasználóval hozhat létre HDInsight-fürtöket. A helyi rendszergazda felhasználó elérheti az összes fájlt, mappát, táblát és oszlopot.  A Enterprise Security Package lehetővé teszi a szerepköralapú hozzáférés-vezérlést, ha a HDInsight-fürtöket a saját Active Directoryba integrálja, beleértve a helyszíni Active Directory, Azure Active Directory Domain Services vagy Active Directoryt a IaaS virtuális gép. A fürt tartományi rendszergazdája engedélyezheti a felhasználóknak, hogy a saját vállalati (tartományi) felhasználónevét és jelszavát használják a fürt eléréséhez.

    További információ eléréséhez lásd:

    - [Bevezetés a Apache Hadoop biztonságba a tartományhoz csatlakoztatott HDInsight-fürtökkel](./domain-joined/hdinsight-security-overview.md)
    - [Azure-tartományhoz csatlakoztatott Apache Hadoop-fürtök tervezése a HDInsight-ben](./domain-joined/apache-domain-joined-architecture.md)
    - [Tartományhoz csatlakoztatott homokozó környezet konfigurálása](./domain-joined/apache-domain-joined-configure.md)
    - [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása Azure Active Directory Domain Services használatával](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Adathozzáférés engedélyezése

  - Integráció az Apache Ranger-nal a kaptár, a Spark SQL és a fonalas várólisták engedélyezéséhez.
  - Megadhatja a fájlok és mappák hozzáférés-vezérlését.

    További információ eléréséhez lásd:

  - [Apache Hive házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight](./domain-joined/apache-domain-joined-run-hive.md)

- Tekintse meg a naplókat a hozzáférések és a konfigurált szabályzatok figyeléséhez.

### <a name="supported-cluster-types"></a>Támogatott fürtök típusai

Jelenleg csak a következő típusú fürtök támogatják a Enterprise Security Package:

- Hadoop (csak HDInsight 3,6)
- Spark
- Interaktív lekérdezés

### <a name="support-for-azure-data-lake-storage"></a>Azure Data Lake Storage támogatása

A Enterprise Security Package az elsődleges tárolóként és a kiegészítő tárolóként is támogatja a Azure Data Lake Storage használatát.

### <a name="pricing-and-service-level-agreement"></a>Díjszabás és szolgáltatói szerződés

További információ a Enterprise Security Package díjszabásáról és SLA-ról: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight-fürt verzióinak szolgáltatói szerződése

A szolgáltatói szerződés (SLA) a _támogatási ablakban_van meghatározva. A támogatási ablak az az időszak, ameddig a HDInsight-fürt verzióját támogatja a Microsoft Customer Service és a support. Ha a verziónak már van egy _támogatott lejárati dátuma_ , a HDInsight-fürt a támogatási ablakon kívül esik. A megadott HDInsight X-es verziójának (újabb X + 1 verzió) lejárati dátuma a következőre lesz kiszámítva:  

- 1\. képlet: 180 nap hozzáadása a HDInsight-fürt X verziójának kiadási dátumához.
- 2\. képlet: adja hozzá a 90 nappal azt a dátumot, amikor a HDInsight-fürt X + 1 verziója elérhetővé válik Azure Portalban.

A lejárati _dátum_ az a dátum, amely után a fürt verziója nem hozható létre a HDInsight. 2017. július 31-ig nem lehet átméretezni egy HDInsight-fürtöt a kivonulási dátum után.

> [!NOTE]  
> A HDInsight Windows-fürtök (beleértve a 2,1, 3,0, 3,1, 3,2 és 3,3 verziót) az Azure vendég operációsrendszer-család 4-es verzióján futnak, amely a Windows Server 64 R2 2012-bites verzióját használja. Az Azure vendég operációsrendszer-család 4-es verziója támogatja a .NET-keretrendszer 4,0-es, 4,5-es, 4.5.1-es és 4.5.2-es verzióit.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>A HDInsight-verziókhoz tartozó Hortonworks kibocsátási megjegyzések

A szakasz hivatkozásokat tartalmaz a Hortonworks adatplatform-disztribúciók és a HDInsight-mel használt Apache-összetevők kibocsátási megjegyzései számára.
* A HDInsight-fürt 4,0-es verziója olyan Hadoop-eloszlást használ, amely a 3,0-es [Hortonworks-Adatplatformon](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) alapul
* A HDInsight-fürt 3,6-es verziója olyan Hadoop-eloszlást használ, amely a 2,6-es [Hortonworks-Adatplatformon](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html)alapul.
* A HDInsight-fürt 3,5-es verziója olyan Hadoop-eloszlást használ, amely a 2,5-es [Hortonworks-Adatplatformon](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html)alapul. A HDInsight-fürt 3,5-es verziója a Azure Portalban létrehozott _alapértelmezett_ Hadoop-fürt.
* A HDInsight-fürt 3,4-es verziója olyan Hadoop-eloszlást használ, amely a 2,4-es [Hortonworks-Adatplatformon](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)alapul.
* A HDInsight-fürt 3,3-es verziója olyan Hadoop-eloszlást használ, amely a 2,3-es [Hortonworks-Adatplatformon](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)alapul.

  * [Apache Storm kibocsátási megjegyzések](https://storm.apache.org/2015/11/05/storm0100-released.html) az Apache webhelyén érhetők el.
  * [Apache Hive kibocsátási megjegyzések](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) az Apache webhelyén érhetők el.
* A HDInsight-fürt 3,2-es verziója olyan Hadoop-eloszlást használ, amely a 2,2-es [Hortonworks-Adatplatformon][hdp-2-2]alapul.

  * Az Apache-összetevők kibocsátási megjegyzései a következőképpen érhetők el [: kaptár 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [fonal 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [TEZ 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2,0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)és [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* A HDInsight-fürt 3,1-es verziója a [Hortonworks adatplatform-2.1.7][hdp-2-1-7]alapuló Hadoop-eloszlást használ. A HDInsight 3,1-es, 2014-ig létrehozott fürtök a [Hortonworks adatplatform 2.1.1][hdp-2-1-1]-es változata alapján lettek létrehozva.
* A HDInsight-fürt 3,0-es verziója olyan Hadoop-eloszlást használ, amely a 2,0-es [Hortonworks-Adatplatformon][hdp-2-0-8]alapul.
* A HDInsight-fürt 2,1-es verziója olyan Hadoop-eloszlást használ, amely a 1,3-es [Hortonworks-Adatplatformon][hdp-1-3-0]alapul.
* A HDInsight-fürt 1,6-es verziója olyan Hadoop-eloszlást használ, amely a 1,1-es [Hortonworks-Adatplatformon][hdp-1-1-0]alapul.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>A csomópontok alapértelmezett konfigurációja és a virtuális gépek méretei a fürtökhöz

További információ arról, hogy mely virtuálisgép-SKU-ket szeretné kijelölni a fürt számára: az [Azure HDInsight-fürt konfigurációjának részletei](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Következő lépések

- [Fürt beállítása Apache Hadoop, Spark és más HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Munkahelyi Apache Hadoop HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
