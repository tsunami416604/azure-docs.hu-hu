---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: 0347d6ca951b75c385b138487f58b85a809c6805
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
Zóna redundáns tárolás (ZRS) szinkron módon replikálja az adatok három (3) tároló fürtök egy régió között. Minden egyes tárolási fürt a többi fizikailag elkülönített, és a saját rendelkezésre állási zóna (AZ) találhatók. Egyes rendelkezésre állási zóna és a ZRS fürtön belül, az autonóm, külön segédprogramok és hálózati képességek.

Az adatok tárolása a ZRS fiók biztosítja, hogy fog férnek hozzá és kezeli az adatokat, abban az esetben, ha a zóna nem érhető el. A ZRS kiváló teljesítményt és rendkívül kis késést biztosít. Valójában a ZRS rendelkezik azonos [méretezhetőségi célok](../articles/storage/common/storage-scalability-targets.md) LRS szerint.

Vegye figyelembe a ZRS akkor is, ha egy kimaradás vagy természeti katasztrófa Renderelés zonal adatközpont nem érhető el az erős konzisztencia, erős tartósság és magas rendelkezésre állást igénylő forgatókönyvek esetén. ZRS nyújt a tárolási objektum legalább 99.9999999999 % tartósságot (12 9 meg) egy adott évben.

További információ a rendelkezésre állási zónák: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Támogatást biztosít, és a regionális rendelkezésre állása
A ZRS jelenleg [ **szabványos, általános célú v2 (GPv2)** ](../articles/storage/common/storage-account-options.md#general-purpose-v2) típusú fiókra. A ZRS blokkblobokat, lapblobokat nem szabad, fájlok, táblát és üzenetsort érhető el. Emellett minden a [tárolási analitika](../articles/storage/common/storage-analytics.md) naplók és [Storage Metrics](../articles/storage/common/storage-enable-and-view-metrics.md)

A ZRS van **általánosan elérhető** a következő régióban:

- USA keleti régiója 2
- USA középső régiója
- Észak-Európa
- Nyugat-Európa
- Közép-Franciaország
- Délkelet-Ázsia

A Microsoft engedélyezése az egyéb Azure-régiók ZRS továbbra is fennáll, és frissíti ebben a listában, amikor ez történik. Például ilyen közlemények a szabványos csatornákon keresztül is végzünk a [Azure szolgáltatásfrissítések](https://azure.microsoft.com/updates/) Azure-előfizetés tulajdonosok és a rendszergazdák lap és az e-mail értesítések.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Mi történik, ha a zóna nem érhető el?

Az adatok továbbra is ellenáll, ha a zóna nem érhető el lesz. A Microsoft azt javasolja, hogy továbbra is kövesse az eljárások az átmeneti hiba kezelésre, például az exponenciális vissza off újrapróbálkozási házirendek megvalósítása. Ha a zóna nem érhető el, Azure vállalja hálózati frissítéseket, például DNS repointing. Ezek a frissítések hatással lehet az alkalmazás előtt végzi az adatok eléréséhez.

A ZRS nem előfordulhat, hogy védeni az adatokat egy regionális katasztrófa, ha több zóna véglegesen érintett ellen. Ehelyett a zrs-t kínál a rugalmassági historikus elérhetetlensége esetén az adatok. A regionális katasztrófák elleni védelem, a Microsoft javasolja használatával [georedundáns tárolás (GRS): az Azure Storage kereszt-területi replikációs](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>A ZRS replikációs konvertálása
Ma azt közérthető egyszerű közötti LRS, GRS és RA-GRS - is használja, a portál vagy az API-t. A zrs-t azonban nincs másként egyértelmű mert magában foglalja a fizikai adatok mozgása egyetlen tárolóblokk régión belül több stamp. Ön elsődleges kétféle – manuális másolására/áthelyezésére adatokat egy új ZRS fiókhoz meglévő fiókjából, vagy kérje meg az élő áttelepítés. Erősen ajánlott manuális áttelepítés végrehajtása, mert nem tudjuk garantálni, amikor élő áttelepítés befejeződik; nincsenek számos tényező, amely közvetlen és közvetett hatással van az áttelepítési feladat létrehozása után. 

A manuális áttelepítéshez, egy számos lehetőség közül választhat:
- Használja a meglévő eszközt használunk erre például AzCopy, a tárolási SDK, megbízható harmadik fél eszközeit, stb.
- Ha ismeri a Hadoop vagy HDInsight, hozzácsatolhat a mindkét forrás és cél (ZRS) a fürthöz fiókot, és nagymértékben parallelize az másolási folyamat segítségével ból a DistCp hasonlót
- A tároló SDK egy változat kihasználva saját tooling összeállítása

Amint azt korábban említettük, erősen ajánlott lépjen az áttelepítési útvonal, mert ez további rugalmasságot biztosít, mint az élő áttelepítés. Is szerepelnek, ha az áttelepítés történik teljes ellenőrzést.

Ha azonban manuális áttelepítést néhány alkalmazás leállást eredményez, és nem tudja felvegye, amely a végén, majd nyújtunk egy élő áttelepítés beállítása. Az élő áttelepítés egy helyben történő áttelepítés, amely lehetővé teszi, hogy továbbra is a meglévő tárfiókot használni, amíg az áttelepítés forrás és cél tárolási bélyegzők között. Az áttelepítés során továbbra is fog a tartósság és SLA-elérhetőséget azonos szinten mint általában.

Az élő áttelepítés kapható bizonyos korlátozások azonban. Ezek listája látható.

- Azonnal fog célja a működés közbeni áttelepítés kérést, de nem tudjuk garantálni lejártának az áttelepítés ténylegesen végezze el. Szüksége van az adatok úgy, hogy a zrs-t egy bizonyos ideig, majd el kell végeznie manuális áttelepítést. Általában a további adatokat kell a fiókjában, annál tovább tart, hogy az adatok áttelepítéséhez. 
- Előfordulhat, hogy csak a működés közbeni áttelepítése LRS és Georedundáns replikáció fiókból. Ha RA-GRS majd szüksége lesz egy, a következő replikációs típusú, a folytatás előtt először módosítása. A köztes lépés biztosítja, hogy a másodlagos csak olvasható végpontot, amely RA-GRS biztosít távolítja el készen állnak.
- A fióknak kell lennie a nem üres.
- Csak a helyen belüli-régió áttelepítések támogatottak. Ha az adatok áttelepítését a ZRS figyelembe eltér a forrás fiók régióban található, majd végezze el a manuális áttelepítéshez.
- Standard szintű tárolást fióktípus esetében. A prémium szintű storage-fiók nem tud áttelepíteni.

Élő áttelepítés kérelmek nyissa meg a támogatási Azure portálon keresztül. A portálról válassza ki a ZRS az átalakítani kívánt tárfiókot.
1. Kattintson a **új támogatási kérelem**
2. Az alapok ellenőrzése. Kattintson a **Tovább** gombra. 
3. Az a **probléma** területen 
    - Súlyosság szerint hagyja-van.
    - Problématípus = **adatok áttelepítése**
    - Kategória = **áttelepítés ZRS régión belül**
    - Cím = **ZRS fiók áttelepítési** (vagy valami leíró)
    - Részletek = szeretnék áttelepítésére [LRS, GRS] ZRS ___ régióban. 
4. Kattintson a **Tovább** gombra.
5. Győződjön meg arról, hogy a kapcsolattartási adatok helyesek a kapcsolattartási adatok panelen.
6. Kattintson a **nyújt**.

A támogatási szolgálatnak szerepelnek, akkor lesz. Adott személy bármely segítségnyújtás lehet szükség lesz. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>A ZRS klasszikus: Egy örökölt beállítása blokk blobokat redundancia
> [!NOTE]
> A ZRS klasszikus fiókok érvénytelenítése és 2021 március 31 szükséges áttelepítésének tervbe van véve. Microsoft elküld további részleteket a ZRS klasszikus ügyfelek elévülése előtt. Microsoft tervek szeretne biztosítani egy automatizált áttelepítési folyamat a ZRS klasszikus a zrs-t a jövőben.

>[!NOTE]
> Miután a ZRS [általánosan elérhető](#support-coverage-and-regional-availability) régióban, Ön pedig már nem fogják tudni ZRS klasszikus-fiók létrehozása a portálon ugyanabban a régióban. Azonban továbbra is létrehozhat egy Microsoft PowerShell és az Azure parancssori felület, például más módon Ez azt jelenti, hogy amíg a ZRS klasszikus elavult.

A ZRS klasszikus aszinkron módon replikálja az adatokat egy-két régiókban adatközpontok között. Lehetséges, hogy a replika nem lesz elérhető, hacsak a Microsoft nem kezdeményezi a másodlagos példány feladatátvételét. A ZRS klasszikus érhető el csak **blokkblobokat** a [általános célú 1-es verzió (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1) storage-fiókok. A klasszikus ZRS-fiókokat nem lehet átalakítani LRS- vagy GRS-fiókokká vagy fiókokból, és nem rendelkeznek metrikákkal vagy naplózási képességgel.

A ZRS klasszikus fiókokat nem lehet konvertálni, vagy a LRS, grs-re vagy RA-GRS. A ZRS klasszikus fiókok is nem támogatják az metrikákat és naplózási.

Manuális áttelepítésével ZRS fiók adatait, vagy a LRS, ZRS klasszikus, grs-re vagy RA-GRS fiókkal, használja az AzCopy, az Azure Tártallózó, az Azure PowerShell vagy az Azure parancssori felület. A saját áttelepítési megoldást az Azure Storage ügyfélkódtáraival egyikét is létrehozható.
