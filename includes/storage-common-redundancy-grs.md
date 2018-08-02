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
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399957"
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

## <a name="read-access-geo-redundant-storage"></a>Írásvédett georedundáns tárolás

Írásvédett georedundáns tárolás (RA-GRS) maximalizálja a tárfiók rendelkezésre állását. RA-GRS az adatokat a másodlagos hely, georeplikációt két régióban mellett csak olvasási hozzáférést biztosít.

Ha engedélyezi a csak olvasási hozzáférés az adatokhoz a másodlagos régióban, az adatok érhető el a másodlagos végpontra és a tárfiók elsődleges végpontjába. A másodlagos végpontot az elsődleges végpont hasonló, de az utótag `–secondary` fióknevet. Például, ha az elsődleges végpont a Blob szolgáltatás `myaccount.blob.core.windows.net`, akkor a másodlagos végpontra `myaccount-secondary.blob.core.windows.net`. A tárfiók hozzáférési kulcsait megegyeznek az elsődleges és másodlagos végpontokhoz.

Néhány szempontot, vegye figyelembe, amikor, RA-GRS használatával:

* Az alkalmazás melyik végponthoz, tesztverzióval az RA-GRS használata kezelheti azt.
* Aszinkron replikációs késleltetés jár, mivel a módosításokat, amelyek még nem replikálódott a másodlagos régióba elveszhetnek, ha az adatok nem állíthatók vissza az elsődleges régióból, például regionális katasztrófa.
* Ellenőrizheti a tárfiók a legutóbbi szinkronizálás időpontja. Utolsó szinkronizálás időpontja egy olyan GMT dátum/idő érték. Az összes elsődleges írási művelet a legutóbbi szinkronizálás időpontja előtt sikeresen alkalmazáskonfigurációjának a másodlagos helyre, ami azt jelenti, hogy elérhetők legyenek az olvasható másodlagos helyről. Elsődleges írása után előfordulhat, hogy a legutóbbi szinkronizálás időpontja, vagy nem érhető el az olvasásokhoz még. Az érték használatával lekérdezheti a [az Azure portal](https://portal.azure.com/), [Azure PowerShell-lel](../articles/storage/common/storage-powershell-guide-full.md), vagy az Azure Storage ügyfélkódtáraival közül.
* Ha a Microsoft a másodlagos régióba történő feladatátvételt kezdeményez, akkor a read, és befejeződött a feladatátvétel után az adatok írási hozzáféréssel. További információkért lásd: [vészhelyreállítási útmutató](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Hogyan lehet váltani a másodlagos régióba információkért lásd: [Mi a teendő az Azure Storage leállása esetén](../articles/storage/common/storage-disaster-recovery-guidance.md).
* RA-GRS a magas rendelkezésre állású célokra szolgál. Skálázhatósági útmutató, tekintse át a [teljesítmény ellenőrzőlista](../articles/storage/common/storage-performance-checklist.md).
* Javaslatok a magas rendelkezésre állás az RA-GRS tervezéséhez, lásd: [tervezése magas rendelkezésre álló alkalmazásokat a RA-GRS tároló](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Mi az az RPO és RTO a grs Tárolással?
**Helyreállítási időkorlát (RPO):** GRS és RA-GRS, a storage szolgáltatás aszinkron módon geo-replikálja az adatokat az elsődleges kiszolgálóról a másodlagos helyen. Esetén a regionális vészhelyzetek az elsődleges régióban a Microsoft végzi a feladatátvételt a másodlagos régióba. Ha feladatátvétel történik, a legutóbbi módosítások, amelyek még nem történt georeplikált elveszhetnek. Az esetleges adat elveszett percben nevezzük az rpo-t, és azt jelzi, a pont, amelyre az adatok helyreállíthatók időben. Az Azure Storage általában kevesebb, mint 15 perces egy Helyreállításipont-célkitűzéssel rendelkezik, noha jelenleg nem vonatkozik garantált szolgáltatási georeplikációs mennyi ideig tart.

**A helyreállítási időre vonatkozó célkitűzés (RTO):** az RTO azt méri, mennyi ideig tart, hajtsa végre a feladatátvételt, és kérje le a tárfiókhoz online állapotba. Az idő a feladatátvétel végrehajtásához az alábbi műveleteket tartalmazza:

   * Az idő a Microsoft van szükség az adatok helyreállíthatók legyenek, az elsődleges helyen, vagy ha szükség egy feladatátvevő.
   * Az az idő módosításával, hogy a másodlagos helyre mutasson az elsődleges DNS-bejegyzéseket a tárfiók a feladatátvétel végrehajtásához.

   A Microsoft felelőssége komolyan megőrzi az adatok vesz igénybe. Ha az elsődleges régióban lévő adatok helyreállítása bármilyen esélyét, a Microsoft a feladatátvétel késleltetés, és összpontosítson az adatok helyreállítása. Egy jövőbeli verziójában a szolgáltatás lehetővé teszi, hogy a fiók szintjén a feladatátvétel aktiválása, így szabályozható az RTO saját maga.

## <a name="paired-regions"></a>Párosított régiók 

Amikor létrehoz egy tárfiókot, válassza ki az elsődleges régió a fiókhoz. A párosított másodlagos régió az elsődleges régió alapján határozza meg, és nem módosítható. Az Azure által támogatott régiók naprakész kapcsolatban lásd: [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Azure párosított régiói](../articles/best-practices-availability-paired-regions.md).
