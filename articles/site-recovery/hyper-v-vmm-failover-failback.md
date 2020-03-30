---
title: Feladatátvétel/feladat-visszavétel beállítása egy másodlagos Hyper-V-helyre az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan lehet feladatátvételi hyper-V virtuális gépek a másodlagos helyszíni helyen, és a feladat-vissza az elsődleges hely, az Azure Site Recovery vész-helyreállítási során.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082602"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Feladatátvétel és feladat-visszavétel a másodlagos helyszíni helyre replikált Hyper-V virtuális gépek

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás kezeli és vezényli a replikációt, a feladatátvételt és a feladat-visszavételt a helyszíni gépek, valamint az Azure virtuális gépek (VM-ek) számára.

Ez a cikk azt ismerteti, hogy miként lehet feladatátvételt egy System Center Virtuálisgép-kezelő (VMM) felhőben egy másodlagos VMM-helyen kezelt Hyper-V VM-szolgáltatás felett. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hyper-V virtuális gép elsődleges VMM-felhőből másodlagos VMM-felhőbe történő átvétele
> * A másodlagos helyről az elsődleges helyre való újbóli védelem és a feladat-visszavétel
> * Igény szerint kezdje el újra replikálni az elsődlegesről a másodlagosra

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

A feladatátvétel és a feladat-visszavétel három szakaszból áll:

1. **Feladatátvétel a másodlagos helyre:** A gépek átadása az elsődleges helyről a másodlagosra.
2. **Feladat-visszavétel a másodlagos helyről:** Replikálja a virtuális gépeket a másodlagosról az elsődlegesre, és futtasson egy tervezett feladatátvételt feladat-visszavételre.
3. A tervezett feladatátvétel után, opcionálisan kezdje el a replikálás az elsődleges helyről a másodlagos újra.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy befejezte a [vész-helyreállítási gyakorlatot,](hyper-v-vmm-test-failover.md) hogy ellenőrizze, hogy minden a várt módon működik-e.
- A feladat-visszavétel befejezéséhez győződjön meg arról, hogy az elsődleges és másodlagos VMM-kiszolgálók a Site Recovery-hez csatlakoznak.



## <a name="run-a-failover-from-primary-to-secondary"></a>Feladatátvétel futtatása az elsődlegesről a másodlagosra

Futtathat egy rendszeres vagy tervezett feladatátvételt a Hyper-V virtuális gépekhez.

- A nem várt leállások rendszeres feladatátvételt használjon. A feladatátvétel futtatásakor a Site Recovery létrehoz egy virtuális gép a másodlagos helyen, és bekapcsolja azt. Adatvesztés a nem szinkronizált függőadatoktól függően fordulhat elő.
- A tervezett feladatátvétel karbantartásra vagy a várható kimaradás során használható. Ez a beállítás nulla adatvesztést biztosít. Ha egy tervezett feladatátvétel aktiválódik, a forrás virtuális gépek leáll. A nem szinkronizált adatok szinkronizálva vannak, és a feladatátvétel aktiválódik. 
- 
  Ez az eljárás a rendszeres feladatátvétel futtatását ismerteti.


1. A**Replikált elemek** **beállításai** > ban kattintson a virtuális gép > **feladatátvétel**.
1. Válassza **a Számítógép leállítása a feladatátvétel megkezdése előtt** lehetőséget, ha azt szeretné, hogy a Site Recovery a feladatátvétel elírása előtt megkísérli a forrásvirtuális gépek leállítását. A Site Recovery megpróbálja szinkronizálni azokat a helyszíni adatokat is, amelyekmég nem lettek elküldve a másodlagos helyre, mielőtt a feladatátvételt kezdeményezte volna. Vegye figyelembe, hogy a feladatátvétel akkor is folytatódik, ha a leállítás sikertelen. A feladatátvételi folyamatot a Feladatok lapon **követheti.**
2. Most már látnia kell a virtuális gép a másodlagos VMM-felhőben.
3. Miután ellenőrizte a virtuális gép, **véglegesítse** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.  


## <a name="reverse-replicate-and-failover"></a>Fordított replikálás és feladatátvétel

Kezdje el a replikálást a másodlagos helyről az elsődleges helyre, és feladatvissza az elsődleges helyre. Miután a virtuális gépek ismét futnak az elsődleges helyen, replikálhatja őket a másodlagos helyre.  

 
1. Kattintson a virtuális gépre> kattintson a **Reverse Replicate gombra.**
2. Miután a feladat befejeződött, kattintson a virtuális gép >**a feladatátvétel,** ellenőrizze a feladatátvételi irányt (a másodlagos VMM-felhőből), és válassza ki a forrás- és célhelyeket. 
4. Indítsa el a feladatátvételt. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
5. Az elsődleges VMM-felhőben ellenőrizze, hogy a virtuális gép elérhető-e.
6. Ha ismét vissza szeretné replikálni az elsődleges virtuális gépreplikálását a másodlagos helyre, kattintson a **Fordított replikálás gombra.**

## <a name="next-steps"></a>További lépések
[Tekintse át a hyper-v](hyper-v-vmm-disaster-recovery.md) virtuális gépek replikálásának lépése egy másodlagos helyre.
