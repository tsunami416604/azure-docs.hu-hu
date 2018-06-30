---
title: Hadoop-összetevők és verziók - Azure HDInsight |} Microsoft Docs
description: Ismerje meg, a Hadoop-összetevők és a HDInsight és a szolgáltatási szintek érhető el a felhőalapú terjesztési Hortonworks Data platform-verziók.
keywords: hadoop verziók, a hadoop-ökoszisztémával összetevők, a hadoop-összetevők, hogyan hadoop-verziójának ellenőrzése
services: hdinsight
editor: cgronlun
manager: asadk
author: kkampf
tags: azure-portal
documentationcenter: ''
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: kakampf
ms.openlocfilehash: f79d80a2743fb1a46fd2f17ec3b99d97e3b3d8d8
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111024"
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Mik azok a Hadoop-összetevők és a hdinsight eszközzel verziók?

További tudnivalók az Apache Hadoop-ökoszisztémával összetevők és a Microsoft Azure HDInsight, valamint a vállalati biztonsági csomag verziója. Emellett ismerje meg a HDInsight Hadoop összetevő verziók ellenőrzéséhez. 

Minden HDInsight-verzió egy felhőalapú terjesztési verziójának Hortonworks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>HDInsight különböző verzióiban Hadoop-összetevők
Az Azure HDInsight Hadoop fürt több verziója, amely bármikor telepíthető támogatja. Minden egyes verzió choice hoz létre, egy adott verziójához a HDP telepítési és összetevők belüli, hogy a terjesztési. 2017. április 4. frissítésétől Azure HDInsight által használt alapértelmezett fürt verzió 3.6 és HDP 2.6 alapul.

A HDInsight-fürt verziókról társított összetevő-verziók a következő táblázatban láthatók: 

> [!NOTE]
> Az alapértelmezett verzió a HDInsight szolgáltatás minden külön értesítés nélkül változhatnak. Ha verzió függőség, ha a .NET SDK-val Azure PowerShell és az Azure parancssori felület a fürtök létrehozásához, adja meg a HDInsight-verzió.

| Összetevő | HDInsight 3.6 (alapértelmezett) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | A HDInsight 3.2. | HDInsight 3.1 | A HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop és a YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive és HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.3.0, 2.2.0, 2.1.0 |1.6.2, 2.0-s |1.6.0 |1.5.2 |1.3.1 (csak Windows) |-|-|
| Apache Livy |0.4 |0,3 |0,3 |0.2 |-|-|-|
| Apache Kafka | 1.0, 0.10.1 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Monó |4.2.1 |4.2.1 |3.2.8 |-|-|-|
| Apache csúszka | 0.92.0 |-|-|-|-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Ellenőrizze a jelenlegi Hadoop összetevő verzióinformáció

HDInsight-fürt verziókról társított Hadoop-ökoszisztémával összetevő verziók HDInsight módosíthatja a frissítések. Ellenőrizze a Hadoop-összetevők, és ellenőrizze, hogy mely verzióit használatban van egy fürt használja az Ambari REST API-t. A **GetComponentInformation** parancs segítségével lekérdezhető szolgáltatás-összetevőivel kapcsolatos információk. További információkért lásd: a [Ambari dokumentáció][ambari-docs].

Windows-fürtök esetén egy másik összetevő verziójának módja távoli asztal használatával jelentkezzen be egy fürtöt, és vizsgálja meg a C:\apps\dist\ könyvtár tartalmát.

> [!IMPORTANT]
> Linux az egyetlen operációs rendszer használt a HDInsight 3.4 vagy újabb verziója. További információkért lásd: [Windows használatból való kivonást a HDInsight](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Kibocsátási megjegyzések

Lásd: [HDInsight kibocsátási megjegyzések](hdinsight-release-notes.md) további kibocsátási megjegyzések a HDInsight legújabb verziói.

## <a name="supported-hdinsight-versions"></a>Támogatott HDInsight-verziókról
Az alábbi táblázatok a HDInsight-verziók. Az, hogy minden HDInsight-verzió HDP-verziók szerepelnek a listán, a termék kiadási dátum együtt. A támogatás lejárati és a használatból való kivonást dátumát is biztosít, ha azok még ismert.

### <a name="available-versions"></a>Elérhető verzió

A következő táblázat használható az Azure portál, valamint a más központi telepítési módszerek, például a PowerShell és a .NET SDK-val a HDInsight-verziók listája olvasható.

| HDInsight-verzió | HDP verzió | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Kivezetési dátum | Magas rendelkezésre állás |  Az Azure portálon rendelkezésre állása | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6. |2.6 HDP |Ubuntu 16.0.4 LTS |2017. április 4. | | |Igen |Igen |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015. december 2. |2016. június 27. |2018. július 31-ig. |Igen |Nem |

> [!NOTE]
> Támogatási után az egy lejárt, azt nem feltétlenül érhető el a Microsoft Azure portálon keresztül. Azonban fürt verziók továbbra is a rendelkezésre álló használatával a `Version` a Windows PowerShell paraméter [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) parancs és a .NET SDK, amíg a verzió kivezetési dátum.
>

### <a name="retired-versions"></a>Elavult verzió

A következő táblázat, amelyek a HDInsight-verziók listája olvasható **nem** elérhető az Azure portálon.

| HDInsight-verzió | HDP verzió | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Kivezetési dátum | Magas rendelkezésre állás |  Az Azure portálon rendelkezésre állása | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |2.5 HDP |Ubuntu 16.0.4 LTS |2016. Szeptembertől 30. |2017. szeptember 5. |2018. június 28. |Igen |Nem |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016. március 29. |2016. december 29. |2018. január 9. |Igen |Nem |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015. december 2. |2016. június 27. |2017. július 31-ig. |Igen |Nem |
| A HDInsight 3.2. |2.2 HDP |Ubuntu, 12.04 LTS, vagy a Windows Server 2012 R2 rendszerben |2015. február 18. |2016. március 1. |2017. április 1. |Igen |Nem |
| HDInsight 3.1 |2.1 HDP |Windows Server 2012 R2 |2014. június 24. |2015. május 18. |2016. június 30. |Igen |Nem |
| A HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014. február 11. |2014. szeptember 17. |2015. június 30. |Igen |Nem |
| A HDInsight 2.1-es verziója |1.3 HDP |Windows Server 2012 R2 |2013. október 28. |2014. május 12. |2015. május 31-ig. |Igen |Nem |
| HDInsight 1.6-os |1.1 HDP | |2013. október 28. |2014. április 26. |2015. május 31-ig. |Nem |Nem |

> [!NOTE]
> Magas rendelkezésre állású fürtök két átjárócsomópontokkal a HDInsight-verzió 2.1-es és újabb verziók esetében alapértelmezés szerint vannak telepítve. Nem elérhetők a HDInsight-fürtök 1.6-os verzióra.

## <a name="enterprise-security-package-for-hdinsight"></a>A HDInsight a vállalati biztonsági csomag

A vállalati biztonsági csomag egy nem kötelező, amelyeket felvehet a HDInsight-fürt létrehozása a fürt munkafolyamat részeként. A vállalati biztonsági csomag támogatja:

- Active Directory integrációja a hitelesítéshez.

    A múltban csak egy felhasználót a helyi rendszergazda és a helyi SSH-felhasználó hozhat létre HDInsight-fürtök. A helyi rendszergazda felhasználónak hozzáférhető minden a fájlok, mappák, táblákat és oszlopokat.  A vállalati biztonsági csomag engedélyezheti szerepköralapú hozzáférés-vezérlés a HDInsight-fürtök integrálása a saját Active Directoryval, amelyek magukban foglalják a helyszíni Active Directory, Azure Active Directory tartományi szolgáltatások vagy infrastruktúra-szolgáltatási az Active Directory a virtuális gép. A fürtön tartományi rendszergazda biztosíthat a felhasználók számára saját vállalati (tartomány)-felhasználónevet és jelszót a fürt eléréséhez használja. 

    További információkért lásd:

    - [Tartományhoz csatlakozó HDInsight-fürtökkel a Hadoop biztonsági bemutatása](./domain-joined/apache-domain-joined-introduction.md)
    - [Tervezze meg az Azure-tartományhoz Hadoop-fürtök a Hdinsightban](./domain-joined/apache-domain-joined-architecture.md)
    - [Tartományhoz csatlakozó védőfal mögötti környezet konfigurálása](./domain-joined/apache-domain-joined-configure.md)
    - [Tartományhoz csatlakozó HDInsight-fürtök Azure Active Directory tartományi szolgáltatások konfigurálása](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Az engedélyezési adatok

    - A hitelesítéshez a Hive, a Spark SQL és a Yarn várólisták Apache Pletyka integrációja.
    - Hozzáférés-vezérlés a fájlok és mappák állíthatja be.

    További információkért lásd:

    - [A tartományhoz csatlakoztatott HDInsight Hive-szabályzatok konfigurálása](./domain-joined/apache-domain-joined-run-hive.md)

- A figyelő hozzáférések és a konfigurált házirendek naplófájlok megtekintése. 

### <a name="supported-cluster-types"></a>Támogatott fürttípusok

Jelenleg csak a következő fürttípus támogatja a vállalati biztonsági csomag:

- Hadoop (csak a 3.6. HDInsight)
- Spark
- Interaktív lekérdezés

### <a name="support-for-azure-data-lake-store"></a>Az Azure Data Lake Store támogatása

A vállalati biztonsági csomag támogatja az Azure Data Lake Store használja, mint az elsődleges és a bővítmény tárolására is.

### <a name="pricing-and-sla"></a>Díjszabás és SLA
A vállalati biztonsági csomag tarifa- és SLA-t a információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>A windowsos HDInsight kivonása
A Microsoft Azure HDInsight 3.3-as verzió a legfrissebb verziója a Windows a HDInsight volt. A Windows a HDInsight a kivezetési dátum 2018 július 31. Ha a HDInsight-fürtök Windows 3.3-as vagy annál régebbi, át kell telepítenie a Linux (HDInsight 3.5-ös vagy újabb verziójú) HDInsight 2018 július 31 előtt. A Linux operációs rendszert futtató történő lehetővé teszi létrehozása vagy a HDInsight-fürtök átméretezése megőrzése. HDInsight Windows 3.3-as verzió támogatása lejárt 2016. június 27.

HDInsight 3.4-es verziójú verziótól kezdődően a Microsoft közzétette a HDInsight csak a Linux operációs rendszeren. Ennek eredményeképpen egyes összetevői belül HDInsight érhetők el a Linux csak. Ezek közé tartoznak, Apache Pletyka, Kafka, interaktív lekérdezést, a Spark HDInsight-alkalmazásokat, és az Azure Data Lake Store elsődleges fájlrendszer. A HDInsight a későbbi kiadásokban csak a Linux operációs rendszeren érhetők el. Nincs HDInsight a Windows későbbi kiadásaiban lesz. 

## <a name="faqs"></a>Gyakori kérdések

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Mi az az ütemterv HDInsight eltávolítása Windows?
2018. július 31. a Windows a HDInsight a kivezetési dátum. Ha a tervezett kivezetési dátum eltér a régió, értesítést kap külön-külön. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Mi az a Windows HDInsight eltávolítása a meglévő ügyfeleknek hatását?
A Windows HDInsight kivonását követően nem egy új HDInsight Windows-fürt létrehozása, vagy egy meglévő HDInsight-Windows-fürt méretezése. HDInsight 3.3-as verzió támogatása lejárt 2016. június 27. Ezért nincs támogatási vagy a HDInsight 3.3-as vagy korábbi verziójú hibajavításokat tartalmaz. A HDInsight a későbbi kiadásokban csak a Linux operációs rendszeren érhetők el. Nincs HDInsight a Windows későbbi kiadásaiban lesz.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>A HDInsight a Windows mely verzióit érintett?
Az Azure HDInsight 3.3-as verziójú HDInsight Windows utolsó verziója telepítve. HDInsight Windows kivonják, mielőtt az összes HDInsight Windows fürtök 3.3-as verziójának vagy korábbi verzióját HDInsight Linux 3.5-ös vagy újabb verzió kell áttelepíteni. A fürtökhöz történő HDInsight Linux rendszeren lehetővé teszi új fürtök létrehozása vagy meglévő fürtök átméretezése megőrzése. 

### <a name="what-do-i-need-to-do"></a>Mit kell tennem?
A Windows a HDInsight-fürtök áttelepítése támogatott HDInsight Linux fürtre 2018 július 31 előtt. További információ: a [HDInsight áttelepítési dokumentum](hdinsight-migrate-from-windows-to-linux.md). További Azure HDInsight verzióival kapcsolatos információkért lásd: listájának [támogatott verziók](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Hol található a fürt az operációs rendszer típusa?
Az Azure portálon lépjen a HDInsight-fürtök – áttekintés oldalra, és keresse meg **típusú fürt** alatt **Essentials**. Azok a fürttípusok az operációs rendszer adott oldalon találhatók. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>I nem telepíthet át egy HDInsight Linux-fürt által 2018 július 31-ig. Újdonságok a Windows a HDInsight fürt gyakorolt?
A HDInsight-Windows-fürt futtatja-van, de nem hozható létre egy új HDInsight Windows-fürt, vagy egy meglévő HDInsight-Windows-fürt méretezése. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>A fürt egy .NET-függőség van. Hogyan oldja meg a függőség Linux?
A Linux-fürt függőségi használva oldhatja a [monó projekt](http://www.mono-project.com/). A nyílt forráskódú végrehajtása .NET HDInsight Linux-fürtök érhető el. További információ: a [HDInsight áttelepítési dokumentum](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>A HDInsight a Windows egy új ügyfél vagyok. Hogyan hozható létre egy HDInsight Windows fürt?
2017. július 3. frissítésétől csak meglévő HDInsight-Windows-ügyfelek tárolófürtöket hozhat létre új HDInsight Windows. Új ügyfelek nem hozható létre egy HDInsight Windows-fürthöz az Azure portálon PowerShell vagy az SDK használatával. Javasoljuk, hogy új ügyfelek hozzon létre egy Linux HDInsight-fürtöt. Meglévő ügyfelek tárolófürtöket hozhat létre új HDInsight Windows a kivezetési dátum Windows HDInsight-ig. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Van egy árképzési hatás társított Windows HDInsight Linux rendszeren továbblép a HDInsight-ból?
Nem, a jelenlegi díjszabás megegyezik a HDInsight vagy operációs rendszer. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Mik azok a felhasználói előnyeit, csak a HDInsight Linux rendszeren tételének társított?
* Gyorsabb idő piacra jutási nyílt forráskódú big Data típusú adatok technológiák a HDInsight szolgáltatáson keresztül
* A nagy közösségi és támogatási ökoszisztémájának
* Azon képessége, hogy a Hadoop és egyéb big Data típusú adatok technológiák nyílt forráskódú közösségi aktív fejlesztés

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>HDInsight Linux rendszeren nyújt további funkciók túl az elérhető a Windows a Hdinsightban?
HDInsight 3.4-es verziójú verziótól kezdődően a Microsoft közzétette a HDInsight csak a Linux operációs rendszeren. Ennek eredményeképpen egyes összetevői belül HDInsight érhetők el a Linux csak. Ezek közé tartoznak, Apache Pletyka, Kafka, interaktív lekérdezést, a Spark HDInsight-alkalmazásokat, és az Azure Data Lake Store elsődleges fájlrendszer. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight-fürt verziókról szolgáltatásiszint-szerződés
A szolgáltatásiszint-szerződéssel (SLA) van megadva a egy _támogatási ablak_. A támogatási időszak, amelyet egy HDInsight-fürt verziószáma támogat a Microsoft ügyfélszolgálata és mennyi ideig. A verzió-e egy _támogatja a lejárati dátum_ , amely megfelelt, a támogatási időszakon kívül van a HDInsight-fürthöz. Támogatott verzióival kapcsolatos további információkért tekintse meg a listája [támogatott HDInsight-fürt verziók](hdinsight-migrate-from-windows-to-linux.md). A megadott HDInsight verziója X (miután elérhetővé vált egy újabb X + 1) támogatás lejárati dátuma, a későbbi kiszámítása a:  

* 1. képlet: 180 nap hozzáadása a dátum, amikor a HDInsight-fürt verziószáma X jelent.
* 2. képlet: 90 nap hozzáadása a dátum, amikor a HDInsight-fürt verziószáma X + 1 szeretné elérhetővé tenni az Azure portálon.

A _kivezetési dátum_ a dátum, amely után a fürt verziószáma nem hozható létre a hdinsight platformon. 2017. július 31., kezdve a kivezetési dátum után egy HDInsight-fürt nem tudja átméretezni. 

> [!NOTE]
> HDInsight Windows-fürtök (többek között a következőket verzió 2.1, 3.0-s, 3.1, 3.2-es és 3.3-as) futtatható Azure Vendég operációsrendszer-család 4-es verzióját, amely a Windows Server 2012 R2 64 bites verzióját használja. Azure Vendég operációsrendszer-család 4-es verziója támogatja a .NET-keretrendszer verziója 4.0-s, 4.5-ös, 4.5.1 és 4.5.2-es.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks kibocsátási megjegyzéseket társított a HDInsight-verziókról

A szakasz a kibocsátási megjegyzésekben a Hortonworks Data Platform disztribúcióiról, valamint a hdinsight eszközzel használt Apache összetevők mutató hivatkozásokat tartalmaz.
* HDInsight-fürt verziószáma 3.6 alapuló Hadoop-terjesztést használja [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* HDInsight-fürt verziószáma 3.5-ös verzióját használja egy Hadoop-terjesztést alapuló [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). A HDInsight fürt 3.5-ös verziója a _alapértelmezett_ Hadoop-fürt, amely az Azure portálon jön létre.
* HDInsight-fürt verziószáma 3.4 alapuló Hadoop-terjesztést használja [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* HDInsight-fürt verziószáma 3.3-as verzióját használja egy Hadoop-terjesztést alapuló [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Apache Storm kibocsátási megjegyzések](https://storm.apache.org/2015/11/05/storm0100-released.html) az Apache webhelyen érhetők el.
  * [Kibocsátási megjegyzések Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) az Apache webhelyen érhetők el.
* A HDInsight fürt 3.2-es verziójú alapuló Hadoop-terjesztést használja [Hortonworks Data Platform 2.2][hdp-2-2].

  * Release notes for specific Apache components are available as follows: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), and [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight-fürt verziószáma 3.1 alapuló Hadoop-terjesztést használja [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. 2014. novemberi, 7, előtt létrehozott HDInsight 3.1 fürtök alapuló [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* HDInsight-fürt verziószáma 3.0 alapuló Hadoop-terjesztést használja [Hortonworks Data Platform 2.0][hdp-2-0-8].
* HDInsight-fürt verziószáma 2.1-es verzióját használja egy Hadoop-terjesztést alapuló [Hortonworks Data Platform 1.3][hdp-1-3-0].
* HDInsight-fürt verziószáma 1.6-os alapuló Hadoop-terjesztést használja [Hortonworks Data Platform 1.1][hdp-1-1-0].






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Alapértelmezett csomópont konfigurációs és virtuális gépek méretei fürtök
Az alábbi táblázatok a HDInsight-fürtök az alapértelmezett virtuális gép (VM) mérete.

> [!IMPORTANT]
> Ha több mint 32 munkavégző csomópontokhoz fürtben, ki kell választania egy átjárócsomóponttal mérete legalább 8 maggal és 14 GB RAM-mal.
> 
> 

* Dél-Brazília és Nyugat-japán kivételével minden támogatott régiók:

  | Fürttípus | Hadoop | HBase | Interaktív lekérdezés | Storm | Spark | Gépi tanulás összetevő-kiszolgáló |
  | --- | --- | --- | --- | --- | --- | --- |
  | HEAD: alapértelmezett Virtuálisgép-méretet |D3 v2 |D3 v2 | D13, D14 |A3 |D12 v2 |D12 v2 |
  | HEAD: ajánlott Virtuálisgép-méretek |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |A3 MÉRETŰ, A4 MÉRETŰ, A5 CSOMAG |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Munkavégző: alapértelmezett Virtuálisgép-méretet |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Munkavégző: ajánlott Virtuálisgép-méretek |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: alapértelmezett Virtuálisgép-méretet | |A3 | |A2 | | |
  | ZooKeeper: ajánlott Virtuálisgép-méretek | |A3 MÉRETŰ, A4 MÉRETŰ, A5 CSOMAG | | A2 MÉRETŰ, A3 MÉRETŰ, A4 | | |
  | Peremhálózati: alapértelmezett Virtuálisgép-méretet | | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Peremhálózati: ajánlott Virtuálisgép-mérettel | | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Dél-Brazília és Nyugat-japán csak (nincs v2 méretű):

  | Fürttípus | Hadoop | HBase | Interaktív lekérdezés |Storm | Spark | Gépi tanulás összetevő-kiszolgáló |
  | --- | --- | --- | --- | --- | --- | --- |
  | HEAD: alapértelmezett Virtuálisgép-méretet |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | HEAD: ajánlott Virtuálisgép-méretek |D3 D4, D12 |D3 D4, D12  | D13, D14 |A3 MÉRETŰ, A4 MÉRETŰ, A5 CSOMAG |D12, D13, D14 |D12, D13, D14 |
  | Munkavégző: alapértelmezett Virtuálisgép-méretet |D3 |D3  | D13, D14 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Munkavégző: ajánlott Virtuálisgép-méretek |D3 D4, D12 |D3 D4, D12  | D13, D14 |D3 D4, D12 |Windows: D12 D13, D14; Linux: D4, D12 D13, D14 |Windows: D12 D13, D14; Linux: D4, D12 D13, D14 |
  | ZooKeeper: alapértelmezett Virtuálisgép-méretet | |A2 | | A2 | | |
  | ZooKeeper: ajánlott Virtuálisgép-méretek | |A2 MÉRETŰ, A3 MÉRETŰ, A4 | |A2 MÉRETŰ, A3 MÉRETŰ, A4 | | |
  | Peremhálózati: alapértelmezett Virtuálisgép-méretek | | | | | |Windows: D12; Linux: D4 |
  | Peremhálózati: ajánlott Virtuálisgép-méretek | | | | | |Windows: D12 D13, D14; Linux: D4, D12 D13, D14 |

> [!NOTE]
> - HEAD nevezik *Nimbus* a Storm a fürt típusa.
> - Néven dolgozó *felügyelő* a Storm a fürt típusa.
> - Néven dolgozó *régió* a HBase a fürt típusa.

## <a name="next-steps"></a>További lépések
- [A telepítő Hadoop, Spark, és a HDInsight fürt](hdinsight-hadoop-provision-linux-clusters.md)
- [Működik a Hadoop on HDInsight from Windows-számítógépek](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
