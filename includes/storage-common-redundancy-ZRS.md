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
ms.openlocfilehash: f6d437e4ad0e05d55c408ad8f9defe5385b52050
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
Zóna redundáns tárolás (ZRS) szinkron módon replikálja az adatokat egy régió három tároló fürtök között. Minden egyes tárolási fürt a többi fizikailag elkülönített, és a saját rendelkezésre állási zóna (AZ) találhatók. Egyes rendelkezésre állási zóna és a ZRS fürtön belül, az autonóm, külön segédprogramok és hálózati képességek.

Az adatok tárolása a ZRS fiók biztosítja, hogy fog férnek hozzá és kezeli az adatokat, abban az esetben, ha a zóna nem érhető el. A ZRS kiváló teljesítményt és rendkívül kis késést biztosít.

További információ a rendelkezésre állási zónák: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="zrs-for-high-availability"></a>A ZRS a magas rendelkezésre állás 

A ZRS az erős konzisztencia, erős tartósság és magas rendelkezésre állású még akkor is, ha igénylő forgatókönyvek esetén fontolja meg a nem tervezett kimaradás vagy természeti katasztrófa képezi le egy adott adatközpont nem érhető el. ZRS nyújt a tárolási objektum legalább 99.9999999999 % tartósságot (12 9 meg) egy adott évben.

A ZRS jelenleg támogatott szabványos, általános célú v2 (GPv2) a fióktípusoknak a használata. A ZRS blokkblobokat, lapblobokat nem szabad, fájlok, táblát és üzenetsort érhető el. 

Általában a ZRS érhető el a következő régióban:

- USA keleti régiója 2
- USA középső régiója
- Észak-Európa
- Nyugat-Európa
- Közép-Franciaország
- Délkelet-Ázsia

A Microsoft továbbra is más Azure-régiók a ZRS engedélyezése.

### <a name="what-happens-when-a-zone-becomes-unavailable"></a>Mi történik, ha a zóna nem érhető el?

Az adatok továbbra is ellenáll, ha a zóna nem érhető el lesz. A Microsoft azt javasolja, hogy továbbra is kövesse az eljárások az átmeneti hiba kezelésre, például az exponenciális vissza off újrapróbálkozási házirendek megvalósítása. Ha a zóna nem érhető el, Azure vállalja hálózati frissítéseket, például DNS repointing. Ezek a frissítések hatással lehet az alkalmazás előtt végzi az adatok eléréséhez.

A ZRS nem előfordulhat, hogy védeni az adatokat egy regionális katasztrófa, ha több zóna véglegesen érintett ellen. Ehelyett a zrs-t kínál a rugalmassági historikus elérhetetlensége esetén az adatok. A regionális katasztrófák elleni védelem, a Microsoft javasolja használatával [georedundáns tárolás (GRS): az Azure Storage kereszt-területi replikációs](../articles/storage/common/storage-redundancy-grs.md).

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>A ZRS klasszikus: Egy örökölt beállítása blokk blobokat redundancia
> [!NOTE]
> A ZRS klasszikus fiókok érvénytelenítése és 2021 március 31 szükséges áttelepítésének tervbe van véve. Microsoft elküld további részleteket a ZRS klasszikus ügyfelek elévülése előtt. Microsoft tervek szeretne biztosítani egy automatizált áttelepítési folyamat a ZRS klasszikus a zrs-t a jövőben.

A ZRS klasszikus áll rendelkezésre, csak a blokkblobokhoz az általános célú V1 (GPv1) storage-fiókok. A ZRS klasszikus aszinkron módon replikálja az adatokat egy-két régiókban adatközpontok között. Lehetséges, hogy a replika nem lesz elérhető, hacsak a Microsoft nem kezdeményezi a másodlagos példány feladatátvételét. A klasszikus ZRS-fiókokat nem lehet átalakítani LRS- vagy GRS-fiókokká vagy fiókokból, és nem rendelkeznek metrikákkal vagy naplózási képességgel.

A ZRS klasszikus fiókokat nem lehet konvertálni, vagy a LRS, grs-re vagy RA-GRS. A ZRS klasszikus fiókok is nem támogatják az metrikákat és naplózási.

A ZRS régióban általánosan elérhetővé válik, többé nem tudnak a ZRS klasszikus-fiók létrehozása a portál az adott régióban, de létrehozhat egy más eszközökkel, például a PowerShell parancssori felület és így tovább.

Manuális áttelepítésével ZRS fiók adatait, vagy a LRS, ZRS klasszikus, grs-re vagy RA-GRS fiókkal, használja az AzCopy, az Azure Tártallózó, az Azure PowerShell vagy az Azure parancssori felület. A saját áttelepítési megoldást az Azure Storage ügyfélkódtáraival egyikét is létrehozható.