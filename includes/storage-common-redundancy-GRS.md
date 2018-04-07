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
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
Georedundáns tárolás (GRS) szolgál a legalább 99.99999999999999 % (16 9 tartozó) tartóssági objektumok egy adott évben által az adatok replikálása egy másodlagos régióban, amelyek több száz miles elhagyja az elsődleges régióban van. Ha a tárfiók Georedundáns engedélyezve van, az adatok akkor tartós teljes regionális kimaradás vagy az elsődleges régióban nincs helyreállítható katasztrófa esetén is.

Ha úgy dönt, a Georedundáns, két lehetősége kapcsolódó választhat:

* Georedundáns replikálja az adatokat egy másodlagos régióban, egy másik adatközpont azonban, hogy a csak olvasható, ha Microsoft indít el egy feladatátvételt az elsődleges másodlagos régióba érhetők el adatok. 
* Írásvédett georedundáns tárolás (RA-GRS) Georedundáns alapul. RA-GRS replikálja az adatokat egy másik adatközpont egy másodlagos régióban, és emellett lehetőséget biztosít a beállítást, a másodlagos régióba olvasni. Az RA-GRS áttekintheti a másodlagos, függetlenül attól, hogy a Microsoft indít el egy feladatátvételt a másodlagos az elsődleges adatbázisból. 

A grs-re vagy RA-GRS engedélyezve van a tárfiókon minden adat először replikálódik a helyileg redundáns tárolás (LRS). Egy frissítés először véglegesítése az elsődleges helyre, és replikálja az LRS használatával. A frissítés ezután a rendszer aszinkron módon a másodlagos régióba Georedundáns. A másodlagos hely adatot ír, amikor is replikált belül erre a helyre LRS használatával. 

Az elsődleges és másodlagos régióban külön tartalék tartományok közötti replikák kezelése, és frissítse a tárolási méretezési egység tartományban. A tárolási méretezési egység az egyszerű replikációs egység az adatközponton belül. Ezen a szinten replikációs biztosítja LRS; További információkért lásd: [helyileg redundáns tárolás (LRS): az Azure Storage alacsony költségű adatredundanciát](../articles/storage/common/storage-redundancy-lrs.md).

Ezek a pontok tartsa szem előtt, ha a replikációs beállítás használata:

* Zónaredundáns tárolás (ZRS) szinkron replikáció magas rendelkezésre állást biztosít, és lehet, hogy az egyes forgatókönyvek jobb megoldás, mint a grs-re vagy RA-GRS. A ZRS további információkért lásd: [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Aszinkron replikációs késés jár, mivel regionális katasztrófa esetén is lehetséges, hogy még nem replikálódott a másodlagos régióba módosítások elvesznek az adatokat nem lehet helyreállítani az elsődleges régióban.
* A GRS a replika csak akkor érhető Microsoft kezdeményezi a másodlagos régióba. Ha a Microsoft kezdeményezzen feladatátvételt a másodlagos régióban, akkor lesz olvasási és írási engedéllyel a feladatátvételt követően befejeződött. További információkért lásd: [vész-helyreállítási útmutatást](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Ha az alkalmazásnak a másodlagos régióba olvasni, engedélyezze az RA-GRS.

## <a name="read-access-geo-redundant-storage"></a>Írásvédett georedundáns tárolás

Írásvédett georedundáns tárolás (RA-GRS) a lehető legnagyobbra növeli a tárfiók rendelkezésre állását. RA-GRS az adatokat a másodlagos helyen mellett georeplikáció két régióban is olvasási hozzáférést biztosít.

Csak olvasási hozzáféréssel a másodlagos régióba adatai engedélyezésekor az adatok érhető el a másodlagos végponti, valamint a tárfiók elsődleges végpont. A másodlagos végponti az elsődleges végpont hasonló, de a utótag `–secondary` fióknevet. Például, ha az elsődleges végpont a Blob szolgáltatás `myaccount.blob.core.windows.net`, akkor a másodlagos végponti `myaccount-secondary.blob.core.windows.net`. A tárfiók hozzáférési kulcsainak megegyeznek az elsődleges és másodlagos végpontok.

RA-GRS használatakor szem előtt tartani szempontokat:

* Az alkalmazás melyik végponthoz az RA-GRS használatakor dolgozik kezelésére van.
* Mivel aszinkron replikációs késés, a módosítások, amelyek még nem replikálódott a másodlagos régióba elveszhetnek, ha adatokat nem lehet helyreállítani az elsődleges régióban, például regionális katasztrófa esetén.
* Ellenőrizheti a legutóbbi szinkronizálásának időpontja a tárfiók. Utolsó szinkronizálás ideje GMT dátum/idő értéket. Minden elsődleges írás előtt a legutóbbi szinkronizálás ideje sikeresen elkészült a másodlagos helyre, ami azt jelenti, hogy elérhetők a másodlagos helyről olvasható. Elsődleges írása után utolsó szinkronizálásának időpontja is, vagy nem érhető el az olvasási műveletek még. Az érték használatával kérdezheti a [Azure-portálon](https://portal.azure.com/), [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), vagy az Azure Storage ügyfélkódtáraival egyikéből.
* Ha a Microsoft a másodlagos régióba történő feladatátvételt kezdeményez, hogy fog rendelkezik olvasási és írási engedéllyel a feladatátvételt követően befejeződött. További információkért lásd: [vész-helyreállítási útmutatást](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Váltson át a másodlagos régióba kapcsolatos információkért lásd: [Mi a teendő, ha egy Azure Storage esetleges leálláskor](../articles/storage/common/storage-disaster-recovery-guidance.md).
* RA-GRS magas rendelkezésre állású célokra szolgál. Méretezhetőség útmutatásért tekintse át a [teljesítmény ellenőrzőlista](../articles/storage/common/storage-performance-checklist.md).
* A magas rendelkezésre álláshoz az RA-GRS tervezéséről a javaslatok, lásd: [tervezése magas rendelkezésre álló alkalmazások RA-GRS-tárolót](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Mi az a helyreállítási Időkorlát és a GRS RTO?
**Helyreállítási időkorlát (RPO):** Georedundáns és RA-GRS, a tárolás szolgáltatás aszinkron módon földrajzi-replikálja az adatokat a másodlagos hely az elsődleges. Legyen fő regionális katasztrófahelyzet esetén az elsődleges régióban Microsoft hajtja végre a feladatátvételt a másodlagos régióba. A feladatátvétel történik, ha a legutóbbi módosítások, amelyek még nincsenek georeplikált elveszhetnek. A potenciális elveszett adatok percet nevezzük a helyreállítási Időkorlát, és a pont azt jelzi, amelyhez az adatok helyreállíthatók időben. Az Azure Storage általában kevesebb mint 15 perces az RPO, akkor bár jelenleg nincs SLA georeplikáció mennyi ideig tart.

**A helyreállítási idő célkitűzése (RTO):** a RTO azt méri, hogy mennyi ideig tart a tárfiók újra online, és végezze el a feladatátvételt. Az idő a feladatátvétel elvégzéséhez a következő műveleteket tartalmazza:

   * Az idő a Microsoft megköveteli az adatok helyreállíthatók az elsődleges helyen, vagy ha a feladatátvétel során szükséges.
   * Az idő hajtsa végre a feladatátvételt a tárfiók módosítása az elsődleges DNS-bejegyzéseket a másodlagos helyére mutasson.

   A Microsoft felelőssége súlyosan megőrzi az adatokat fogad. Ha az elsődleges régióban adatok helyreállítását minden alkalommal, Microsoft késleltetés a feladatátvételt, és összpontosítson, az adatok helyreállításáról. Egy jövőbeli verziójában a szolgáltatás lehetővé teszi a fiók szintjén feladatátvevő indul el, így felügyelheti a RTO saját maga.

## <a name="paired-regions"></a>Párhuzamos régiók 

A storage-fiók létrehozásakor ki kell választania a fiók az elsődleges régióban. A párhuzamos másodlagos régióba az elsődleges régió alapján történik, és nem módosítható. Használható az Azure-régiók naprakész információkat, lásd: [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Azure-régiókat párosítva](../articles/best-practices-availability-paired-regions.md).
