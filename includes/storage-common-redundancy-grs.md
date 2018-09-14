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
ms.openlocfilehash: 922f4d5dd8c71bc9365523b1d539d1b0754fff15
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580939"
---
Georedundáns tárolás (GRS) célja, hogy 99,99999999999999 %-ában (16 9-es) tartós objektumok egy adott évben által az adatok replikálása egy másodlagos régióba, amely több száz mérföld a forrásadatok elsődleges. Ha a tárfiók GRS engedélyezve van, az adatok a tartós még egy teljes regionális kimaradás vagy az elsődleges régió nem helyreállítható vészhelyzet esetén.

Amennyiben GRS igénybe vétele esetén, amelyek közül választhatnak, két kapcsolódó lehetősége van:

* GRS azonban, hogy adatokat érhető el lehet csak olvasható, ha a Microsoft kezdeményezi egy feladatátvétel az elsődleges kiszolgálóról a másodlagos régióba replikálja az adatokat egy másodlagos régióban, egy másik adatközpontba. 
* Írásvédett georedundáns tárolás (RA-GRS) GRS alapul. RA-GRS az adatait egy másodlagos régióban egy másik adatközpontba replikálja, és emellett lehetőséget biztosít arra, hogy olvassa el a másodlagos régióból. Az RA-GRS áttekintheti, függetlenül attól, hogy a Microsoft feladatátvétel az elsődleges kiszolgálóról a másodlagos kezdeményezi a másodlagos helyről. 

GRS vagy RA-GRS engedélyezve van a storage-fiókok az összes adat először replikálódik a helyileg redundáns tárolás (LRS). Frissítés először elkötelezte magát az elsődleges helyre, és replikálja az LRS használata. A frissítés majd aszinkron módon replikálja a másodlagos régióba, GRS használatával. A másodlagos helyre írja az adatokat, amikor is replikált LRS használatával adott helyen belül. 

Az elsődleges és másodlagos régiók kezelheti a replikák külön tartalék tartományokban és frissítési tartományokban lévő tárolóskálázási egységben. A tárolási skálázási egység az egyszerű replikációs egység az adatközponton belül. LRS; által biztosított replikáció ezen a szinten További információkért lásd: [helyileg redundáns tárolás (LRS): az Azure Storage alacsony költségű adatredundancia](../articles/storage/common/storage-redundancy-lrs.md).

Tartsa szem ezeken a pontokon, ha a replikációs beállítás használata:

* Zónaredundáns tárolás (ZRS) szinkron replikációt a magas rendelkezésre állást biztosít, és lehet, hogy bizonyos helyzetekben, mint a GRS vagy RA-GRS jobb választás. A zrs-t további információkért lásd: [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Mivel az aszinkron replikáció magában foglalja a késést, regionális katasztrófa, lehetséges, hogy módosítások, amelyek még nem replikálódott a másodlagos régióba elvesznek, ha az adatok nem állíthatók vissza az elsődleges régióból.
* A grs Tárolással a replika nem áll rendelkezésre, ha a Microsoft a másodlagos régióba történő feladatátvételt kezdeményez. Ha a Microsoft kezdeményezzen feladatátvételt a másodlagos régióba, akkor a read, és befejeződött a feladatátvétel után az adatok írási hozzáféréssel. További információkért tekintse meg [vészhelyreállítási útmutató](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Ha az alkalmazásnak kell olvasni a másodlagos régióba, engedélyezze az RA-GRS.