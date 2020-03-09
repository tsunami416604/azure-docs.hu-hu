---
title: Azure Migrate Server Migration – gyakori kérdések
description: Választ kaphat a Azure Migrate kiszolgáló áttelepítésének a gépek áttelepítésére való használatával kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 273ef746e685afcf9f3654963dd9c6bd5b855b24
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927520"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate kiszolgáló áttelepítése: gyakori kérdések

Ez a cikk a Azure Migrate: Server áttelepítési eszközzel kapcsolatos gyakori kérdésekre ad választ. Ha további kérdései vannak, tekintse meg a következő erőforrásokat:

- [Általános kérdések](resources-faq.md) a Azure Migrate
- Az [Azure Migrate berendezéssel](common-questions-appliance.md) kapcsolatos kérdések
- A [felderítéssel, az értékeléssel és a függőségi vizualizációval](common-questions-discovery-assessment.md) kapcsolatos kérdések
- A [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum) választ kaphat kérdéseire

## <a name="how-does-agentless-vmware-replication-work"></a>Hogyan működik az ügynök nélküli VMware-replikáció?

A VMware ügynök nélküli replikációs módszere a VMware-Pillanatképek és a VMware changed Block Tracking (CBT) használatával működik.

A folyamat a következő:

1. A replikálás megkezdése után a kezdeti replikálási ciklus ütemezve lesz. A kezdeti ciklusban a virtuális gép pillanatképe készül. A pillanatkép a virtuális gépek VMDK (lemezek) replikálására szolgál. 
2. A kezdeti replikálási ciklus befejeződése után a különbözeti replikálási ciklusok ütemezése rendszeres időközönként történik.
    - A különbözeti replikáció során pillanatkép készül, és az előző replikálási ciklus óta módosult adatblokkok replikálódnak.
    - A VMware CBT a legutóbbi ciklus óta megváltoztatott blokkok meghatározására szolgál.
    - Az időszakos replikálási ciklusok gyakoriságát a Azure Migrate automatikusan kezeli, és attól függ, hogy hány másik virtuális gép és lemez párhuzamosan replikálódik ugyanabból az adattárból. Az ideális körülmények között a replikáció minden virtuális gép esetében óránként egy ciklusba konvergál.

A Migrálás során egy igény szerinti replikációs ciklus ütemezve van a gép számára, hogy rögzítse a fennmaradó adatmennyiséget. A nulla adatvesztés és az alkalmazások konzisztenciájának biztosítása érdekében dönthet úgy, hogy leállítja a gépet az áttelepítés során.

## <a name="why-isnt-resynchronization-exposed"></a>Miért nem teszi elérhetővé az újraszinkronizálást?

Az ügynök nélküli áttelepítés során a rendszer minden változási ciklusban megírja az aktuális pillanatkép és a korábban végrehajtott pillanatkép közötti különbséget. Mindig a pillanatképek és a behajtott adategységek közötti különbség. Ha egy adott szektor a pillanatképek között *N* alkalommal íródott, csak az utolsó írási időt kell átadni, mert csak az utolsó szinkronizáláskor érdeklik. A folyamat különbözik az ügynök-alapú replikációtól, amely alatt nyomon követjük és alkalmazzuk az összes írást. Ebben a folyamatban minden különbözeti ciklus újraszinkronizálást eredményez. Tehát nincs elérhető újraszinkronizálási beállítás. Ha a lemezek egy hiba miatt nem szinkronizálhatók, azt a következő ciklusban rögzíti a rendszer. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Hogyan befolyásolja az adatforgalom aránya az ügynök nélküli replikációt?

Mivel az ügynök nélküli replikáció az adatforgalomban található, az *adatváltozási minta* fontosabb, mint a változási *arány*. Ha egy fájl újra és újra íródik, a ráta nem befolyásolja a nagy hatást. A következő ciklusban azonban egy minta, amelyben minden más ágazat írásos, magas adatváltozást okoz. Mivel minimálisra csökkentjük az általunk átvitt adatok mennyiségét, a lehető legnagyobb mértékben engedélyezzük, hogy az adatok minél hamarabb bekerüljön a következő ciklusba.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Milyen gyakran ütemezik a replikálási ciklust?

A következő replikálási ciklust ütemezni kívánt képlet (a korábbi ciklus ideje/2) vagy egy óra, attól függően, hogy melyik a magasabb.

Ha például egy virtuális gép négy órát vesz igénybe egy különbözeti ciklus esetében, a következő ciklus két órán belül ütemezve van, és nem a következő órában. A folyamat azonnal eltér a kezdeti replikáció után, amikor az első különbözeti ciklust azonnal ütemezi.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Hogyan befolyásolja az ügynök nélküli replikáció a VMware-kiszolgálókat?

Az ügynök nélküli replikáció a VMware vCenter Server és VMware ESXi gazdagépek teljesítményére gyakorolt hatásukat eredményez. Mivel az ügynök nélküli replikáció pillanatképeket használ, a IOPS-t használja a tárterületen, ezért bizonyos IOPS-sávszélességre van szükség. Nem ajánlott ügynök nélküli replikálást használni, ha a környezetében korlátozásokkal rendelkezik a tároló-vagy IOPs.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Az UEFI virtuális gépek ügynök nélkül áttelepíthetők az Azure Gen 2-be?

Nem. A Azure Site Recovery használatával telepítse át ezeket a virtuális gépeket a 2. generációs Azure-beli virtuális gépekre. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Rögzíthetek Azure Availability Zones a virtuális gépeket a Migrálás során?

Nem. A Azure Availability Zones Azure Migrate Migrálás nem támogatott.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Milyen átviteli protokollt Azure Migrate használni a replikáció során?

Azure Migrate a hálózati blokk-eszköz (NBD) protokollt használja SSL-titkosítással.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Milyen minimális vCenter Server verzió szükséges az áttelepítéshez?

Legalább vCenter Server 5,5 és vSphere ESXi-gazdagép 5,5-es verziójának kell lennie.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Az ügyfelek áttelepíthetik a virtuális gépeket a nem felügyelt lemezekre?

Nem. A Azure Migrate csak felügyelt lemezekre (standard HDD, prémium SSD) támogatja az áttelepítést.

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Hány virtuális gépet lehet egyszerre replikálni az ügynök nélküli áttelepítés használatával?

Jelenleg a 100-es virtuális gépeket a vCenter Server egy példányán egyszerre telepítheti át. Migrálás 10 virtuális gép kötegei között.
 
## <a name="next-steps"></a>Következő lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).
