---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748855"
---
Ez a cikk áttekintést nyújt az Azure virtuális gépek (VM-ek) rendelkezésre állási funkcióiról.

## <a name="high-availability"></a>Magas rendelkezésre állás

A számítási feladatok általában különböző virtuális gépek között vannak elosztva a nagy átviteli, teljesítmény- és redundancia létrehozása érdekében, ha egy frissítés vagy más esemény miatt a virtuális gép érintett. 

Kevés lehetőség, hogy az Azure rendelkezik a magas rendelkezésre állás eléréséhez. Először beszéljünk az alapvető konstrukciókról. 

### <a name="availability-zones"></a>Rendelkezésre állási zónák

[A rendelkezésre állási zónák kibővítik](../articles/availability-zones/az-overview.md) a vezérlőszintet, amelyet a virtuális gépeken lévő alkalmazások és adatok rendelkezésre állásának fenntartásához kell megtartania. Az elérhetőségi zóna egy fizikailag különálló zóna, egy Azure-régión belül. Támogatott Azure-régiónként három rendelkezésre állási zóna van. 

Mindegyik rendelkezésre állási zóna különálló áramforrással, hálózattal és hűtéssel rendelkezik. A megoldások at a zónákban replikált virtuális gépek használatára való megoldásként megvédheti az alkalmazásokat és az adatokat az adatközpont elvesztésétől. Ha az egyik zóna sérül, majd replikált alkalmazások és adatok azonnal elérhetők egy másik zónában. 

![Rendelkezésre állási zónák](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

További információ a [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) vagy [Linux virtuális](../articles/virtual-machines/linux/create-cli-availability-zone.md) gép rendelkezésre állási zónában történő központi telepítéséről.


### <a name="fault-domains"></a>Tartalék tartományok

A tartalék tartomány a mögöttes hardverelemek logikus csoportja, ahol az áramforrás és a hálózati kapcsoló közös, a helyszíni adatközpontok állványaihoz hasonlóan. 

### <a name="update-domains"></a>Frissítési tartományok

A frissítési tartomány a mögöttes hardverelemek logikus csoportja, amelyen egyszerre végezhető karbantartás vagy újraindítás. 

Ez lehetővé teszi, hogy az alkalmazás legalább egy példánya mindig fusson akkor is, ha az Azure platformon épp rendszeres karbantartás folyik. Előfordulhat, hogy az újraindított frissítési tartományok sorrendje nem folytatódik egymás után a karbantartás során, de egyszerre csak egy frissítési tartomány tindít.


## <a name="virtual-machines-scale-sets"></a>Virtuális gépek méretezési készletei 

Az Azure virtuálisgép-méretezési készletek segítségével hozhat létre és kezelhet egy csoport terheléselosztásos virtuális gépek. A virtuálisgép-példányok száma automatikusan növelhető vagy csökkenthető a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés szerint. A méretezési csoportok magas rendelkezésre állást biztosítanak az alkalmazások számára, és lehetővé teszik számos virtuális gép központi kezelését, konfigurálását és frissítését. Azt javasoljuk, hogy két vagy több virtuális gép jön létre egy méretezési csoporton belül, hogy egy magas rendelkezésre állású alkalmazás, és megfelel a [99,95%-os Azure SLA.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Nincs költség a méretezési készlet maga, csak akkor kell fizetnie minden virtuálisgép-példány, amely létrehozott. Ha egyetlen virtuális gép [azure prémium szintű SSD-ket](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)használ, az Azure SLA nem tervezett karbantartási eseményekre vonatkozik. A méretezési csoportban lévő virtuális gépek több frissítési tartományban és tartalék tartományban is telepíthetők, így maximalizálhatók az adatközpont-kimaradások és a tervezett vagy nem tervezett karbantartási események miatti kimaradások és a kimaradások közötti rugalmasság maximalizálása érdekében. A méretezési csoportban lévő virtuális gépek egyetlen rendelkezésre állási zónába vagy regionálisan is üzembe helyezhetők. A rendelkezésre állási zóna telepítési beállításai a vezénylési módtól függően eltérőek lehetnek.

### <a name="preview-orchestration-mode-preview"></a>Előzetes verzió: Vezénylési mód előzetes verziója
A virtuális gépek méretezési készletei lehetővé teszik a vezénylési mód megadását.  A virtuálisgép-méretezési csoport vezénylési mód (előzetes verzió) most már kiválaszthatja, hogy a méretezési csoport kell vezénylni a virtuális gépek, amelyek kifejezetten kívül létrehozott méretezési csoport konfigurációs modell, vagy a virtuális gép példányok implicit módon létrehozott a konfigurációs modell alapján. Válassza ki azt a vezénylési módot, amelyet a VM vezénylési modellje lehetővé tesz explicit módon definiált virtuális gépek csoportosítására egy régióban vagy egy rendelkezésre állási zónában. A rendelkezésre állási zónában üzembe helyezett virtuális gépek zónaszintű elkülönítést biztosít a virtuális gépek számára, amelyek a rendelkezésre állási zóna határához vannak kötve, és nem vannak kitéve olyan hibáknak, amelyek a régió más rendelkezésre állási zónájában fordulhatnak elő. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Virtuális gép konfigurációs modellje| Nincs. VirtualMachineProfile nincs definiálva a méretezési csoport modellben. | Kötelező. VirtualMachineProfile van feltöltve a méretezési csoport modellben. |
| Új virtuális gép hozzáadása a méretezési készlethez| A virtuális gépek explicit módon hozzáadódnak a méretezési készlethez a virtuális gép létrehozásakor. | Virtuális gépek implicit módon jönnek létre, és hozzáadódnak a méretezési csoport hoz a virtuális gép konfigurációs modellje, a példányok száma és az automatikus skálázási szabályok alapján. |
| Rendelkezésre állási zónák| Támogatja a regionális üzembe helyezést vagy virtuális gépeket egy rendelkezésre állási zónában| Támogatja a regionális telepítést vagy több rendelkezésre állási zónát; Meghatározhatja a zónakiegyensúlyozási stratégiát |
| Tartalék tartományok| Definiálhatja a tartalék tartományok számát. 2 vagy 3 a regionális támogatás és 5 a rendelkezésre állási zóna alapján. A hozzárendelt virtuálisgép-tartalék tartomány megmarad a virtuális gép életciklusa, beleértve a felszabadítása és újraindítása. | 1, 2 vagy 3 tartalék tartománydefiniálhat nem zónaszintű központi telepítésekhez, és 5 a rendelkezésre állási zóna központi telepítéseihez. A hozzárendelt virtuálisgép-tartalék tartomány nem marad meg a virtuális gép életciklusa, a virtuális gépek egy tartalék tartomány hozzárendelése a foglalás időpontjában. |
| Frissítési tartományok| N/A. A frissítési tartományok automatikusan tartalék tartományokhoz vannak rendelve| N/A. A frissítési tartományok automatikusan tartalék tartományokhoz vannak rendelve |

**Tartalék tartományok és frissítési tartományok**

A virtuálisgép-méretezési csoportok a tartalék tartományok és a tartományok frissítésének összehangolásával leegyszerűsítik a magas rendelkezésre állás ú tervezését. Csak meg kell adnia a tartalék tartományok száma a méretezési készlet. A méretezési csoportok számára elérhető tartalék tartományok száma régiónként eltérő lehet. Lásd: [A virtuális gépek elérhetőségének kezelése az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)


## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A rendelkezésre állási csoport a virtuális gépek egy adatközponton belüli logikai csoportosítása, amely lehetővé teszi az Azure számára, hogy megértse, hogyan épül fel az alkalmazás a redundancia és a rendelkezésre állás biztosítása érdekében. Azt javasoljuk, hogy két vagy több virtuális gép jön létre egy rendelkezésre állási csoporton belül, hogy egy magas rendelkezésre állású alkalmazás, és megfelel a [99,95%-os Azure SLA.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Nincs költség a rendelkezésre állási csoport maga, csak akkor kell fizetnie minden virtuálisgép-példány, amely létrehozott. Ha egyetlen virtuális gép [azure prémium szintű SSD-ket](../articles/virtual-machines/windows/disks-types.md#premium-ssd)használ, az Azure SLA nem tervezett karbantartási eseményekre vonatkozik.

Egy rendelkezésre állási csoportban a virtuális gépek automatikusan elosztva ezek a tartalék tartományok között. Ez a módszer korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy a tápellátás megszakadásának hatását.

Az [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) használatával létrehozott virtuális gépek felügyelt rendelkezésre állási csoportok használata esetén felügyelt lemezes tartalék tartományokra vannak elosztva. Ez a kiosztás biztosítja, hogy a virtuális géphez csatolt mindegyik felügyelt lemez ugyanabban a felügyelt lemezes tartalék tartományban legyen. 

Kizárólag felügyelt lemezeken futó virtuális gépek hozhatók létre felügyelt rendelkezésre állási csoportokban. A felügyelt lemezes tartalék tartományok száma régiónként eltérő – régiónként kettő vagy három felügyelt lemezes tartalék tartomány lehet. Ezekről a [linuxos virtuális gépekhez](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) vagy Windows virtuális gépekhez felügyelt lemeztartalék tartományokról olvashat [bővebben.](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)

![Felügyelt rendelkezésre állási készlet](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Egy rendelkezésre állási csoporton belüli virtuális gépek is automatikusan elosztva frissítési tartományok között. 

![Rendelkezésre állási csoportok](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>További lépések
Mostantól a saját Azure-környezetében is használhatja ezeket a rendelkezésre állási és redundanciával kapcsolatos szolgáltatásokat. Javasoljuk, hogy tájékozódjon [az Azure rendelkezésre állásával kapcsolatos ajánlott eljárásokról](/azure/architecture/checklist/resiliency-per-service).

