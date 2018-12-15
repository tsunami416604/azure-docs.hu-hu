---
title: Több HDInsight-fürt használata az Azure Data Lake Store-fiók – Azure
description: Több HDInsight-fürt használata egy Data Lake Store-fiókkal
keywords: a hdinsight-storage, hdfs, strukturált adatok, strukturálatlan adatok, data lake store
services: hdinsight,storage
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 6b835dc300808453240dd3b9fe47957bd5feab9b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435225"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Több HDInsight-fürt használata az Azure Data Lake Store-fiók

HDInsight 3.5-ös verziója kezdve létrehozhat HDInsight-fürtök az Azure Data Lake Store-fiókok, az alapértelmezett fájlrendszer.
Data Lake Store támogatja a korlátlan tárolási, amelyek révén a szolgáltatás videóstreamelési nem csupán a nagy mennyiségű adat; üzemeltetése de üzemeltetéséhez-több HDInsight is fürtök adott megosztás egy Data Lake Store-fiók. Egy HDInsight-fürt létrehozása a Data Lake Store az tárolóként, lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Ez a cikk ismerteti a javaslatok a Data Lake tárolni a rendszergazda állít be egy önálló és megosztott Data Lake Store-fiók, amely több használható **aktív** HDInsight-fürtök. Ezekkel az ajánlásokkal üzemeltető több biztonságos, valamint a nem biztonságos Apache Hadoop-fürtöket egy megosztott Data Lake store-fiókot a alkalmazni.


## <a name="data-lake-store-file-and-folder-level-acls"></a>Data Lake Store-fájl- és hozzáférés-vezérlési listák szint

Ez a cikk többi része feltételezi, hogy a fájl- és szintű hozzáférés-vezérlési listák jó ismerete, részletes ismertetése az Azure Data Lake Store a [hozzáférés-vezérlés az Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Data Lake Store több HDInsight-fürt beállítása
Ossza meg velünk igénybe vehet egy kétszintű hierarchiájának annak magyarázata, mutilple HDInsight-fürtök használatával a Data Lake Store-fiókkal kapcsolatos ajánlások. Fontolja meg egy Data Lake Store-fiókot a gyökérmappa-szerkezetében rendelkezik **/fürtök/pénzügyi**. Ez a struktúra a pénzügyi intézmény szükséges összes fürt segítségével /clusters/finance tárolási helyeként. A jövőben, ha egy másik szervezet Tegyük fel, hogy a Marketing, kívánja létrehozni a HDInsight-fürtök ugyanazt a Data Lake Store-fiókot, azok sikerült létrehozni vagy fürtök vagy marketinges munkatársaival. Most használjuk **/fürtök/pénzügyi**.

Ahhoz, hogy a HDInsight-fürtök által ténylegesen használt mappaszerkezet, a Data Lake Store rendszergazdai engedélyeket kell rendelnie megfelelő, a táblában leírtak szerint. A táblázatban szereplő engedélyek Access-ACL-EK és ACL nem alapértelmezett felelnek meg. 


|Mappa  |Engedélyek  |Tulajdonos felhasználó  |Tulajdonoscsoport  | Nevesített felhasználó | Névvel ellátott felhasználói engedélyek | Nevesített csoportra | Elnevezett biztonságicsoport-engedélyeit |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |rendszergazda |rendszergazda  |Szolgáltatásnév |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |rendszergazda |rendszergazda |Szolgáltatásnév |--x  |FINGRP |r-x         |
|/ fürtök/Pénzügy | rwxr-x--t |rendszergazda |FINGRP  |Szolgáltatásnév |rwx  |-  |-     |

A tábla

- **rendszergazdai** a létrehozója, valamint a rendszergazda a Data Lake Store-fiók.
- **Egyszerű szolgáltatás** a fiókhoz tartozó Azure Active Directory (AAD) szolgáltatásnév van.
- **FINGRP** az aad-ben, amely tartalmazza a pénzügyi szervezet felhasználóitól származó létrehozott felhasználói csoport.

Hogyan hozhat létre egy AAD-alkalmazás (amely szintén létrehoz egy egyszerű szolgáltatást), lásd: [hozzon létre egy AAD-alkalmazás](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Az aad-beli felhasználói csoport létrehozásával kapcsolatos útmutatásért lásd: [csoportkezelés az Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Néhány alapvető szempontokat kell figyelembe venni.

- A két szint mappastruktúra (**/fürtök/pénzügyi/**) kell létrehozni és a megfelelő engedélyekkel a Data Lake Store-rendszergazda által üzembe helyezett **előtt** fürtök esetén a tárfiók használatával. Ez a struktúra nem jön automatikusan fürtök létrehozása során.
- A fenti példa javasolja a tulajdonoscsoportját beállítás **/fürtök/pénzügyi** , **FINGRP** lehetővé tevő és **r-x** FINGRP hozzáférést a teljes mappát hierarchiába indítása a legfelső szintű. Ez biztosítja, hogy FINGRP tagjai navigálhat a gyökérmappa-szerkezetében kezdve a legfelső szintű.
- Abban az esetben, ha különböző AAD-szolgáltatásnevek hozhat létre alá tartozó fürtök **/fürtök/pénzügyi**, a ragadós (ha van beállítva a a **pénzügyi** mappa) biztosítja, hogy a mappák létrehozása egy szolgáltatásnév nem lehet törölni a többi.
- A mappastruktúra és az engedélyek vannak érvényben, ha HDInsight-fürt létrehozását hoz létre a fürtre jellemző tárolási helynek alatt **/fürtök/pénzügyi/**. A név fincluster01 a fürthöz a tároló lehet például **/clusters/finance/fincluster01**. A tulajdonosi és a HDInsight-fürt által létrehozott mappákra vonatkozó engedélyek Itt a táblázatban látható.

    |Mappa  |Engedélyek  |Tulajdonos felhasználó  |Tulajdonoscsoport  | Nevesített felhasználó | Névvel ellátott felhasználói engedélyek | Nevesített csoportra | Elnevezett biztonságicsoport-engedélyeit |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Egyszerű szolgáltatás |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Javaslatok a feladat bemeneti és kimeneti adatok

Azt javasoljuk, hogy egy feladatot, és a kimenetek egy feladatot a bemeneti adatokat tárolja egy mappát kívül **/fürtök**. Ez biztosítja, hogy akkor is, ha a fürtre jellemző mappát tároló lemezterületet felszabadítása érdekében törlik, a feladatok bemeneteit és kimeneteit továbbra is elérhető a későbbi használat céljából. Ilyen esetben győződjön meg arról, hogy a feladat bemenetek és kimenetek tárolásához a mappahierarchiában lehetővé teszi, hogy megfelelő szintű hozzáférést a szolgáltatásnevet.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Korlátozza az egy tárfiókban megosztása fürtökön

Egy olyan Data Lake Store-fiók megosztására használható a fürtök számának korlátját a e-fürtökön futó munkaterhelés függ. Túl sok fürtök vagy a nagy számítási feladatok fel a fürtökön, amelyek egy storage-fiókot, előfordulhat, hogy a tárolási fiók bejövő/kimenő forgalom leszabályozza a.

## <a name="support-for-default-acls"></a>Alapértelmezett – ACL-ek támogatása

Ha egyszerű szolgáltatás létrehozása (amint az a fenti táblázatban) nevű felhasználói hozzáférést, javasoljuk, **nem** nevű-felhasználót egy alapértelmezett ACL. Kiépítési nevű felhasználói hozzáférés alapértelmezett-ACL-ek eredmények a 770 engedélyek hozzárendelése a tulajdonos felhasználó, a tulajdonos csoport és mások. 770 az alapértelmezett értéke nem sávdiagrammá engedélyeit (7) a tulajdonos-felhasználó vagy a tulajdonos-csoport (7), amíg tart azonnal összes engedélyének mások (0). Ez egy ismert probléma, amely egy adott használati eset, hogy a részleteket eredménye a [ismert problémák és megoldások](#known-issues-and-workarounds) szakaszban.

## <a name="known-issues-and-workarounds"></a>Ismert problémák és megoldások

Ez a szakasz a HDInsight a Data Lake Store és azok megoldását ismerteti az ismert hibákat sorolja fel.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Országom honosított Apache Hadoop YARN-erőforrások

Egy új Azure Data Lake store-fiók létrehozásakor a rendszer automatikusan üzembe a gyökérkönyvtár hozzáférési-ACL-JÉNEK engedély bites 770 beállítása. A gyökérmappa a tulajdonos felhasználó a felhasználó, aki létrehozta a fiókot (a Data Lake Store-rendszergazda) értékre van állítva, és a tulajdonoscsoport a felhasználó által létrehozott fiók elsődleges csoportja lesz beállítva. Nincs hozzáférés "egyéb" biztosítunk.

Ezeket a beállításokat egy adott HDInsight használati eset a rögzített érintő ismert [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Feladatok elküldésének volt sikertelen, és ehhez hasonló hibaüzenetet:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

A YARN JIRA társított korábban, miközben nyilvános erőforrások, azaz az a lokalizáló azt ellenőrzi, hogy a kért erőforrások nyilvánosak valóban az engedélyeit a távoli fájlrendszerben ellenőrzésével. Bármely, amely nem felel meg a feltételhez LocalResource a honosításra elutasítva. A jelölőnégyzet engedélyeket, az "egyéb" magában foglalja a fájl írásvédett. Ebben a forgatókönyvben nem működik – a – beépített üzemeltetése az Azure Data Lake a HDInsight-fürtök, mivel az Azure Data Lake minden megtagadja "egyéb" esetén legfelső szintű mappa szintjén.

#### <a name="workaround"></a>Áthidaló megoldás
Set olvasási-végrehajtási engedélyeket **mások** a hierarchián keresztül, például **/**, **/fürtök** és   **/fürtök/pénzügyi** a fenti táblázatban látható módon.

## <a name="see-also"></a>Lásd még

* [Gyors útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)


