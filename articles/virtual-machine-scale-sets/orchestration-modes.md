---
title: További információ az Azure-beli virtuálisgép-méretezési csoportokkal kapcsolatos előkészítési módokról
description: További információ az Azure-beli virtuálisgép-méretezési csoportokkal kapcsolatos előkészítési módokról.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279065"
---
# <a name="orchestration-mode-preview"></a>Előkészítési mód (előzetes verzió)

A Virtual Machines Scale sets a platform által felügyelt virtuális gépek logikai csoportosítását biztosítja. A méretezési csoportokkal létrehozhat egy virtuálisgép-konfigurációs modellt, automatikusan hozzáadhat vagy eltávolíthat további példányokat a CPU vagy a memória terhelése alapján, és automatikusan frissítheti a legújabb operációsrendszer-verzióra. A méretezési csoportok hagyományosan lehetővé teszik, hogy virtuális gépeket hozzon létre a méretezési csoport létrehozásakor megadott virtuálisgép-konfigurációs modellel, és a méretezési csoport csak a konfigurációs modell alapján implicit módon létrehozott virtuális gépeket tudja kezelni.

A méretezési csoport előkészítési üzemmódja (előzetes verzió) segítségével kiválaszthatja, hogy a méretezési csoport a méretezési csoport konfigurációs modelljén kívül létrehozott virtuális gépeket, vagy a virtuálisgép-példányokat, amelyek implicit módon lettek létrehozva a konfigurációs modell. A méretezési csoport előkészítési módja a virtuális gépek tartalék tartományokban és Availability Zonesban történő üzembe helyezésével is segíti a virtuálisgép-infrastruktúra megtervezését a magas rendelkezésre állás érdekében.


A virtuálisgép-méretezési csoportok két különböző előkészítési módot támogatnak:

- ScaleSetVM – a méretezési csoportba felvett virtuálisgép-példányok a méretezési csoport konfigurációs modelljén alapulnak. A virtuálisgép-példány életciklusa – létrehozás, frissítés, törlés – a méretezési csoport kezeli.
- Virtuális gép (Virtual machines) – a méretezési csoporton kívül létrehozott virtuális gépeket explicit módon fel lehet venni a méretezési csoport. 
 

> [!IMPORTANT]
> A skálázási mód a méretezési csoport létrehozásakor van meghatározva, és később nem módosítható és nem frissíthető. 
> 
> A virtuálisgép-méretezési csoportoknak ez a funkciója jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Vezénylési módok

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Virtuális gép konfigurációs modellje      | None                                       | Szükséges |
| Új virtuális gép felvétele a méretezési csoportba  | A virtuális gép létrehozásakor a rendszer explicit módon hozzáadja a virtuális gépeket a méretezési csoporthoz. | A virtuális gépek implicit módon jönnek létre és a méretezési csoportba kerülnek a virtuálisgép-konfigurációs modell, a példányszám és az automatikus skálázási szabályok alapján. | |
| Virtuális gép törlése                   | A virtuális gépeket külön kell törölni, a méretezési csoport nem lesz törölve, ha rendelkezik virtuális gépekkel. | A virtuális gépek egyenként törölhetők, a méretezési csoport törlése pedig törli az összes virtuálisgép-példányt.  |
| Virtuális gépek csatlakoztatása/leválasztása           | Nem támogatott                              | Nem támogatott |
| Példány életciklusa (létrehozás törléssel) | A virtuális gépek és az összetevők (például lemezek és hálózati adapterek) egymástól függetlenül kezelhetők. | A példányok és a hozzájuk tartozó összetevők (például lemezek és hálózati adapterek) implicit módon vannak elkészítve a méretezési csoport példányaihoz. Nem választhatók le és nem kezelhetők külön a méretezési csoporton kívül |
| Tartalék tartományok               | Meghatározhatja a tartalék tartományokat. 2 vagy 3 a regionális támogatás és az 5 a rendelkezésre állási zóna alapján. | Meghatározhatja a tartalék tartományokat 1-től 5 |
| Frissítési tartományok              | A frissítési tartományok automatikusan le vannak képezve a tartalék tartományokra | A frissítési tartományok automatikusan le vannak képezve a tartalék tartományokra |
| Rendelkezésre állási zónák          | Regionális telepítést vagy virtuális gépeket támogat egy rendelkezésre állási zónában | Támogatja a regionális telepítést vagy több Availability Zones; Megadhatja a zóna kiegyensúlyozási stratégiáját |
| Automatikus méretezés                   | Nem támogatott                              | Támogatott |
| Operációs rendszer frissítése                  | Nem támogatott                              | Támogatott |
| Modell frissítései               | Nem támogatott                              | Támogatott |
| Példány vezérlő            | Teljes virtuális gép vezérlése. A virtuális gépek teljes körű URI-ja az Azure-beli virtuálisgép-kezelési képességek teljes skáláját támogatja (például Azure Policy, Azure Backup és Azure Site Recovery) | A virtuális gépek a méretezési csoport függő erőforrásai. A példányok csak a méretezési csoporton keresztül érhetők el a felügyelethez. |
| Példány modellje              | Microsoft. számítás/VirtualMachines modell definíciója. | Microsoft. számítás/VirtualMachineScaleSets/VirtualMachines modell definíciója. |
| Kapacitás                    | Üres méretezési csoport hozható létre; akár 200 virtuális gép is felvehető a méretezési csoportba | A méretezési csoportok a 0-1000-es példányszámú példányok használatával határozhatók meg |
| Áthelyezés                        | Támogatott                                  | Támogatott |
| Egyetlen elhelyezési csoport = = false | Nem támogatott                          | Támogatott |


## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg a [rendelkezésre állási lehetőségek áttekintését](availability.md).
