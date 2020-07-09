---
title: Azure Migrate kiszolgáló áttelepítésével kapcsolatos gyakori kérdések
description: Választ kaphat a Azure Migrate kiszolgáló áttelepítésének a gépek áttelepítésére való használatával kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 0cfe23b4e544040fc3ab69796988ca34b1bdcdbf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744324"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate kiszolgáló áttelepítése: gyakori kérdések

Ez a cikk a Azure Migrate: Server áttelepítési eszközzel kapcsolatos gyakori kérdésekre ad választ. Ha további kérdései vannak, tekintse meg a következő erőforrásokat:

- [Általános kérdések](resources-faq.md) a Azure Migrate
- Az [Azure Migrate berendezéssel](common-questions-appliance.md) kapcsolatos kérdések
- A [felderítéssel, az értékeléssel és a függőségi vizualizációval](common-questions-discovery-assessment.md) kapcsolatos kérdések
- A [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum) választ kaphat kérdéseire

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Milyen földrajzi területek támogatottak a Azure Migrate való áttelepítéshez?

Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [Government-felhők](migrate-support-matrix.md#supported-geographies-azure-government) támogatott régióit.

## <a name="how-does-agentless-vmware-replication-work"></a>Hogyan működik az ügynök nélküli VMware-replikáció?

A VMware ügynök nélküli replikációs módszere a VMware-Pillanatképek és a VMware changed Block Tracking (CBT) használatával működik.

Ennek folyamata a következő:

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

Azure Migrate a hálózati blokk-eszköz (NBD) protokollt használja TLS titkosítással.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Milyen minimális vCenter Server verzió szükséges az áttelepítéshez?

Legalább vCenter Server 5,5 és vSphere ESXi-gazdagép 5,5-es verziójának kell lennie.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Az ügyfelek áttelepíthetik a virtuális gépeket a nem felügyelt lemezekre?

Nem. A Azure Migrate csak felügyelt lemezekre (standard HDD, prémium SSD) támogatja az áttelepítést.

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Hány virtuális gépet lehet egyszerre replikálni az ügynök nélküli áttelepítés használatával?

Jelenleg a 100-es virtuális gépeket a vCenter Server egy példányán egyszerre telepítheti át. Migrálás 10 virtuális gép kötegei között.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Hogyan a replikációt a Azure Migrate berendezés használatával az ügynök nélküli VMware-replikációhoz?  

A NetQosPolicy használatával szabályozhatja a szabályozást. Például:

A NetQosPolicy használandó AppNamePrefix: "GatewayWindowsService.exe". Létrehozhat egy szabályzatot az Azure Migrate berendezésen a készülék replikációs forgalmának szabályozásához egy olyan házirend létrehozásával, mint például ez:
 
New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>Áttelepíthetem azokat a virtuális gépeket, amelyek már replikálódnak az Azure-ba? 

Ha a virtuális gépeket már más módon replikálják az Azure-ba, akkor nem telepítheti át a gépeket Azure Migrate-kiszolgáló áttelepítését biztosító virtuális gépekként. Megkerülő megoldásként kezelheti a virtuális gépeket fizikai kiszolgálóként, és áttelepítheti azokat a [támogatott fizikai kiszolgáló áttelepítésének](migrate-support-matrix-physical-migration.md)megfelelően.

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Mikor telepíthetem a gépeket fizikai kiszolgálóként?

A gépek áttelepítése a fizikai kiszolgálóként való kezeléssel számos esetben hasznos:

- Helyszíni fizikai kiszolgálók áttelepítésekor.
- Ha olyan virtuális gépeket telepít át, amelyek platformokon, például a Xen-ban, a KVM-ban vannak virtualizálva.
- A Hyper-V vagy a VMware virtuális gépek áttelepítéséhez, ha valamilyen okból kifolyólag nem tudja használni a [Hyper-v](tutorial-migrate-hyper-v.md)normál áttelepítési folyamatát, vagy a [VMware](server-migrate-overview.md) áttelepítését. Ha például nem futtat VMware-vCenter, és csak ESXi-gazdagépeket használ.
- Saját felhőben futó virtuális gépek migrálása az Azure-ba
- Ha nyilvános felhőben (például Amazon Web Services (AWS) vagy Google Cloud Platform (GCP)) futó virtuális gépeket szeretne áttelepíteni az Azure-ba.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Két (vagy több) készülék üzembe helyezésével felderítem a virtuális gépeket a vCenter Server. Ha azonban megpróbálom áttelepíteni a virtuális gépeket, csak az egyik készülékhez tartozó virtuális gépeket látok.

Habár ez jó használati eset lehet, jelenleg nem támogatott. Ha két (vagy több) készüléket helyez üzembe ugyanazon virtuális gépek felderítéséhez, olyan szolgáltatási problémát okoz, amelyben a virtuális gépek tulajdonlása megtartja a két készülék közötti váltást. Itt láthatja, hogy a virtuális gépek megjelennek és eltűnnek. Ilyen esetekben a probléma megoldásához törölnie kell egy készüléket, és kemény frissítést kell végeznie.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>A VMWare virtuális gépek áttelepítéséhez VMware vCenter szükséges?
[VMWare virtuális gépek](server-migrate-overview.md) áttelepítéséhez VMware ügynök-alapú vagy ügynök nélküli Migrálás esetén az ESXi-gazdagépeket, amelyeken a virtuális gépek találhatók, vCenter Server kell felügyelni. Ha nem rendelkezik vCenter Serverekkel, áttelepítheti a VMware virtuális gépeket fizikai kiszolgálóként való áttelepítéssel. [További információk](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>További lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).
