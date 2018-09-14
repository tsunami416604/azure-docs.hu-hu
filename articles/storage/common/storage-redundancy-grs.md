---
title: Georedundáns tárolás (GRS) az Azure Storage-régiók közti tartóssági |} A Microsoft Docs
description: Georedundáns tárolás (GRS), amelyek több száz mérföld távolságra két régiója között replikálja az adatokat. GRS az adatközpontban, valamint a regionális katasztrófa elleni védelmet biztosít.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: be3d0d32e60e23b2b2d7d414d2297b86dec62f1d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576833"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Georedundáns tárolás (GRS): az Azure Storage-régiók közti replikáció
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Írásvédett georedundáns tárolás
Írásvédett georedundáns tárolás (RA-GRS) maximalizálja a tárfiók rendelkezésre állását. RA-GRS az adatokat a másodlagos hely, georeplikációt két régióban mellett csak olvasási hozzáférést biztosít.

Ha engedélyezi a csak olvasási hozzáférés az adatokhoz a másodlagos régióban, az adatok érhető el a másodlagos végpontra és a tárfiók elsődleges végpontjába. A másodlagos végpontot az elsődleges végpont hasonló, de az utótag `–secondary` fióknevet. Például, ha az elsődleges végpont a Blob szolgáltatás `myaccount.blob.core.windows.net`, akkor a másodlagos végpontra `myaccount-secondary.blob.core.windows.net`. A tárfiók hozzáférési kulcsait megegyeznek az elsődleges és másodlagos végpontokhoz.

Néhány szempontot, vegye figyelembe, amikor, RA-GRS használatával:

* Az alkalmazás melyik végponthoz, tesztverzióval az RA-GRS használata kezelheti azt.
* Aszinkron replikációs késleltetés jár, mivel a módosításokat, amelyek még nem replikálódott a másodlagos régióba elveszhetnek, ha az adatok nem állíthatók vissza az elsődleges régióból, például regionális katasztrófa.
* Ellenőrizheti a tárfiók a legutóbbi szinkronizálás időpontja. Utolsó szinkronizálás időpontja egy olyan GMT dátum/idő érték. Az összes elsődleges írási művelet a legutóbbi szinkronizálás időpontja előtt sikeresen alkalmazáskonfigurációjának a másodlagos helyre, ami azt jelenti, hogy elérhetők legyenek az olvasható másodlagos helyről. Elsődleges írása után előfordulhat, hogy a legutóbbi szinkronizálás időpontja, vagy nem érhető el az olvasásokhoz még. Az érték használatával lekérdezheti a [az Azure portal](https://portal.azure.com/), [Azure PowerShell-lel](storage-powershell-guide-full.md), vagy az Azure Storage ügyfélkódtáraival közül.
* Ha a Microsoft a másodlagos régióba történő feladatátvételt kezdeményez, akkor a read, és befejeződött a feladatátvétel után az adatok írási hozzáféréssel. További információkért lásd: [vészhelyreállítási útmutató](storage-disaster-recovery-guidance.md).
* Hogyan lehet váltani a másodlagos régióba információkért lásd: [Mi a teendő az Azure Storage leállása esetén](storage-disaster-recovery-guidance.md).
* RA-GRS a magas rendelkezésre állású célokra szolgál. Skálázhatósági útmutató, tekintse át a [teljesítmény ellenőrzőlista](storage-performance-checklist.md).
* Javaslatok a magas rendelkezésre állás az RA-GRS tervezéséhez, lásd: [tervezése magas rendelkezésre álló alkalmazásokat a RA-GRS tároló](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Mi az az RPO és RTO a grs Tárolással?
**Helyreállítási időkorlát (RPO):** GRS és RA-GRS, a storage szolgáltatás aszinkron módon geo-replikálja az adatokat az elsődleges kiszolgálóról a másodlagos helyen. Esetén a regionális vészhelyzetek az elsődleges régióban a Microsoft végzi a feladatátvételt a másodlagos régióba. Ha feladatátvétel történik, a legutóbbi módosítások, amelyek még nem történt georeplikált elveszhetnek. Az esetleges adat elveszett percben nevezzük az rpo-t, és azt jelzi, a pont, amelyre az adatok helyreállíthatók időben. Az Azure Storage általában kevesebb, mint 15 perces egy Helyreállításipont-célkitűzéssel rendelkezik, noha jelenleg nem vonatkozik garantált szolgáltatási georeplikációs mennyi ideig tart.

**A helyreállítási időre vonatkozó célkitűzés (RTO):** az RTO azt méri, mennyi ideig tart, hajtsa végre a feladatátvételt, és kérje le a tárfiókhoz online állapotba. Az idő a feladatátvétel végrehajtásához az alábbi műveleteket tartalmazza:

   * Az idő a Microsoft van szükség az adatok helyreállíthatók legyenek, az elsődleges helyen, vagy ha szükség egy feladatátvevő.
   * Az az idő módosításával, hogy a másodlagos helyre mutasson az elsődleges DNS-bejegyzéseket a tárfiók a feladatátvétel végrehajtásához.

   A Microsoft felelőssége komolyan megőrzi az adatok vesz igénybe. Ha az elsődleges régióban lévő adatok helyreállítása bármilyen esélyét, a Microsoft a feladatátvétel késleltetés, és összpontosítson az adatok helyreállítása. Egy jövőbeli verziójában a szolgáltatás lehetővé teszi, hogy a fiók szintjén a feladatátvétel aktiválása, így szabályozható az RTO saját maga.

## <a name="paired-regions"></a>Párosított régiók 
Amikor létrehoz egy tárfiókot, válassza ki az elsődleges régió a fiókhoz. A párosított másodlagos régió az elsődleges régió alapján határozza meg, és nem módosítható. Az Azure által támogatott régiók naprakész kapcsolatban lásd: [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Azure párosított régiói](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Lásd még
- [Azure Storage replication (Azure Storage replikáció)](storage-redundancy.md)
- [Helyileg redundáns tárolás (LRS): az Azure Storage alacsony költségű adatredundancia](storage-redundancy-lrs.md)
- [Zónaredundáns tárolás (ZRS): az Azure Storage magas rendelkezésre állású alkalmazások](storage-redundancy-zrs.md)