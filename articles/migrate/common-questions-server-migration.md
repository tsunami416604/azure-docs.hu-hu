---
title: Azure Migrate kiszolgáló áttelepítésével kapcsolatos gyakori kérdések
description: Válaszok a Azure Migrate-kiszolgáló áttelepítésével kapcsolatos gyakori kérdésekre
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067382"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate kiszolgáló áttelepítése: gyakori kérdések

Ez a cikk a Azure Migrate: kiszolgáló áttelepítésével kapcsolatos gyakori kérdésekre ad választ. Ha további lekérdezésekre van szüksége a cikk elolvasása után, tegye fel őket a [Azure Migrate fórumba](https://aka.ms/AzureMigrateForum). Ha további kérdései vannak, tekintse át a következő cikkeket:

- [Általános kérdések](resources-faq.md) a Azure Migrateról.
- Az Azure Migrate berendezéssel kapcsolatos [kérdések](common-questions-appliance.md) .
- A felderítéssel, az értékeléssel és a függőségi vizualizációval kapcsolatos [kérdések](common-questions-discovery-assessment.md) .


## <a name="how-does-agentless-vmware-replication-work"></a>Hogyan működik az ügynök nélküli VMware-replikáció?

A VMware-hez készült ügynök nélküli replikációs módszer VMware-pillanatképeket és VMware changed Block Tracking (CBT) módszert használ. A kezdeti replikálási ciklus akkor van ütemezve, amikor a felhasználó elindítja a replikálást. A kezdeti replikálási ciklusban létrejön a virtuális gép pillanatképe, és ez a pillanatkép a virtuális gépek VMDK (lemezek) replikálására szolgál. A kezdeti replikálási ciklus befejezése után a különbözeti replikálási ciklusok ütemezése rendszeres időközönként történik. A különbözeti replikálási ciklusban pillanatkép készül, és az előző replikálási ciklus óta módosult adatblokkok replikálódnak. A VMware changed Block Tracking az utolsó ciklus óta módosult blokkok meghatározására szolgál.
Az időszakos replikálási ciklusok gyakoriságát a szolgáltatás automatikusan kezeli, attól függően, hogy hány másik virtuális gép/lemez párhuzamosan replikálódik ugyanabból az adattárolóból, és az ideális körülmények között a virtuális gép óránként 1 ciklusba kerül.

A Migrálás során egy igény szerinti replikációs ciklus ütemezve van a virtuális gép számára, hogy rögzítse a fennmaradó adatmennyiséget. Dönthet úgy, hogy leállítja a virtuális gépet az áttelepítés részeként, így biztosítva a nulla adatvesztést és az alkalmazások konzisztenciáját.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Miért nem érhető el az újraszinkronizálási lehetőség az ügynök nélküli veremben?

Az ügynök nélküli veremben az összes változási ciklusban az aktuális pillanatkép és az elvégzett korábbi pillanatkép közötti különbözetet helyezzük át. Mivel mindig diff a pillanatképek között, ez biztosítja az adatok összehajtásának előnyeit (azaz ha egy adott szektor a pillanatképek között "n" van írva, csak a legutóbbi írást kell átadnia, mert csak az utolsó szinkronizálás érdekli). Ez eltér az ügynök-alapú veremtől, amelyben nyomon követjük az összes írást, és alkalmazza őket. Ez azt jelenti, hogy minden különbözeti ciklus Újraszinkronizálás. Ezért nincs elérhető újraszinkronizálási beállítás. 

Ha a lemezek meghibásodása miatt még nincsenek szinkronban, a következő ciklusban rögzíti a rendszer. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Milyen hatással van a forgalom arányára, ha ügynök nélküli replikációt használok?

Mivel a verem a hajtogatott adatforgalomtól függ, az adatvesztési aránynál nagyobb a változási minta az ebben a veremben. Az a minta, amelyben egy fájl írása folyamatban van, és ismét nem lesz nagy hatással. A következő ciklusban azonban a minta, amelyben minden más szektor íródott, magas adatváltozást okoz. Mivel minimálisra csökkentjük az átvitt adatok mennyiségét, a következő ciklus ütemezése előtt a lehető legnagyobb mértékben engedélyezzük az adatok bedobását.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Milyen gyakran ütemezik a replikálási ciklust?

A következő replikálási ciklust tartalmazó képlet: (előző ciklus ideje/2) vagy 1 óra, amelyik a magasabb. Ha például egy virtuális gép négy órát vett igénybe egy különbözeti ciklus esetében, akkor 2 órán belül beütemezhetjük a következő ciklust, és nem a következő órában. Ez akkor különbözik, ha a ciklus közvetlenül az IR után van, ahol azonnal beütemezhetjük az első különbözeti ciklust.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Milyen hatással van vCenter Server vagy ESXi-gazdagép teljesítményére az ügynök nélküli replikáció használata során?

Mivel az ügynök nélküli replikáció pillanatképeket használ, a tárolás során IOPs kell lennie, és az ügyfeleknek szükségük lesz némi IOPs-belmagasságra a tárterületen. Ezt a veremet nem ajánlott a Storage/IOPs korlátozott környezetben használni.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>Támogatja az ügynök nélküli áttelepítési verem az UEFI virtuális gépek Azure Gen 2 virtuális gépekre való áttelepítését?

Nem, a virtuális gépeknek a 2. generációs Azure-beli virtuális gépekre való áttelepítéséhez Azure Site Recoveryt kell használnia. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Tudom rögzíteni a virtuális gépeket, hogy Azure Availability Zones az áttelepítés során?

Nem, a Azure Availability Zones támogatása nem létezik.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Milyen átviteli protokollt használ a Azure Migrate a replikálás során?

Azure Migrate hálózati blokk-eszköz (NBD) protokollt használ SSL-titkosítással.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Milyen minimális vCenter Server verzió szükséges az áttelepítéshez?

Legalább vCenter Server 5,5 és VMware vSphere ESXi-gazdagép 5,5-es verziójának kell lennie.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Az ügyfelek áttelepíthetik a virtuális gépeket a nem felügyelt lemezekre?

Nem. Azure Migrate csak a felügyelt lemezekre (standard HDD, prémium SSD) való áttelepítést támogatja.

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Hány virtuális gépet tud párhuzamosan replikálni az ügynök nélküli VMware stack használatával?

Az ügyfelek jelenleg vCenter Server egyidejűleg telepíthetnek 100 virtuális gépeket. Ezt 10 virtuális gépet tartalmazó kötegekben lehet elvégezni.




