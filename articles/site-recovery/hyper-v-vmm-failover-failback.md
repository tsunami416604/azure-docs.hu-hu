---
title: Átadása és visszavétele Hyper-V virtuális gépek replikálása egy másodlagos adatközpontba vészhelyreállítás az Azure Site Recoveryvel |} A Microsoft Docs
description: Ismerje meg, hogy a másodlagos helyszíni helyre Hyper-V virtuális gépek feladatainak átadása és feladat-visszavételhez elsődleges helyet, az Azure Site Recovery a vészhelyreállítás során.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: ef360fd56c3530a49c2f36846db34b29ca1cf444
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788018"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Átadása és visszavétele Hyper-V virtuális gépek replikálása a másodlagos helyszíni helyre

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás felügyeli, és koordinálja a replikálása, feladatátvétele és feladat-visszavételhez helyszíni gépek és Azure-beli virtuális gépek (VM).

Ez a cikk azt ismerteti, hogyan végezhet feladatátvételt a másodlagos VMM-helyre a System Center Virtual Machine Manager (VMM) felhőben felügyelt Hyper-V virtuális gép. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hyper-V virtuális gép feladatainak átadásához elsődleges VMM-felhőhöz a másodlagos VMM-felhőhöz
> * Ismételt védelem – a másodlagos hely az elsődleges és a feladat-visszavételt
> * Igény szerint replikáljon az elsődleges, másodlagos újra

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

Feladatátvétel és feladat-visszavétel három fázisa van:

1. **Másodlagos hely feladatátvételt**: Gépek átvétele az elsődleges helyről a másodlagos.
2. **Nem sikerült a másodlagos helyről**: Virtuális gépek replikálása az elsődleges, másodlagos, és a tervezett feladatátvételt a feladat-visszavételt.
3. A tervezett feladatátvétel után igény szerint replikáljon az elsődleges helyről a másodlagos újra.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy befejezte egy [vészhelyreállítási próba végrehajtása](hyper-v-vmm-test-failover.md) ellenőrizze, hogy minden a várt módon működik.
- Feladat-visszavétel befejezése, győződjön meg róla, hogy az elsődleges és másodlagos VMM-kiszolgálók csatlakoznak a Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Feladatátvétel futtatása az elsődleges, másodlagos

A Hyper-V virtuális gépek rendszeres vagy tervezett feladatátvételt futtathat.

- Egy normál feladatátvevő használata a váratlan leállásokat. A feladatátvétel futtatásakor a Site Recovery létrehoz egy virtuális Gépet a másodlagos helyen, és használja azt fel. Adatvesztés fordulhat elő, attól függően, függőben lévő adatokat, amelyek még nem lett szinkronizálva.
- Egy tervezett feladatátvételt a karbantartás, illetve várt leállás során használható. Ezt a beállítást itt nincs adatvesztés. Egy tervezett feladatátvételt akkor aktiválódik, ha a forrásoldali virtuális gép leállt. Nem szinkronizált adatok szinkronizálása, és akkor indul el, a feladatátvételt. 
- 
Ez az eljárás ismerteti, hogyan futtathat egy normál feladatátvevő.


1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.
1. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a Site Recovery megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A Site Recovery megpróbálja is, amely még nem még küldte el a másodlagos helynek, a feladatátvétel indítása előtt a helyszíni adatok szinkronizálása. Vegye figyelembe, hogy a feladatátvétel továbbra is fennáll, még akkor is, ha a Leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
2. Most kell látni a virtuális Gépet a másodlagos VMM-felhőben.
3. Miután ellenőrizte a virtuális gép **véglegesítése** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.  


## <a name="reverse-replicate-and-failover"></a>A visszirányú replikálás és feladatátvétel

A másodlagos helyről az elsődleges replikálásának megkezdéséhez, és a feladat-visszavételhez az elsődleges helyen. Ha virtuális gépeket újra az elsődleges helyen futnak, a másodlagos helyre replikálhatja azokat.  

 
1. Kattintson a virtuális Gépre > kattintson a **visszirányú replikálása**.
2. Ha a feladat befejeződött, kattintson a virtuális Gépre > a **feladatátvételi**, ellenőrizze a feladatátvétel iránya (a másodlagos VMM-felhőben), és válassza ki a forrás- és helyek. 
4. Indítsa el a feladatátvételt. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
5. Az elsődleges VMM-felhő ellenőrizze, hogy elérhető legyen-e a virtuális gép.
6. Ha azt szeretné, az elsődleges virtuális gép replikálása a másodlagos hely újra elindítani, kattintson a **visszirányú replikálása**.

## <a name="next-steps"></a>További lépések
[Tekintse át a lépés](hyper-v-vmm-disaster-recovery.md) a Hyper-V virtuális gépek replikálása másodlagos helyre.
