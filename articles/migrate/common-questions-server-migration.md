---
title: Azure Migrate kiszolgáló áttelepítésével kapcsolatos gyakori kérdések
description: Válaszok a gépek Azure Migrate kiszolgáló áttelepítésével való áttelepítésével kapcsolatos gyakori kérdésekre
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425833"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate kiszolgáló áttelepítése: gyakori kérdések

Ez a cikk a Azure Migrate: Server áttelepítési eszközzel kapcsolatos gyakori kérdésekre ad választ. Ha további kérdései vannak a cikk elolvasása után, tekintse át a következő cikkeket:

- [Általános kérdések](resources-faq.md) a Azure Migrateról.
- Az Azure Migrate berendezéssel kapcsolatos [kérdések](common-questions-appliance.md) .
- A felderítéssel, az értékeléssel és a függőségi vizualizációval kapcsolatos [kérdések](common-questions-discovery-assessment.md) .
- Tegye fel kérdéseit a [Azure Migrate fórumon](https://aka.ms/AzureMigrateForum).


## <a name="how-does-agentless-vmware-replication-work"></a>Hogyan működik az ügynök nélküli VMware-replikáció?

A VMware-hez készült ügynök nélküli replikációs módszer VMware-pillanatképeket és VMware changed Block Tracking (CBT) módszert használ.

1. A replikálás megkezdése után a kezdeti replikálási ciklus ütemezve lesz. A kezdeti ciklusban a virtuális gép pillanatképe készül. Ez a pillanatkép a virtuális gépek VMDK (lemezek) replikálására szolgál. 
2. A kezdeti replikálási ciklus befejeződése után a különbözeti replikálási ciklusok ütemezése rendszeres időközönként történik.
    - A különbözeti replikáció során pillanatkép készül, és az előző replikálási ciklus óta módosult adatblokkok replikálódnak.
    - A VMware CBT a legutóbbi ciklus óta megváltoztatott blokkok meghatározására szolgál.
    - Az időszakos replikálási ciklusok gyakoriságát a Azure Migrate automatikusan kezeli, attól függően, hogy hány másik virtuális gép/lemez párhuzamosan replikálódik ugyanabból az adattárból. Az ideális körülmények között a replikáció minden virtuális gép esetében óránként egy ciklusba konvergál.

A Migrálás során a rendszer egy igény szerinti replikációs ciklust ütemez a gép számára, hogy rögzítse a fennmaradó adatmennyiséget. Dönthet úgy, hogy az áttelepítés során leállítja a gépet, így biztosítva a nulla adatvesztést és az alkalmazások konzisztenciáját.

## <a name="why-isnt-resynchronization-exposed"></a>Miért nem teszi elérhetővé az újraszinkronizálást?

Az ügynök nélküli áttelepítés során az összes különbözeti ciklusban az aktuális pillanatkép és a korábban készített pillanatkép közötti különbség van megírva. Mivel mindig a pillanatképek közötti különbségről van szó, a. Annak érdekében, hogy ha egy adott szektor a pillanatképek között N-szor van írva, csak az utolsó írási időt kell átvinni, mert csak a legutóbbi szinkronizálás érdekli. Ez eltér az ügynök-alapú replikációtól, ahol nyomon követheti és alkalmazhatja az összes írást. Ez azt jelenti, hogy minden különbözeti ciklus Újraszinkronizálás. Ezért nincs elérhető újraszinkronizálási beállítás. Ha a lemezek soha nem szinkronizálhatók egy hiba miatt, azt a következő ciklusban rögzíti a rendszer. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>Hogyan befolyásolja a forgalmi arány az ügynök nélküli replikációt?

Mivel az ügynök nélküli replikáció eldobása megtörténik, az adatváltozási minta nagyobb jelentőséggel bír, mint a forgalom aránya. Ha egy fájl újra és újra íródik, a ráta nem befolyásolja a nagy hatást. A következő ciklusban azonban egy minta, amelyben minden más ágazat írásos, magas adatváltozást okoz. Mivel minimálisra csökkentjük az átvitt adatok mennyiségét, a következő ciklus ütemezése előtt a lehető legnagyobb mértékben engedélyezzük az adatok bedobását.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Milyen gyakran ütemezik a replikálási ciklust?

A következő replikálási ciklust ütemezett képlet: (előző ciklus ideje/2) vagy 1 óra, amelyik a magasabb.

Ha például egy virtuális gép négy órát vesz igénybe egy különbözeti ciklus esetében, a következő ciklus két órán belül ütemezve van, és nem a következő órában. Ez a kezdeti replikáció után azonnal eltér, amikor az első különbözeti ciklust azonnal ütemezi.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>Hogyan befolyásolja az ügynök nélküli replikáció a VMware-kiszolgálókat?

VCenter Server/ESXi-gazdagépek teljesítményére gyakorolt hatás. Mivel az ügynök nélküli replikáció pillanatképeket használ, a IOPs használja a tárterületen, és bizonyos IOPS-sávszélességre van szükség. Nem ajánlott ügynök nélküli replikálást használni, ha a környezetében korlátozások vannak a tároló-vagy IOPs.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Az UEFI virtuális gépek ügynök nélkül áttelepíthetők az Azure Gen 2-be?

Nem. A Azure Site Recovery használatával telepítse át ezeket a virtuális gépeket a 2. generációs Azure-beli virtuális gépekre. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Rögzíthetek Azure Availability Zones a virtuális gépeket a Migrálás során?

Nem, Azure Availability Zones nem támogatottak.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Milyen átviteli protokollt használ a Azure Migrate a replikálás során?

Azure Migrate a hálózati blokk-eszköz (NBD) protokollt használja SSL-titkosítással.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Milyen minimális vCenter Server verzió szükséges az áttelepítéshez?

Legalább vCenter Server 5,5 és VMware vSphere ESXi-gazdagép 5,5-es verziójának kell lennie.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Az ügyfelek áttelepíthetik a virtuális gépeket a nem felügyelt lemezekre?

Nem. Azure Migrate csak a felügyelt lemezekre (standard HDD, prémium SSD) való áttelepítést támogatja.

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Hány virtuális gépet replikálhat az ügynök nélküli áttelepítés használatával?

Jelenleg vCenter Server egyszerre telepítheti át a 100 virtuális gépeket. Migrálás 10 virtuális gép kötegei között.
 



