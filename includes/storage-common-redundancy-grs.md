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
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219851"
---
Georedundáns tárolás (GRS) célja, hogy 99,99999999999999 %-ában (16 9-es) tartós objektumok egy adott évben által az adatok replikálása egy másodlagos régióba, amely több száz mérföld a forrásadatok elsődleges. Ha a tárfiók GRS engedélyezve van, az adatok a tartós még esetén teljes regionális kimaradás vagy vészhelyzet, amelyben az elsődleges régió nem állítható helyre.

Amennyiben GRS igénybe vétele esetén, amelyek közül választhatnak, két kapcsolódó lehetősége van:

* GRS azonban, hogy adatokat érhető el lehet csak olvasható, ha a Microsoft kezdeményezi egy feladatátvétel az elsődleges kiszolgálóról a másodlagos régióba replikálja az adatokat egy másodlagos régióban, egy másik adatközpontba. 
* Írásvédett georedundáns tárolás (RA-GRS) GRS alapul. RA-GRS az adatait egy másodlagos régióban egy másik adatközpontba replikálja, és emellett lehetőséget biztosít arra, hogy olvassa el a másodlagos régióból. Az RA-GRS függetlenül attól, hogy a Microsoft kezdeményezi a másodlagos régióba feladatátvétel az elsődleges kiszolgálóról a másodlagos régióból is olvasásakor. 

GRS vagy RA-GRS engedélyezve van a storage-fiókok az összes adat először replikálódik a helyileg redundáns tárolás (LRS). Frissítés először elkötelezte magát az elsődleges helyre, és replikálja az LRS használata. A frissítés majd aszinkron módon replikálja a másodlagos régióba, GRS használatával. A másodlagos helyre írja az adatokat, amikor is replikált LRS használatával adott helyen belül. 

Az elsődleges és másodlagos régiók kezelheti a replikák külön tartalék tartományokban és frissítési tartományokban lévő tárolóskálázási egységben. A tárolási skálázási egység az egyszerű replikációs egység az adatközponton belül. LRS; által biztosított replikáció ezen a szinten További információkért lásd: [helyileg redundáns tárolás (LRS): az Azure Storage alacsony költségű adatredundancia](../articles/storage/common/storage-redundancy-lrs.md).

Tartsa szem ezeken a pontokon, ha a replikációs beállítás használata:

* Zónaredundáns tárolás (ZRS) szinkron replikációt a magas rendelkezésre állást biztosít, és lehet, hogy bizonyos helyzetekben, mint a GRS vagy RA-GRS jobb választás. A zrs-t további információkért lásd: [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Aszinkron replikációs késleltetés az adatok írása az elsődleges régióra, ha azt a rendszer a másodlagos régióba replikálja az idő foglalja magában. Regionális katasztrófa esetén módosítások, amelyek még nem lett replikálása még a másodlagos régióba lehetséges, hogy elvesznek, ha az adatok nem állíthatók vissza az elsődleges régióból.
* A grs Tárolással a replika nem érhető el olvasási vagy írási hozzáférést, kivéve, ha a Microsoft kezdeményezi egy feladatátvételt a másodlagos régióba. A feladatátvétel esetén sem fog rendelkezik olvasási, és befejeződött a feladatátvétel után az adatok írási hozzáféréssel. További információkért tekintse meg [vészhelyreállítási útmutató](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Ha az alkalmazásnak kell olvasni a másodlagos régióba, engedélyezze az RA-GRS.