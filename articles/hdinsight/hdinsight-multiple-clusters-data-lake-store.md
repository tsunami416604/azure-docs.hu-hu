---
title: "Több HDInsight-fürt használata az Azure Data Lake Store-fiók – Azure |} Microsoft Docs"
description: "Egynél több HDInsight-fürt használata egy Data Lake Store-fiókkal"
keywords: "hdinsight-tárolóba, a hdfs, a strukturált adatok, a strukturálatlan adatok, a data lake store"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: c306c66354f34fc945a5fe0ffa11d63bce4d7005
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Több HDInsight-fürt használata az Azure Data Lake Store-fiók

HDInsight 3.5-ös verziója verziótól kezdődően hozhat létre HDInsight-fürtök az Azure Data Lake Store-fiók, az alapértelmezett fájlrendszer.
Data Lake Store támogatja a korlátlan tárterület, így ideális, ha nem csak futtató nagy mennyiségű adat; de is üzemeltetéséhez több HDInsight-fürtök ennek a megosztásnak a egyetlen Data Lake Store-fiókból. Instructionson hogyan HDInsight-fürtök létrehozása a Data Lake Store áruházból, a tárolóként, lásd: [HDInsight-fürtök létrehozása a Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Ez a cikk ismerteti a Data Lake ajánlásokat rendszergazda tárolja egyetlen és megosztott Data Lake tárolásához fiók beállítása, amely több használható **aktív** a HDInsight-fürtök. Ezek a javaslatok alkalmazása több biztonságos, valamint a nem biztonságos Hadoop-fürtök megosztott Data Lake store-fiók a futtató.


## <a name="data-lake-store-file-and-folder-level-acls"></a>Data Lake Store-fájl- és hozzáférés-vezérlési listák szinten

Ez a cikk többi feltételezi, hogy rendelkezik-e a fájl- és szintű hozzáférés-vezérlési listák jó ismerete, részletes leírását lásd az Azure Data Lake Store a [hozzáférés-vezérlés az Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Több HDInsight-fürtök a Data Lake Store-telepítő
Ossza meg velünk igénybe vehet egy kétszintű mappahierarchia ismertetik a javaslatok mutilple a HDInsight-fürtök használata a Data Lake Store-fiók. Vegye figyelembe a rendelkezik egy Data Lake Store-fiókkal, és a gyökérmappa-szerkezetében **/fürtök/pénzügyi**. Ez a struktúra a pénzügyi szervezet által igényelt összes fürt segítségével /clusters/finance tárolási helyként. A jövőbeli, ha egy másik szervezet mondja ki a Marketing, hozzon létre HDInsight-fürtök ugyanazt a Data Lake Store-fiókot, akkor létrehozhat a/fürtök/marketing szeretné. Most tegyük használja **/fürtök/pénzügyi**.

Ahhoz, hogy a gyökérmappa-szerkezetében hatékonyan használhatják a HDInsight-fürtök, a Data Lake Store rendszergazdájának kell megadnia megfelelő engedélyekkel a táblázatban ismertetett módon. Hozzáférés-vezérlési listák és a nem alapértelmezett-hozzáférés-vezérlési listák megfelelnek az engedélyeket a táblázatban látható. 


|Mappa  |Engedélyek  |Tulajdonos felhasználó  |Tulajdonoscsoport  | Nevesített felhasználó | Nevesített felhasználó engedélyek | Nevesített csoport | Elnevezett csoportját engedélyek |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |adminisztrátor |adminisztrátor  |Szolgáltatásnév |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |adminisztrátor |adminisztrátor |Szolgáltatásnév |--x  |FINGRP |r-x         |
|/ fürtök/pénzügyi | rwxr-x--t |adminisztrátor |FINGRP  |Szolgáltatásnév |rwx  |-  |-     |

A következő táblában:

- **felügyeleti** létrehozó és a Data Lake Store-fiók rendszergazdája.
- **Szolgáltatás egyszerű** van a fiókhoz tartozó egyszerű Azure Active Directory (AAD) szolgáltatást.
- **FINGRP** az aad-ben, amely a pénzügyi szervezet felhasználóinak tartalmazza létrehozott felhasználói csoport.

További információ a egy AAD-alkalmazást, (amelyek is létrehoz egy egyszerű szolgáltatásnév) létrehozása [hozzon létre egy AAD-alkalmazást](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Felhasználói csoport létrehozása az aad-ben, lásd: [csoportkezelés az Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Figyelembe kell venni néhány kulcs mutat.

- A két szinten mappaszerkezet (**/fürtök/pénzügyi/**) kell létrehozni, majd a megfelelő engedélyekkel a Data Lake Store-rendszergazda által kiosztott **előtt** fürtök a storage-fiók használatával. Ez a struktúra nem készül automatikusan fürtök létrehozása során.
- A fenti példa azt javasolja, hogy a tulajdonos csoportja beállítás **/fürtök/pénzügyi** , **FINGRP** és lehetővé teszi a **r-x** FINGRP való hozzáférést a teljes mappahierarchia indítása a legfelső szintű. Ez biztosítja, hogy FINGRP tagjai navigálhatnak a mappaszerkezet legfelső szintű kezdve.
- Abban az esetben, ha különböző AAD Szolgáltatásnevekről hozhat létre a fürtök **/fürtök/pénzügyi**, a kapcsolódó bites (Ha be a **pénzügyi** mappa) biztosítja, hogy a mappák létrehozása egy szolgáltatásnév nem lehet törölni, a többi.
- Miután a gyökérmappa-szerkezetében és az engedélyek vannak érvényben, HDInsight fürt létrehozási folyamata hoz létre a fürt-specifikus tárolási helynek a **/fürtök/pénzügyi/**. A név fincluster01 fürt tárolóhelyét lehet például **/clusters/finance/fincluster01**. A tulajdonosi és a HDInsight-fürt által létrehozott mappákra vonatkozó engedélyek Itt a táblázatban látható.

    |Mappa  |Engedélyek  |Tulajdonos felhasználó  |Tulajdonoscsoport  | Nevesített felhasználó | Nevesített felhasználó engedélyek | Nevesített csoport | Elnevezett csoportját engedélyek |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Egyszerű szolgáltatásnév |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Javaslatok feladat bemeneti és kimeneti adatok

Azt javasoljuk, hogy a feladat egy feladatot, és a kimenetek bemeneti adatok tárolja egy mappát kívül **/fürtök**. Ez biztosítja, hogy akkor is, ha a fürt-specifikus mappa néhány tárolóhely visszaigénylésének törlődik, a feladatot bemenetekhez és kimenetekhez továbbra is elérhetők a későbbi használat érdekében. Ilyen esetben győződjön meg arról, hogy a feladatot bemenetekhez és kimenetekhez tárolásához mappahierarchia lehetővé teszi, hogy megfelelő szintű hozzáférést a szolgáltatásnevet.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Egyetlen tárfiók megosztása fürtökön korlátozása

A fürtök, megoszthat egy Data Lake Store-fiók számára vonatkozó korlátozást az e-fürtökön futó munkaterhelés függ. Túl sok fürtök vagy nagyon nehéz munkaterhelések rendelkező a fürtökön, amelyek egy tárfiókot, előfordulhat, hogy a tárolási fiók be-és kilépési az beszerzése szabályozva.

## <a name="support-for-default-acls"></a>Alapértelmezett – ACL-ek támogatása

Ha egy egyszerű szolgáltatás létrehozása nevű felhasználói hozzáférést (a fenti táblázatban látható), javasoljuk **nem** a nevű-felhasználót egy alapértelmezett ACL hozzáadása. Üzembe helyezési nevű felhasználói hozzáférés alapértelmezett-hozzáférés-vezérlési listák eredmények 770 engedélyek hozzárendelése a tulajdonos felhasználó, a tulajdonos csoport és mások számára. 770 az alapértelmezett értéke nem jár engedélyeket (7) tulajdonos vagy a tulajdonos-csoport (7), amíg tart a számítógépnél összes engedélyt, mások (0). Ez egy ismert probléma az egy adott használati eset részletesen ismertetett eredményez a [ismert problémák és megoldások](#known-issues-and-workarounds) szakasz.

## <a name="known-issues-and-workarounds"></a>Ismert problémák és megoldások

Ez a rész felsorolja az ismert problémák a HDInsight használata a Data Lake Store, és azok megoldását ismerteti.

### <a name="publicly-visible-localized-yarn-resources"></a>Nyilvánosan látható honosított YARN erőforrások

Egy új Azure Data Lake store-fiók létrehozásakor a gyökérkönyvtár automatikusan létrehozza a rendszer a hozzáférés-ACL engedélybitjei 770 értékre. A legfelső szintű mappa birtokló felhasználói fiók (a Data Lake Store admin) létrehozó felhasználó van beállítva, és a tulajdonos csoport beállítása a fiókot létrehozó felhasználó az elsődleges csoportja számára. Nem lehet hozzáférni az "egyéb" valósul meg.

Ezek a beállítások ismert, hogy egy adott HDInsight használati eset rögzített hatással [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Feladatok elküldésének sikertelen lehet a következőhöz hasonló hibaüzenettel:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Ahogy az a YARN JIRA kapcsolódó korábbi, azaz nyilvános erőforrásokat, miközben a lokalizáló ellenőrzi, hogy a kért erőforrások valóban nyilvános rájuk vonatkozó engedélyek a távoli fájlrendszerben ellenőrzésével. Bármely, amelyek nem felelnek meg, hogy a feltétel LocalResource honosításhoz elutasítva. Az engedélyek ellenőrzése "egyéb" olvasási jogosultsággal a fájl tartalmazza. Ebben a forgatókönyvben nem működik, a-kész esetén a HDInsight-fürtök az Azure Data Lake, mivel az Azure Data Lake összes megtagadja "egyéb" mappa gyökérszinten.

#### <a name="workaround"></a>Megkerülő megoldás
Set olvasási-végrehajtási engedélyeinek **mások** keresztül a hierarchiában, például  **/** , **/fürtök** és   **/fürtök/pénzügyi** a fenti táblázatban látható módon.

## <a name="see-also"></a>Lásd még

* [HDInsight-fürtök létrehozása a Data Lake Store tárolóként](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


