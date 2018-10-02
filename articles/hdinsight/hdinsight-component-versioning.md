---
title: Hadoop-összetevők és verziók – Azure HDInsight
description: Ismerje meg, a Hadoop összetevői és verziói a HDInsight és a felhőalapú terjesztési Hortonworks Data platform elérhető a szolgáltatási szintek.
keywords: hadoop-verziók, hadoop-ökoszisztéma összetevők, hadoop-összetevők, hogyan ellenőrizheti a hadoop-verzió
services: hdinsight
ms.reviewer: jasonh
author: kkampf
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: kakampf
ms.openlocfilehash: 2047a9fbbe1bc7d867bb1c300f882e287ab8ad73
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018910"
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Mik azok a Hadoop-összetevők és a HDInsight-verziók?

További tudnivalók az Apache Hadoop-ökoszisztéma összetevők és verziók a Microsoft Azure HDInsight, valamint a vállalati biztonsági csomag. Emellett ismerje meg a HDInsight Hadoop-összetevők verziók ellenőrzéséhez. 

Minden egyes HDInsight felhőalapú terjesztése egy verziójának megfelelő Hortonworks Data Platform (HDP) verziószáma.

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Hadoop-összetevők a különböző HDInsight-verziók
Az Azure HDInsight Hadoop fürt több verzió telepítését követően bármikor támogatja. Minden verzió tetszőleges meghatározott verziójához a HDP telepítési és összetevők, hogy a terjesztés belüli lemezképcsomagban hoz létre. 2017. április 4, az Azure HDInsight által használt alapértelmezett fürtverzió 3.6-os és a HDP 2.6 alapul.

A HDInsight-fürt verziók-összetevő verzió az alábbi táblázatban láthatók: 

> [!NOTE]
> A HDInsight szolgáltatás az alapértelmezett verziójának előzetes értesítés nélkül változhatnak. Ha egy függőségi, adja meg a HDInsight-verzió a .NET SDK használatával az Azure PowerShell és a klasszikus Azure CLI-fürtök létrehozásakor.

| Összetevő | HDInsight 4.0-s (előzetes verzió) | A HDInsight 3.6-os (alapértelmezett) | HDInsight 3.5-ös verzióját. | HDInsight 3.4 | HDInsight 3.3. | HDInsight 3.2-es verzióját | HDInsight 3.1. | HDInsight 3.0-s |
| --- | --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |3.0 |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Az Apache Hadoop és YARN |2.9.1-es |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Az Apache Tez |0.9.1 |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Az Apache Pig |0.16.0 |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Az Apache Hive és HCatalog |-|1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Az Apache Hive |3.1.0 | 2.1.0 |-|-|-|-|-|-|
| Az Apache Tez Hive2 |-| 0.8.4 |-|-|-|-|-|-|
| Apache Ranger |1.1.0 |0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |2.0.1 |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Az Apache sqoop használatával |1.4.7 |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Az Apache Oozie |4.3.1. |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Az Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.2.1 |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Az Apache mahout használatával |-|0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Az Apache Phoenixhez |5 |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.3.1 |2.3.0-át, 2.2.0, 2.1.0 |1.6.2-es verzióján, 2.0-s |1.6.0-s |1.5.2 |1.3.1 (csak Windows) |-|-|
| Az Apache Livy |0.5 |0.4 |0,3 |0,3 |0.2 |-|-|-|
| Apache Kafka | 1.0.1 |1.1-es, 1.0-s, 0.10.1 | 0.10.0 | 0.9.0 |-|-|-|-|
| Az Apache Ambari | 2.7.0 |2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Az Apache Zeppelin | 0.8.0 |0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |4.2.1 |3.2.8 |-|-|-|
| Az Apache csúszka |-| 0.92.0 |-|-|-|-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Hadoop-összetevő verzióinformációkat aktuális keresése

Társított fürtverziók HDInsight Hadoop-ökoszisztéma összetevő verziók a frissítések HDInsight módosíthatja. Ellenőrizze a Hadoop-összetevők, és ellenőrizze, hogy mely verziói vannak használatban a fürt, az Ambari REST API használata. A **GetComponentInformation** a paranccsal kérdezhető le információ a szolgáltatás-összetevők. További információkért lásd: a [Ambari dokumentáció][ambari-docs].

Windows-fürtök másik módja, hogy az összetevők verziójának ellenőrzéséhez, hogy jelentkezzen be a fürthöz a távoli asztal használatával, és vizsgálja meg a C:\apps\dist\ könyvtár tartalmát.

> [!IMPORTANT]
> Linux az egyetlen operációs rendszer használt a HDInsight 3.4-es vagy újabb verzió. További információkért lásd: [a HDInsight Windows kivezetési](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Kibocsátási megjegyzések

Lásd: [HDInsight kibocsátási megjegyzései](hdinsight-release-notes.md) további kibocsátási megjegyzések a HDInsight a legfrissebb verzióit.

## <a name="supported-hdinsight-versions"></a>Támogatott HDInsight-verziók
Az alábbi táblázatok sorolják fel a HDInsight verzióit. Az egyes HDInsight verziószámnak HDP verziók a termék kiadási dátum együtt jelennek meg. A támogatás lejárati és kivezetési dátum is biztosít, ha azok már ismert.

### <a name="available-versions"></a>Elérhető verzió

Az alábbi táblázat az Azure Portalon, valamint egyéb telepítési módszerek, például a PowerShell és a .NET SDK-val a HDInsight verziói.

| HDInsight-verzió | HDP verzió | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Visszavonás dátuma | Magas rendelkezésre állás |  Rendelkezésre állás az Azure Portalon | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 <br> (Előzetes verzió) |HDP 3.0 |Ubuntu 16.0.4 LTS |2018. szeptember 24. | | |Igen |Igen |
| HDInsight 3.6-os |2.6 HDP |Ubuntu 16.0.4 LTS |2017. április 4. | | |Igen |Igen |
| HDInsight 3.5-ös verzióját. <br> (Spark) * |2.6 HDP |Ubuntu 16.0.4 LTS |2016. szeptember 30. |2019. márciusi 13. |2019. márciusi 13. |Igen |Igen |

*&ast; HDInsight 3.5-ös támogatási ki lett terjesztve, csak a Spark-fürt típusa*

> [!NOTE]
> Támogatási után a lejárt verziót, nem lehet elérhető a Microsoft Azure-portálon keresztül. Azonban fürtverziók továbbra is elérhetők az a `Version` paramétert a Windows PowerShellben [New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) parancs, és a .NET SDK, amíg a verzió kivezetési dátuma.
>

### <a name="retired-versions"></a>Elavult verzió

A következő táblázat felsorolja a verziókat HDInsight **nem** elérhető az Azure Portalon.

| HDInsight-verzió | HDP verzió | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Visszavonás dátuma | Magas rendelkezésre állás |  Rendelkezésre állás az Azure Portalon | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5-ös verzióját. <br> (Nem Spark) |HDP 2,5 |Ubuntu 16.0.4 LTS |2016. szeptember 30. |2017. szeptember 5. |2018. június 28. |Igen |Nem |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016. március 29-én |2016. december 29-én |2018. január 9. |Igen |Nem |
| HDInsight 3.3. |HDP 2.3 |Windows Server 2012 R2 |2015. december 2. |2016. június 27. |2018. július 31. |Igen |Nem |
| HDInsight 3.3. |HDP 2.3 |Ubuntu 14.0.4 LTS |2015. december 2. |2016. június 27. |2017. július 31-ig. |Igen |Nem |
| HDInsight 3.2-es verzióját |2.2-ES HDP |Ubuntu 12.04 LTS, vagy a Windows Server 2012 R2 rendszerben |2015. február 18. |2016. március 1-én |2017. április 1. |Igen |Nem |
| HDInsight 3.1. |HDP 2.1-ES VERZIÓJA |Windows Server 2012 R2 |2014. június 24-én |2015. május 18. |2016. június 30. |Igen |Nem |
| HDInsight 3.0-s |HDP 2.0 |Windows Server 2012 R2 |2014. február 11. |2014. szeptember 17-én |2015. június 30. |Igen |Nem |
| HDInsight 2.1-es verziója |1.3 HDP |Windows Server 2012 R2 |2013. október 28. |2014. május 12. |2015. május 31-ig. |Igen |Nem |
| HDInsight 1.6-os |1.1 HDP | |2013. október 28. |2014. április 26. |2015. május 31-ig. |Nem |Nem |

> [!NOTE]
> Két fő csomópont a magas rendelkezésre állású fürtök HDInsight verzió a 2.1-es és újabb verziók esetében alapértelmezés szerint települnek. Ezek nem érhető el a HDInsight-fürtökben 1.6-os.

## <a name="enterprise-security-package-for-hdinsight"></a>A HDInsight vállalati biztonsági csomag

Vállalati biztonsági csomag egy nem kötelező, amelyeket hozzáadhat a HDInsight-fürt létrehozása a fürt munkafolyamat részeként. A vállalati biztonsági csomag támogatja:

- Integráció az Active Directory-hitelesítéshez.

    Korábban csak egy helyi rendszergazdai felhasználó és a helyi SSH-felhasználó hozhat létre HDInsight-fürtök. A helyi rendszergazda felhasználó is hozzáférhet, minden a fájlok, mappák, táblákat és oszlopokat.  A vállalati biztonsági csomaggal, engedélyezheti a szerepköralapú hozzáférés-vezérlés integrálásával a HDInsight-fürtök a saját Active Directoryval, többek között a helyszíni Active Directory, Azure Active Directory Domain Services vagy az Active Directory IaaS-on a virtuális gép. A fürtön a tartományi rendszergazda biztosíthat a felhasználók számára, aki a fürtöt a saját vállalati (tartomány)-felhasználónevet és jelszót használja. 

    További információkért lásd:

    - [Hadoop-biztonság használatába a tartományhoz csatlakoztatott HDInsight-fürtök bemutatása](./domain-joined/apache-domain-joined-introduction.md)
    - [Az Azure tartományhoz csatlakoztatott Hadoop-fürtök tervezése a HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Tartományhoz csatlakoztatott tesztkörnyezet konfigurálása](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurálja a tartományhoz csatlakoztatott HDInsight-fürtöket az Azure Active Directory Domain Services használatával](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Engedélyezési adatok

    - Integráció az Apache Ranger Hive, a Spark SQL és a Yarn-várólisták engedélyezési.
    - Beállíthatja a fájlokra és mappákra vonatkozó hozzáférés-vezérlés.

    További információkért lásd:

    - [A tartományhoz csatlakoztatott HDInsight Hive-házirendek konfigurálása](./domain-joined/apache-domain-joined-run-hive.md)

- A vizsgálati naplók és a figyelő hozzáfér a konfigurált szabályzatok megtekintése. 

### <a name="supported-cluster-types"></a>Támogatott fürttípusok

Jelenleg csak a következő fürttípus támogatja a vállalati biztonsági csomag:

- Hadoop (csak a 3.6. HDInsight)
- Spark
- Interaktív lekérdezés

### <a name="support-for-azure-data-lake-store"></a>Az Azure Data Lake Store támogatása

A vállalati biztonsági csomag támogatja az Azure Data Lake Store használatával az elsődleges tárolási és a kiegészítő tárterület is.

### <a name="pricing-and-sla"></a>Díjszabás és SLA
A vállalati biztonsági csomag díjszabással és a szolgáltatásiszint-szerződés kapcsolatos információkért lásd: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>A windowsos HDInsight kivonása
A Microsoft Azure HDInsight 3.3-as verziója lett a HDInsight, a Windows előző verzióját. A kivezetési dátum, a HDInsight, a Windows rendszer 2018. július 31-ig. Ha minden HDInsight-fürtök a Windows 3.3-as vagy korábbi, át kell telepítenie a Linux (HDInsight 3.5-ös vagy újabb verziójú) HDInsight előtt 2018. július 31-ig. A Linux OS-ba való migrálás lehetővé teszi lehetővé a létrehozása vagy a HDInsight-fürtök méretezése megőrzése. A HDInsight 3.3-as, a Windows verzió támogatása 2016. június 27-én lejárt.

A HDInsight 3.4-es verziójú verziótól kezdődően a Microsoft közzétette a Linux operációs rendszer csak a HDInsight. Ennek eredményeképpen a HDInsight-összetevőinek néhány érhető el Linux rendszeren csak. Ezek közé tartozik az Apache Ranger, Kafka, interaktív lekérdezés, Spark, a HDInsight-alkalmazások, és az Azure Data Lake Store fájlrendszer elsődleges. A Linux operációs rendszer csak a HDInsight későbbi kiadásaiban érhetők el. Nem HDInsight, a Windows későbbi verzióiban lesz. 

## <a name="faqs"></a>Gyakori kérdések

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Mi a HDInsight eltávolítása Windows ütemtervét?
2018. július 31-ig lesz a HDInsight, a Windows kivezetési dátuma. Ha a tervezett kivezetési dátum nem ez az Ön régiójában, külön-külön értesítést. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Mi a HDInsight Windows eltávolítása a meglévő ügyfeleknek hatását?
A Windows HDInsight kivonják a forgalomból, miután nem HDInsight Windows-fürtöt létrehozni, vagy egy meglévő HDInsight Windows-fürt méretezése. A HDInsight 3.3-as verzió támogatása 2016. június 27-én lejárt. Ezért nincs támogatási vagy hibajavítások a HDInsight 3.3-as vagy régebbi verziójú. A Linux operációs rendszer csak a HDInsight későbbi kiadásaiban érhetők el. Nem HDInsight, a Windows későbbi verzióiban lesz.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>HDInsight a Windows mely verziói vannak hatással?
Az Azure HDInsight 3.3-as verziója az HDInsight a Windows előző verziója. A Windows HDInsight kivonják a forgalomból, mielőtt az összes Windows HDInsight fürtök 3.3-as verziójának vagy korábbi verzióját a HDInsight 3.5-ös vagy újabb verzió Linux rendszeren kell áttelepíteni. HDInsight Linux rendszeren, a fürtök áttelepítése lehetővé teszi új fürtök létrehozása vagy meglévő fürtök méretezése megőrzése. 

### <a name="what-do-i-need-to-do"></a>Mit kell tennem?
Egy támogatott HDInsight Linux-fürt a HDInsight Windows-fürtök áttelepítése előtt 2018. július 31-ig. További információ: a [HDInsight dokumentu migrace](hdinsight-migrate-from-windows-to-linux.md). Az Azure HDInsight-verziókkal kapcsolatos részletekért lásd: listájának [támogatott verziók](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Hol találom meg a fürt operációs rendszer típusa?
Az Azure Portalon nyissa meg a HDInsight-fürt – áttekintés oldalra, és keresse meg **fürt típusa** alatt **Essentials**. A fürt operációs rendszer típusa az adott oldalon láthatók. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>E nem telepíthető át HDInsight Linux-fürt által 2018. július 31-ig. Mi a HDInsight Windows-fürtbe gyakorolt?
A HDInsight Windows-fürt fut,-van, de nem hozható létre egy új HDInsight Windows-fürt, vagy egy meglévő HDInsight Windows-fürt méretezése. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Fürt rendelkezik egy .NET-függőséget. Hogyan oldhatom fel ezt a függőséget Linux rendszeren?
A Linux-fürt függőségi használatával oldható meg a [monó projekt](http://www.mono-project.com/). Ezen .NET nyílt forráskódú megvalósítása a HDInsight Linux-fürtök esetén érhető el. További információ: a [HDInsight dokumentu migrace](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>A HDInsight, a Windows egy új ügyfél vagyok. Hogyan hozható létre egy HDInsight Windows-fürtöt?
2017. július 3, meglévő HDInsight Windows ügyfelek hozhat létre új HDInsight Windows fürtök. Új ügyfeleket nem hozható létre egy HDInsight Windows-fürtön az Azure Portalon PowerShell vagy az SDK használatával. Azt javasoljuk, hogy az új ügyfelek hozzon létre egy Linux rendszerű HDInsight-fürtöt. Meglévő ügyfeleinknek létrehozhatnak új HDInsight Windows fürtök a kivezetési dátum Windows a HDInsight-ig. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>A számlázott díjra társított áthelyezését a HDInsight Windows a HDInsight Linux rendszeren van?
Nem, a díjszabása megegyezik a HDInsight vagy operációs rendszereken. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Mik a csak a HDInsight linuxon való társított ügyfél előnyei?
* Gyorsabb-piacra nyílt forráskódú big data-technológiák a HDInsight szolgáltatással
* Egy nagy közösségi és támogatási ökoszisztéma
* Azon képessége, hogy a Hadoop és más big data-technológiák a nyílt forráskódú Közösség aktív fejlesztés

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>Biztosítja a HDInsight Linux rendszeren érhető el az Windows a HDInsight további funkciókat?
A HDInsight 3.4-es verziójú verziótól kezdődően a Microsoft közzétette a Linux operációs rendszer csak a HDInsight. Ennek eredményeképpen a HDInsight-összetevőinek néhány érhető el Linux rendszeren csak. Ezek közé tartozik az Apache Ranger, Kafka, interaktív lekérdezés, Spark, a HDInsight-alkalmazások, és az Azure Data Lake Store fájlrendszer elsődleges. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight-fürt verziók szolgáltatásiszint-szerződés
A szolgáltatásiszint-szerződés (SLA) van meghatározva, hogy egy _támogatási ablak_. A támogatási időszak az az időtartam, a Microsoft ügyfélszolgálata és a egy HDInsight-fürt verziója támogatott. A verzió-e egy _támogatja a lejárati dátum_ , amely megfelelt, a HDInsight-fürt a támogatási időszakon kívül. További információ a támogatott verziók listájának megtekintéséhez [támogatott HDInsight-fürt verziók](hdinsight-migrate-from-windows-to-linux.md). A megadott verzió (miután elérhetővé vált egy újabb X + 1) X HDInsight támogatás lejárati dátuma számítjuk ki, hogy a későbbi a:  

* Képlet 1: 180 nap hozzáadása a dátum, amikor a HDInsight-fürt verziója X fel lett oldva.
* Képlet 2: 90 nap hozzáadása a dátum, amikor a HDInsight-fürt verziója X + 1 szeretné elérhetővé tenni az Azure Portalon.

A _kivezetési dátum_ az a dátum, amely után a fürt verziója nem hozható létre a HDInsight. 2017. július 31-én kezdve a kivezetési dátum után egy HDInsight-fürt nem méretezhető. 

> [!NOTE]
> HDInsight Windows-fürtök (beleértve verzió 2.1-es, 3.0-s, 3.1, 3.2-es és 3.3-as) futtatása Azure Vendég operációsrendszer-család 4-es verzió, amely a Windows Server 2012 R2 64 bites verzióját használja. Azure vendég operációs rendszerek 4. verziójú támogatja a .NET-keretrendszer 4.0-s, 4.5-ös, 4.5.1 és 4.5.2-es.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks kibocsátási megjegyzéseket, társított a HDInsight-verziók

A szakasz a kibocsátási megjegyzések a Hortonworks Data Platform disztribúcióiról, valamint a használt HDInsight Apache-összetevők a mutató hivatkozásokat tartalmaz.
* HDInsight fürt 4.0-s verzióját használja egy Hadoop-változat alapján [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* HDInsight fürt 3.6-os verzióját használja egy Hadoop-változat alapján [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Fürtverzió HDInsight 3.5-ös verzióját használja egy Hadoop-változat alapján [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). A HDInsight fürt 3.5-ös verziója a _alapértelmezett_ az Azure Portalon létrehozott Hadoop-fürtöt.
* HDInsight fürt 3.4-es verziójú használ egy Hadoop-változat alapján [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* HDInsight-fürt verziója 3.3-as használ egy Hadoop-változat alapján [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Az Apache Storm kibocsátási megjegyzések](https://storm.apache.org/2015/11/05/storm0100-released.html) a Apache webhelyen érhető el.
  * [Kibocsátási megjegyzések az Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) az Apache webhelyen érhető el.
* HDInsight fürt 3.2-es verziójú használ egy Hadoop-változat alapján [Hortonworks Data Platform 2.2][hdp-2-2].

  * Kibocsátási megjegyzések a meghatározott Apache-összetevők a következők érhető el: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [közös](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [0.9.3-as Storm](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), és [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight-fürt verziója 3.1-et használ egy Hadoop-változat alapján [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Létrehozott, 7, 2014. November előtti HDInsight 3.1-fürtök alapuló [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* HDInsight fürt 3.0-s verzió használ egy Hadoop-változat alapján [Hortonworks Data Platform 2.0][hdp-2-0-8].
* HDInsight fürt 2.1-es verzió használ egy Hadoop-változat alapján [Hortonworks Data Platform 1.3][hdp-1-3-0].
* HDInsight-fürt verziója 1.6-os használ egy Hadoop-változat alapján [Hortonworks Data Platform 1.1][hdp-1-1-0].






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Alapértelmezett csomópont konfigurációs és virtuális gépek méretei fürtök
Az alábbi táblázatok sorolják fel a HDInsight-fürtök esetében az alapértelmezett virtuális gép (VM) méretek.

> [!IMPORTANT]
> Ha egy fürtben több mint 32 feldolgozó csomópontokat, jelöljön ki egy fő csomópontméretet legalább 8 maggal és 14 GB RAM.
> 
> 

* Az összes támogatott, kivéve Dél-Brazíliát és Nyugat-japán régióban:

  | Fürttípus | Hadoop | HBase | Interaktív lekérdezés | Storm | Spark | Machine Learning-kiszolgáló |
  | --- | --- | --- | --- | --- | --- | --- |
  | A fő: virtuális gépek alapértelmezett mérete |D3 v2 |D3 v2 | D13, D14 |A4 v2 |D12 v2 |D12 v2 |
  | A fő: javasolt Virtuálisgép-méretek |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |A4 v2, A8 v2, A2m v2 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Feldolgozó: virtuális gépek alapértelmezett mérete |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Feldolgozó: javasolt Virtuálisgép-méretek |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2 és D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2 és D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: virtuális gépek alapértelmezett mérete | |A4 v2 | |A2 v2 | | |
  | ZooKeeper: javasolt Virtuálisgép-méretek | |A4 v2, A8 v2, A2m v2 | | A2 v2, A4 v2, A8 v2 | | |
  | Edge: virtuális gépek alapértelmezett mérete | | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Edge: Virtuálisgép-méretet ajánlott | | | | | |Windows: D12 v2, D13 v2 és D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Dél-Brazíliát és Nyugat-Japánban csak (nincs v2 méretű):

  | Fürttípus | Hadoop | HBase | Interaktív lekérdezés |Storm | Spark | Machine Learning szolgáltatás |
  | --- | --- | --- | --- | --- | --- | --- |
  | A fő: virtuális gépek alapértelmezett mérete |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | A fő: javasolt Virtuálisgép-méretek |D4, D3, D12 CSOMAG |D4, D3, D12 CSOMAG  | D13, D14 |A3, A4, A5 CSOMAG |D12, D13, D14 |D12, D13, D14 |
  | Feldolgozó: virtuális gépek alapértelmezett mérete |D3 |D3  | D13, D14 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Feldolgozó: javasolt Virtuálisgép-méretek |D4, D3, D12 CSOMAG |D4, D3, D12 CSOMAG  | D13, D14 |D4, D3, D12 CSOMAG |Windows: D12, D13, D14; Linux: D4, D13, D12, D14 |Windows: D12, D13, D14; Linux: D4, D13, D12, D14 |
  | ZooKeeper: virtuális gépek alapértelmezett mérete | |A2 | | A2 | | |
  | ZooKeeper: javasolt Virtuálisgép-méretek | |A2, A3, A4 | |A2, A3, A4 | | |
  | Edge: alapértelmezett Virtuálisgép-méretek | | | | | |Windows: D12; Linux: D4 |
  | Edge: javasolt Virtuálisgép-méretek | | | | | |Windows: D12, D13, D14; Linux: D4, D13, D12, D14 |

> [!NOTE]
> - A fő néven *Nimbus* a Storm a fürt típusát.
> - Feldolgozó néven *felügyelő* a Storm a fürt típusát.
> - Feldolgozó néven *régió* a hbase-fürt típusa.

## <a name="next-steps"></a>További lépések
- [A telepítő a Hadoop, Spark- és a HDInsight-fürt](hdinsight-hadoop-provision-linux-clusters.md)
- [Hadoop on HDInsight az olyan Windows PC-s verziójában](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
