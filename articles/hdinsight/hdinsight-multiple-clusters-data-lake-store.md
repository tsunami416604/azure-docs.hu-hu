---
title: Több HDInsight-fürt & egy Azure Data Lake Storage-fiókkal
description: Megtudhatja, hogyan használhat egynél több HDInsight-fürtöt egyetlen Data Lake Storage-fiókkal
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: cc67acca11e7e0f24dc0597dcd19672a38a7bf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495748"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Több HDInsight-fürt használata Azure Data Lake Storage-fiókkal

A HDInsight 3.5-ös verziójától kezdve hdinsight-fürtöket hozhat létre alapértelmezett fájlrendszerként az Azure Data Lake Storage-fiókokkal.
A Data Lake Storage korlátlan tárhelyet támogat, ami ideálissá teszi nem csak nagy mennyiségű adat tárolására; hanem több HDInsight-fürt üzemeltetéséhez is, amelyek egyetlen Data Lake storage-fiókkal rendelkeznek. A HDInsight-fürt tárolóként való létrehozásáról a Rövid [útmutató: Fürtök beállítása a HDInsightban](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)című témakörben talál útmutatást.

Ez a cikk javaslatokat nyújt a Data Lake Storage rendszergazdájának egy egyetlen és megosztott Data Lake storage-fiók beállításához, amely több **aktív** HDInsight-fürtön használható. Ezek a javaslatok több biztonságos és nem biztonságos Apache Hadoop-fürtök üzemeltetésére vonatkoznak egy megosztott Data Lake Storage-fiókon.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage fájl- és mappaszintű ACL-k

A cikk további részében azt feltételezi, hogy jól ismeri a fájl- és mappaszintű Hozzáférés-vezérlést az Azure Data Lake Storage szolgáltatásban, amelyet részletesen ismertetett az [Azure Data Lake Storage hozzáférés-vezérlési szolgáltatásában.](../data-lake-store/data-lake-store-access-control.md)

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>A Data Lake Storage beállítása több HDInsight-fürthöz

Használjunk egy kétszintű mappahierarchiát, hogy elmagyarázzuk a több HDInsight-fürt Data Lake Storage-fiókkal való használatára vonatkozó javaslatokat. Vegyük például a Data Lake Storage-fiókot a **/clusters/finance**mappastruktúrával. Ezzel a struktúrával a pénzügyi szervezet által igényelt összes fürt használhatja a /clusters/finance kapcsolót tárolóhelyként. A jövőben, ha egy másik szervezet, például a Marketing, hdinsight-fürtöket szeretne létrehozni ugyanazzal a Data Lake Storage-fiókkal, létrehozhatnak /fürtöket/marketinget. Most, csak használja **/ klaszterek / finanszírozás**.

Ahhoz, hogy a HDInsight-fürtök hatékonyan lehessen használni ezt a mappastruktúrát, a Data Lake Storage rendszergazdájának hozzá kell rendelnie a megfelelő engedélyeket a táblázatban leírtak szerint. A táblázatban látható engedélyek az Access-ACL-ok, és nem az alapértelmezett Hozzáférés-hozzáférés-hozzáférés-hozzáférés-engedélyek.

|Mappa  |Engedélyek  |Tulajdonos felhasználó  |Tulajdonoscsoport  | Elnevezett felhasználó | Elnevezett felhasználói engedélyek | Elnevezett csoport | Elnevezett csoport engedélyei |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x---x  |admin |admin  |Szolgáltatásnév |--x  |FINGRP között   |r-x         |
|/fürtök | rwxr-x---x |admin |admin |Szolgáltatásnév |--x  |FINGRP között |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP között  |Szolgáltatásnév |Rwx  |-  |-     |

A táblázatban,

- **rendszergazda** a Data Lake Storage-fiók létrehozója és rendszergazdája.
- **Egyszerű szolgáltatás** a fiókhoz társított Azure Active Directory (AAD) egyszerű szolgáltatás.
- **A FINGRP** az AAD-ben létrehozott felhasználói csoport, amely a Pénzügyi szervezet felhasználóit tartalmazza.

Az AAD-alkalmazások létrehozásáról (amely egyszerű szolgáltatásszolgáltatást is létrehoz) a [Létrehozás AAD-alkalmazáscímű](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)témakörben talál. Az AAD-ben a csoportok kezelése az [Azure Active Directoryban](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)című témakörben talál további tudnivalókat arról, hogyan hozhat létre felhasználói csoportot.

Néhány kulcsfontosságú szempont, hogy fontolja meg.

- A kétszintű mappastruktúrát (**/clusters/finance/**) a Data Lake Storage rendszergazdájának kell létrehoznia és kiépítenie a fürttárfiók használata **előtt.** Ez a struktúra nem jön létre automatikusan fürtök létrehozása közben.
- A fenti példa azt javasolja, hogy a **/clusters/finance** tulajdonában lévő csoportot **fingrp-ként** tassak, és engedélyezze az **r-x** hozzáférést a FINGRP-hez a teljes mappahierarchiához a gyökértől kezdve. Ez biztosítja, hogy a FINGRP tagjai a gyökértől kezdve navigálhassanak a mappastruktúrában.
- Abban az esetben, ha különböző AAD szolgáltatásnévi tagok hozhatnak létre fürtöket a **/clusters/finance**alatt, a ragadós bit (a **pénzügyi** mappában beállítás esetén) biztosítja, hogy az egyik egyszerű szolgáltatás által létrehozott mappákat a másik ne tudja törölni.
- Miután a mappastruktúra és az engedélyek a helyükön vannak, a HDInsight-fürt létrehozási folyamata fürtspecifikus tárolóhelyet hoz létre a **/clusters/finance/** alatt. A fincluster01 nevű fürt tárolója például lehet **/clusters/finance/fincluster01.** A HDInsight-fürt által létrehozott mappák tulajdonjoga és engedélyei az itt található táblázatban láthatók.

    |Mappa  |Engedélyek  |Tulajdonos felhasználó  |Tulajdonoscsoport  | Elnevezett felhasználó | Elnevezett felhasználói engedélyek | Elnevezett csoport | Elnevezett csoport engedélyei |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Szolgáltatásnév |FINGRP között  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Javaslatok a feladatbeviteli és kimeneti adatokhoz

Azt javasoljuk, hogy a bemeneti adatokat a feladatba, és a kimenetek egy feladat tárolja a mappát kívül **/clusters**. Ez biztosítja, hogy még akkor is, ha a fürt-specifikus mappa törlődik, hogy visszaszerezze néhány tárhelyet, a feladat bemenetek és kimenetek továbbra is elérhető a későbbi használatra. Ebben az esetben győződjön meg arról, hogy a feladatbemenetek és -kimenetek tárolására szolgáló mappahierarchia lehetővé teszi a szolgáltatásnév megfelelő szintű hozzáférést.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Egyetlen tárfiókot megosztó fürtök korlátozása

Az egyetlen Data Lake Storage-fiók megosztására képes fürtök számának korlátja a fürtökön futtatott számítási feladatoktól függ. Ha túl sok fürtvagy nagyon nagy számítási feladatok vannak a fürtön, amelyek egy tárfiókkal rendelkeznek, a tárfiók be-/kimenő forgalom hozhatja a szabályozott.

## <a name="support-for-default-acls"></a>Alapértelmezett ACL-ek támogatása

Ha névvel ellátott felhasználói hozzáféréssel rendelkező egyszerű szolgáltatáslétrehozása történik (a fenti táblázatban látható módon), azt javasoljuk, hogy **ne** adja hozzá a megnevezett felhasználót az alapértelmezett Hozzáférés-jegyzékhez. A névvel ellátott-felhasználó hozzáférés alapértelmezett Hozzáférés-ellenőrzései használatával történő kiépítése 770 engedély hozzárendelését eredményezi a felhasználó, a saját csoport és mások számára. Bár ez az alapértelmezett 770-es érték nem veszi el az engedélyeket a tulajdonában lévő felhasználótól (7) vagy a saját csoporttól (7), elveszi a többi engedély (0) összes engedélyét. Ez egy ismert problémát eredményez egy adott használati esettel, amelyet részletesen az [Ismert problémák és kerülő megoldások](#known-issues-and-workarounds) szakaszban tárgyalunk.

## <a name="known-issues-and-workarounds"></a>Ismert problémák és kerülő megoldások

Ez a szakasz a HDInsight Data Lake Storage szolgáltatással való használatának ismert problémáit és azok kerülő megoldásait sorolja fel.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Nyilvánosan látható lokalizált Apache Hadoop YARN erőforrások

Új Azure Data Lake Storage-fiók létrehozásakor a gyökérkönyvtár automatikusan kiépítésre kerül az Access-ACL engedélybitek 770-re állítva. A gyökérmappa saját felhasználója a fiókot létrehozó felhasználó (a Data Lake Storage-rendszergazda) és a tulajdonában lévő csoport a fiókot létrehozó felhasználó elsődleges csoportjára van beállítva. A "többiek" számára nem biztosít hozzáférést.

Ezek a beállítások a [YARN 247-ben](https://hwxmonarch.atlassian.net/browse/YARN-247)rögzített HDInsight-használati esetet érintik. Az álláspályázatok sikertelenek lehetnek az ehhez hasonló hibaüzenettel:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Amint azt a YARN JIRA korábban hivatkozott, míg a honosításállami források, a localizer ellenőrzi, hogy az összes kért források valóban nyilvános ellenőrzésével engedélyeiket a távoli fájlrendszer. Minden olyan LocalResource erőforrás, amely nem felel meg ennek a feltételnek, honosításra elutasításra kerül. Az engedélyek ellenőrzése magában foglalja a fájl hoz való olvasási hozzáférést a "mások" számára. Ez a forgatókönyv nem működik a beépített HDInsight-fürtök üzemeltetésekesetén az Azure Data Lake-ben, mivel az Azure Data Lake a gyökérmappa szintjén minden hozzáférést megad a "mások" számára.

#### <a name="workaround"></a>Áthidaló megoldás

Állítson be olvasási végrehajtási engedélyeket **mások** **/** számára a hierarchián keresztül, például a , **/clusters** és **/clusters/finance** helyen, ahogy az a fenti táblázatban látható.

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Fürtök beállítása a HDInsightban](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](hdinsight-hadoop-use-data-lake-storage-gen2.md)
