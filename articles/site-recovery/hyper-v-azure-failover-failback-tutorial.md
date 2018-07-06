---
title: Átadása és visszavétele Azure Site Recovery-bA replikált Hyper-V virtuális gépek |} A Microsoft Docs
description: Ismerje meg, hogyan végezhet feladatátvételt a Hyper-V virtuális gépek az Azure-ba, és visszaadják feladataikat a helyszíni hely, az Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: b87ec5144154714dfc8f9f2d5a06c158bc956a2d
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858537"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>Feladatátvétel és feladat-visszavétel Azure-bA replikált Hyper-V virtuális gépek

Ez az oktatóanyag azt ismerteti, hogyan lehet az Azure-bA Hyper-V virtuális gép feladatátvételi. Miután a feladatátvétel, feladat-visszavételt a helyszíni helyre elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Hyper-V virtuális gép tulajdonságainak ellenőrzése megfelelnek az Azure követelményeinek ellenőrzése
> * Feladatátvétel futtatása az Azure-ban
> * Feladat-visszavétel az Azure-ból a helyszíni
> * A visszirányú replikálás a helyszíni virtuális gépek replikálása Azure-bA indítása

Ebben az oktatóanyagban az egy sorozat ötödik oktatóanyaga. Azt feltételezi, hogy már végrehajtotta a feladatokat az előző oktatóanyagok.    

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)
3. Vészhelyreállítás beállítása [Hyper-V virtuális gépek](tutorial-hyper-v-to-azure.md), vagy a [System Center VMM-felhőkben felügyelt Hyper-V virtuális gépek](tutorial-hyper-v-vmm-to-azure.md)
4. [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>A feladatátvétel és feladat-visszavétel előkészítése

Ellenőrizze, hogy legyenek pillanatképek a virtuális gépen, és, hogy a helyszíni virtuális gép ki van kapcsolva feladat-visszavétel során. Ez segít az adatok konzisztenciájának biztosítására, a replikáció során. Ne kapcsolja be a helyszíni virtuális gép feladat-visszavétel során. 

Feladatátvétel és feladat-visszavétel van három fázisa:

1. **Feladatátvétel az Azure-bA**: feladatátvétel Hyper-V virtuális gépek a helyszíni helyről az Azure-bA.
2. **Feladat-Visszavételhez helyszíni**: feladatátvétel Azure virtuális gépek, a helyszíni helyre, ha a helyszíni hely érhető el. Elindítja az adatok az Azure-ból a helyszíni és a befejezési szinkronizálása, a virtuális gépek, a helyszíni lehetővé teszi.  
3. **A visszirányú replikálás a helyszíni virtuális gépek**: után sikertelen a helyszíni, fordított replikálása a helyszíni virtuális gépek őket az Azure-ba történő elindításához.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvétel előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel-e a [Azure-követelmények](hyper-v-azure-support-matrix.md#replicated-vms).

A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.

2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.

3. A **Számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, a [rendelkezésre állási csoportot](../virtual-machines/windows/tutorial-availability-sets.md) és a felügyelt lemez beállításait.

4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.

5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="failover-to-azure"></a>Feladatátvétel az Azure-bA

1. A **beállítások** > **replikált elemek**, kattintson a virtuális Gépre > **feladatátvételi**.
2. A **feladatátvételi**, jelölje be a **legújabb** helyreállítási pontot. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket ehhez a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Miután ellenőrizte a feladatátvétel, kattintson a **véglegesítése**. Törli az összes rendelkezésre álló helyreállítási pontok.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: Ha megszakítja a feladatátvételt, az leáll, a folyamatban lévő, de a virtuális gép újra nem folytatódik.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Feladat-visszavétel az Azure virtuális gép a helyszíni és a fordított replikáció a helyszíni virtuális gép

Feladat-visszavételi műveletet alapvetően a feladatátvétel az Azure-ból a helyszíni hely és a visszirányú replikálás újra elindítja a helyszíni helyről virtuális gépek replikálása az Azure-bA.

1. A **beállítások** > **replikált elemek**, kattintson a virtuális Gépre > **tervezett feladatátvétel**.
2. A **tervezett feladatátvétel megerősítése**, ellenőrizze a feladatátvétel iránya (az Azure-ból), és válassza ki a forrás- és helyek.
3. Válassza ki **a feladatátvétel előtt az adatok szinkronizálása (csak a változásokat szinkronizálása)**. Ezzel a beállítással minimálisra csökkenti a Virtuálisgép-leállási, mert a virtuális gép leállítása nélkül szinkronizálja.
4. Kezdeményezze a feladatátvételt. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** fülre.
5. Miután a kezdeti szinkronizálás történik, és készen áll az Azure virtuális gépek kattintson a Leállítás **feladatok** > tervezett-feladatátvétel – feladat-neve > **befejezheti a feladatátvételt**. Azt az Azure virtuális gép leáll, átviszi a legutóbbi módosítások a helyszínen, és a helyszíni virtuális gép elindul.
6. Jelentkezzen be a helyszíni virtuális Gépre, és ellenőrizze, hogy elérhető a várt módon.
7. A helyszíni virtuális gép már az egy **függőben lévő véglegesítési** állapota. Kattintson a **véglegesítése**. Az Azure virtuális gépek és annak lemezeire törli, és a helyszíni virtuális gép előkészíti a visszirányú replikálás.
A helyszíni virtuális gép Azure-ba történő indításához engedélyezése **visszirányú replikálása**. Elindítja replikációja óta az Azure virtuális gép kikapcsolva volt.  
