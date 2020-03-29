---
title: További információ a virtuálisgép-méretezési csoportok vezénylési módjairól az Azure-ban
description: További információ a virtuálisgép-méretezési csoportok vezénylési módjairól az Azure-ban.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279065"
---
# <a name="orchestration-mode-preview"></a>Vezénylési mód (előzetes verzió)

A virtuális gépek méretezési készletei a platform által felügyelt virtuális gépek logikai csoportosítását biztosítják. Méretezési csoportok segítségével létrehoz egy virtuálisgép-konfigurációs modellt, automatikusan hozzáad vagy eltávolít további példányokat a PROCESSZOR vagy a memória betöltése alapján, és automatikusan frissít a legújabb operációs rendszer-verzióra. Hagyományosan a méretezési csoportok lehetővé teszik, hogy virtuális gépeket hozzon létre a méretezési csoport létrehozásakor biztosított virtuális gép konfigurációs modell használatával, és a méretezési csoport csak a konfigurációs modell alapján implicit módon létrehozott virtuális gépek kezelésére képes.

A méretezési csoport vezénylési mód (előzetes verzió) most már kiválaszthatja, hogy a méretezési csoport kell vezénylni a virtuális gépek, amelyek kifejezetten kívül létrehozott méretezési csoport konfigurációs modell, vagy a virtuális gép példányok implicit módon létrehozott alapján a konfigurációs modellt. A méretezési csoport vezénylési módja is segít a virtuális gép infrastruktúrájának magas rendelkezésre állású tervezésében azáltal, hogy ezeket a virtuális gépeket a tartalék tartományokban és a rendelkezésre állási zónákban telepíti.


A virtuálisgép-méretezési csoportok 2 különböző vezénylési módot támogatnak:

- ScaleSetVM – A méretezési készlethez hozzáadott virtuálisgép-példányok a méretezési csoport konfigurációs modelljén alapulnak. A virtuálisgép-példány életciklusát - létrehozás, frissítés, törlés - a méretezési csoport kezeli.
- Virtuális gép (virtuális gépek) – a méretezési készleten kívül létrehozott virtuális gépek explicit módon hozzáadhatók a scalesethez. 
 

> [!IMPORTANT]
> A vezénylési mód a méretezési csoport létrehozásakor van definiálva, és később nem módosítható vagy frissíthető. 
> 
> Ez a szolgáltatás a virtuális gép méretezési készletek jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)


## <a name="orchestration-modes"></a>Vezénylési módok

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Virtuális gép konfigurációs modellje      | None                                       | Kötelező |
| Új virtuális gép hozzáadása a méretezési készlethez  | A virtuális gépek explicit módon hozzáadódnak a méretezési készlethez a virtuális gép létrehozásakor. | A virtuális gépek implicit módon jönnek létre, és hozzáadódnak a méretezési csoporthoz a virtuális gép konfigurációs modellje, a példányok száma és az automatikus skálázási szabályok alapján | |
| Virtuális gép törlése                   | A virtuális gépeket egyenként kell törölni, a méretezési készlet nem törlődik, ha van benne virtuális gépek. | Virtuális gépek egyenként törölhetők, a méretezési csoport törlése törli az összes virtuálisgép-példányok.  |
| Virtuális gépek csatlakoztatása/leválasztása           | Nem támogatott                              | Nem támogatott |
| Példány életciklusa (létrehozás törlés útján) | A virtuális gépek és azok összetevői (például a lemezek és a hálózati adapterek) egymástól függetlenül kezelhetők. | A példányok és azok összetevői (például a lemezek és a hálózati adapterek) implicit módon a méretezési csoport példányait, amelyek létrehozzák őket. Nem választhatók le és nem kezelhetők külön a méretezési készleten kívül |
| Tartalék tartományok               | Definiálhatja a tartalék tartományokat. 2 vagy 3 a regionális támogatás és 5 a rendelkezésre állási zóna alapján. | 1-től 5-ig haladó tartalék tartományokat határozhat meg |
| Frissítési tartományok              | A frissítési tartományok automatikusan tartalék tartományokhoz vannak rendelve | A frissítési tartományok automatikusan tartalék tartományokhoz vannak rendelve |
| Rendelkezésre állási zónák          | Támogatja a regionális üzembe helyezést vagy virtuális gépeket egy rendelkezésre állási zónában | Támogatja a regionális telepítést vagy több rendelkezésre állási zónát; Meghatározhatja a zónakiegyensúlyozási stratégiát |
| Automatikus méretezés                   | Nem támogatott                              | Támogatott |
| Operációs rendszer frissítése                  | Nem támogatott                              | Támogatott |
| Modellfrissítések               | Nem támogatott                              | Támogatott |
| Példányvezérlő            | Teljes virtuális gép vezérlése. A virtuális gépek teljesen minősített URI-val rendelkeznek, amely támogatja az Azure virtuális gép kezelési képességeinek teljes skáláját (például az Azure Policy, az Azure Backup és az Azure Site Recovery) | A virtuális gépek a méretezési készlet függő erőforrásai. Példányok csak a méretezési csoporton keresztül érhető el a felügyelethez. |
| Példánymodell              | Microsoft.Compute/VirtualMachines modelldefiníció. | Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines modelldefiníció. |
| Kapacitás                    | Üres méretezési készlet hozható létre; legfeljebb 200 virtuális gép adható hozzá a méretezési készlethez | A méretezési csoportok 0 és 1000 példányszámmal határozhatók meg. |
| Áthelyezés                        | Támogatott                                  | Támogatott |
| Egy elhelyezési csoport == hamis | Nem támogatott                          | Támogatott |


## <a name="next-steps"></a>További lépések

További információt a [Rendelkezésre állási lehetőségek áttekintése című](availability.md)témakörben talál.
