---
title: A feladatátvétel és a sikertelen biztonsági Hyper-V virtuális gépek replikálása egy másodlagos adatközpontba Site Recovery szolgáltatással |} Microsoft Docs
description: Megtudhatja, hogyan Hyper-V virtuális gépek feladatátvétele a másodlagos helyszíni helyre, és vissza az elsődleges hely, az Azure Site Recovery sikertelen
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: ecb0b9395ce7071442ddf0dd976e1ca57b8be906
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>A feladatátvétel, és sikertelen lesz a másodlagos helyszíni helyre replikált Hyper-V virtuális gépek biztonsági

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás kezeli, és koordinálja a replikációt, a feladatátvételi és a feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

A cikk ismerteti, hogy áthelyezze a feladatokat a Hyper-V virtuális gépek kezelése a System Center Virtual Machine Manager (VMM) felhő, egy másodlagos VMM-helyre. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Feladatok átadása a Hyper-V virtuális gépek egy elsődleges felhőből a VMM egy másodlagos VMM-felhő
> * Állítsa a másodlagos hely az elsődleges és a feladat-visszavételt
> * Opcionálisan a replikálása az elsődleges másodlagos újra elindítani

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

A feladatátvételi és a feladat-visszavétel rendelkezik három fázisból áll:

1. **Feladatok átadása a másodlagos hely**: feladatátvételt gépeket az elsődleges helyről másodlagos.
2. **A másodlagos helyről sikertelen**: virtuális gépek replikálása az elsődleges a másodlagos, és futtassa a tervezett feladatátvételt a feladat-visszavételt.
3. A tervezett feladatátvétel után opcionálisan replikálást indítani az elsődleges helyről a másodlagos újra.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy befejezte a [vész-helyreállítási részletezési](hyper-v-vmm-test-failover.md) ellenőrizze, hogy minden a várt módon működik.
- Feladat-visszavétel befejezése, győződjön meg arról, hogy az elsődleges és másodlagos VMM-kiszolgáló csatlakozik-e a Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Futtassa a feladatátvétel az elsődleges másodlagos

Hyper-V virtuális gépek futtatása rendszeres vagy tervezett feladatátvétel.

- Váratlan leállás rendszeres feladatátvevő használja. Ennél a feladatátvételnél futtatásakor a Site Recovery hoz létre egy virtuális Gépet a másodlagos hely, és amely rendszert működtet azt. Előfordulhat adatvesztés függően függőben lévő adatokat, amelyek még nem lett szinkronizálva.
- Egy tervezett feladatátvételt a karbantartás vagy várt leállás során használható. Ez a beállítás nulla adatveszteséget biztosít. Egy tervezett feladatátvételt akkor váltódik ki, ha a forrás virtuális gépek állnak le. Nem szinkronizált adatok szinkronizálása, és akkor váltódik ki, a feladatátvételt. 
- 
Ez az eljárás rendszeres feladatátvételt ismerteti.


1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.
1. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. A Site Recovery is megpróbálja küldött még nem még a másodlagos helyet ahhoz, hogy kiváltsa a feladatátvétel a helyszíni adatok szinkronizálása. Vegye figyelembe, hogy a feladatátvétel továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
2. A virtuális Gépet a másodlagos VMM-felhőben most kell meg.
3. Miután ellenőrizte, hogy a virtuális gép **véglegesítése** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.  


## <a name="reverse-replicate-and-failover"></a>A visszirányú replikálás és a feladatátvétel

A másodlagos hely a fő replikálást indítani, és visszaadják feladataikat az elsődleges hely. Virtuális gépek futnak a az elsődleges hely újra, miután replikálhatja azokat a másodlagos helyre.  

 
1. Kattintson a virtuális gép > kattintson a **visszirányú replikálása**.
2. Ha a feladat befejeződött, kattintson a virtuális gép > a **feladatátvételi**, ellenőrizze a feladatátvételi irányba (másodlagos VMM-felhő), és válassza ki a forrás és cél helyeket. 
4. Kezdeményezze a feladatátvételt. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** fülre.
5. Az elsődleges VMM-felhő ellenőrizze, hogy rendelkezésre áll-e a virtuális gép.
6. Ha azt szeretné, az elsődleges virtuális gép újra vissza egy másodlagos hely replikálást indítani, kattintson a **visszirányú replikálása**.

## <a name="next-steps"></a>További lépések
[Tekintse át a lépés](hyper-v-vmm-disaster-recovery.md) a Hyper-V virtuális gépek replikálása másodlagos helyre.
