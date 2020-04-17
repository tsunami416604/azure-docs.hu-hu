---
title: – gyakori kérdések az Azure Áttelepítési kiszolgáló áttelepítésével kapcsolatos gyakori kérdések
description: Válaszok az Azure Migrate Server Migration használatával a gépek áttelepítésével kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: bf33c276c721f3be16d75b964c2b223e90c2a560
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529798"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Áttelepítési kiszolgáló: Gyakori kérdések

Ez a cikk az Azure Áttelepítés: Kiszolgálóáttelepítés imátló eszközzel kapcsolatos gyakori kérdésekre ad választ. Ha egyéb kérdése van, ellenőrizze az alábbi forrásokat:

- [Általános kérdések az](resources-faq.md) Azure Áttelepítésével kapcsolatban
- Kérdések az [Azure Migrate készülékkel kapcsolatban](common-questions-appliance.md)
- Kérdések [a felderítéssel, értékeléssel és függőségi megjelenítéssel kapcsolatban](common-questions-discovery-assessment.md)
- Kérdések megválaszolása az [Azure Áttelepítésfórumában](https://aka.ms/AzureMigrateForum)

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Milyen földrajzi területek támogatottak az Azure Áttelepítés?

Tekintse át a támogatott földrajzi köz- [és](migrate-support-matrix.md#supported-geographies-public-cloud) [kormányzati felhőket.](migrate-support-matrix.md#supported-geographies-azure-government)

## <a name="how-does-agentless-vmware-replication-work"></a>Hogyan működik az ügynök nélküli VMware replikáció?

A VMware ügynök nélküli replikációs módszere VMware pillanatképeket és VMware módosított blokkkövetést (CBT) használ.

Ennek folyamata a következő:

1. A replikáció indításakor egy kezdeti replikációs ciklus lesz ütemezve. A kezdeti ciklusban a virtuális gép pillanatképe készül. A pillanatkép a virtuális gépek VMDK-k (lemezek) replikálására szolgál. 
2. A kezdeti replikációs ciklus befejezése után a rendszer rendszeres időközönként ütemezi a különbözeti replikációs ciklusokat.
    - A különbözeti replikáció során pillanatkép készül, és az előző replikációs ciklus óta megváltozott adatblokkok replikálódnak.
    - A VMware CBT az utolsó ciklus óta megváltozott blokkok meghatározására szolgál.
    - Az időszakos replikációs ciklusok gyakoriságát automatikusan kezeli az Azure Migrate, és attól függ, hogy hány más virtuális gépek és lemezek egyidejűleg replikálja az azonos adattárból. Ideális körülmények között a replikáció végül minden virtuális gép óránként egy ciklushoz közelít.

Az áttelepítéskor igény szerinti replikációs ciklus van ütemezve a számítógép számára a fennmaradó adatok rögzítésére. A nulla adatvesztés és az alkalmazás konzisztenciájának biztosítása érdekében dönthet úgy, hogy az áttelepítés során leállítja a számítógépet.

## <a name="why-isnt-resynchronization-exposed"></a>Miért nem elérhető az újraszinkronizálás?

Ügynök nélküli áttelepítés során, minden különbözeti ciklusban, az aktuális pillanatkép és a korábban készített pillanatkép közötti különbség van írva. Mindig ez a különbség a pillanatképek és az adatok összecsukása között. Ha egy adott szektor van írva *N* alkalommal pillanatképek között, csak az utolsó írási kell átvinni, mert mi csak az utolsó szinkronizálás érdekel. A folyamat eltér az ügynökalapú replikációtól, amelynek során minden írást nyomon követünk és alkalmazunk. Ebben a folyamatban minden különbözeti ciklus egy reszinkronizáció. Tehát nincs resynchronization lehetőség kitéve. Ha a lemezek hiba miatt nem szinkronizálódnak, a következő ciklusban lesz rögzítve. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Hogyan befolyásolja a lemorzsolódási arány az ügynök nélküli replikációt?

Mivel az ügynök nélküli replikáció összecsukódik az adatokban, a *lemorzsolódási minta* fontosabb, mint a *lemorzsolódási arány.* Amikor egy fájl van írva újra és újra, az arány nem sok hatása. Azonban egy minta, amelyben minden más szektor van írva okoz nagy lemorzsolódás a következő ciklusban. Mivel minimálisra csökkentjük az általunk átadott adatok mennyiségét, a következő ciklus ütemezése előtt lehetővé tesszük az adatok lehető legnagyobb mértékű összecsukását.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Milyen gyakran van ütemezve a replikációs ciklus?

A következő replikációs ciklus ütemezésére képlet (előző ciklusidő / 2) vagy egy óra, attól függően, hogy melyik a magasabb.

Például ha egy virtuális gép négy órát vesz igénybe egy különbözeti ciklus, a következő ciklus két óra, és nem a következő órában. A folyamat a kezdeti replikáció után azonnal más, amikor az első különbözeti ciklus t azonnal ütemezi.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Milyen hatással van az ügynök nélküli replikáció a VMware-kiszolgálókra?

Az ügynök nélküli replikáció némi teljesítményhatást eredményez a VMware vCenter Server és a VMware ESXi gazdagépeken. Mivel az ügynök nélküli replikáció pillanatképeket használ, iops-t használ a tárolón, ezért bizonyos IOPS-tárolási sávszélességre van szükség. Nem javasoljuk az ügynök nélküli replikáció használatát, ha korlátozások vannak a tárolási vagy ipp-kre a környezetben.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Eltudom végezni az UEFI-virtuális gépek ügynök nélküli migrálását az Azure Gen 2-re?

Nem. Az Azure Site Recovery használatával migrálhat ezek a virtuális gépek gen 2 Azure-beli virtuális gépekre. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Rögzíthetek virtuális gépeket az Azure rendelkezésre állási zónáiba az áttelepítéskor?

Nem. Az Azure rendelkezésre állási zónái nem támogatottak az Azure Áttelepítés áttelepítése esetén.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Milyen átviteli protokollt használ az Azure Migrate a replikáció során?

Az Azure Migrate a hálózati blokkeszköz (NBD) protokollt használja SSL titkosítással.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Mi az áttelepítéshez szükséges minimális vCenter Server-verzió?

Legalább vCenter Server 5.5 és vSphere ESXi host version 5.5-tel kell rendelkeznie.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Az ügyfelek áttelepíthetik a virtuális gépeket nem felügyelt lemezekre?

Nem. Az Azure Migrate csak felügyelt lemezekre való áttelepítést támogat (Standard HDD, Prémium szintű SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Hány virtuális gépet replikálhatok egyszerre ügynök nélküli áttelepítés használatával?

Jelenleg a vCenter-kiszolgáló példányonkénti 100 virtuális gépet telepíthet át egyszerre. 10 virtuális gép kötegeinek áttelepítése.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Hogyan szabályozhatom a replikációt az Azure Migrate appliance ügynök nélküli VMware-replikációhoz való használatában?  

A NetQosPolicy használatával szabályozást is használhat. Például:

A NetQosPolicy alkalmazásban használandó AppNamePrefix a "GatewayWindowsService.exe". Létrehozhat egy szabályzatot az Azure Migrate appliance-on a replikációs forgalom szabályozására a készülékről egy ilyen házirend létrehozásával, például:
 
New-NetQosPolicy -"ThrottleReplication" név -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Mikor telepíthetek át gépeket fizikai kiszolgálóként?

A gépek fizikai kiszolgálóként való áttelepítése számos esetben hasznos:

- Amikor a helyszíni fizikai kiszolgálók áttelepítése.
- Ha olyan platformok által virtualizált virtuális gépeket telepít át, mint az Xen, a KVM.
- Hyper-V vagy VMware virtuális gépek áttelepítéséhez, ha valamilyen okból nem tudja használni a normál áttelepítési folyamatot a [Hyper-V](tutorial-migrate-hyper-v.md)vagy a [VMware](server-migrate-overview.md) áttelepítéshez. Ha például nem a VMware vCenter t futtatja, és csak ESXi-állomásokat használ.
- Privát felhőkben futó virtuális gépek áttelepítése az Azure-ba
- Ha nyilvános felhőkben, például az Amazon Web Services (AWS) vagy a Google Cloud Platform (GCP) szolgáltatásban futó virtuális gépeket szeretne áttelepíteni az Azure-ba.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Két (vagy több) készüléket telepítettem a virtuális gépek felderítésére a vCenter-kiszolgálón. Azonban, amikor megpróbálom áttelepíteni a virtuális gépek, én csak látni a készülék egyikének megfelelő virtuális gépek.

Bár ez lehet egy jó használati eset, jelenleg nem támogatja azt. Két (vagy több) készülék üzembe helyezése ugyanazon virtuális gépek felderítéséhez szolgáltatásproblémát okoz, amelyben a virtuális gépek tulajdonjoga folyamatosan válthat a két készülék között. Ez az oka annak, hogy megjelennek és eltűnnek a virtuális gépek. Ilyen esetekben a probléma megoldásához törölnie kell egy készüléket, és gyorsfrissítést kell végeznie.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Szükségem van vMware vCenter-re a VMware virtuális gépek áttelepítéséhez?
A [VMware virtuális gépek](server-migrate-overview.md) VMware-ügynökalapú vagy ügynök nélküli áttelepítéssel történő áttelepítéséhez a vCenter Server által kezelt ESXi-állomásokat, amelyeken a virtuális gépek találhatók, kezelniük kell. Ha nem rendelkezik vCenter Server, telepítheti vmware virtuális gépek áttelepítésével őket fizikai kiszolgálók. [További információ](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Migrate áttekintését.](migrate-services-overview.md)
