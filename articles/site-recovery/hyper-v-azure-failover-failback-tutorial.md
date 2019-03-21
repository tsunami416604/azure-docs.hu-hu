---
title: Hyper-V virtuális gépek feladatátvétele és feladat-visszavétele az Azure-ba irányuló vészhelyreállítás során az Azure Site Recoveryvel | Microsoft Docs
description: Ismerje meg, hogyan végezhet feladatátvételt és feladat-visszavételt az Azure-ba irányuló vészhelyreállítás során az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ead2b83fb7bf505fe4cbb5a09a383598617066a3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104046"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Az Azure-ba replikált Hyper-V virtuális gépek feladatátvétele és feladat-visszavétele

Ez az oktatóanyag leírja, hogyan vehetők át a Hyper-V virtuális gépek feladatai az Azure-ba. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Hyper-V virtuális gép tulajdonságainak ellenőrzése abból a szempontból, hogy az megfelel-e az Azure-követelményeknek
> * Feladatátvétel futtatása az Azure-ban
> * Feladat-visszavétel az Azure-ból a helyszíni gépre
> * A helyszíni virtuális gépek visszirányú replikálása az Azure-ba való replikálás ismételt megkezdéséhez

Ez az oktatóanyag egy sorozat ötödik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait.    

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)
3. Vészhelyreállítás beállítása [Hyper-V virtuális gépekhez](tutorial-hyper-v-to-azure.md) vagy [System Center VMM-felhőben felügyelt Hyper-V virtuális gépekhez](tutorial-hyper-v-vmm-to-azure.md)
4. [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Felkészülés a feladatátvételre és a feladat-visszavételre

Gondoskodjon arról, hogy a virtuális gépen ne legyenek pillanatképek, és hogy a helyszíni virtuális gép ki legyen kapcsolva a feladat-visszavétel során. Ez elősegíti az adatok konzisztenciáját a replikáció során. A helyszíni virtuális gépet ne kapcsolja be a feladat-visszavétel során. 

A feladatátvétel és a feladat-visszavétel három fázisból áll:

1. **Feladatátvétel az Azure-bA**: Feladatátvétel a Hyper-V virtuális gépek a helyszíni helyről az Azure-bA.
2. **Feladat-Visszavételhez helyszíni**: Feladatátvétel az Azure virtuális gépek a helyszíni helyre, ha a helyszíni hely érhető el. Ekkor a rendszer megkezdi az adatok szinkronizálását az Azure-ból a helyszínre, majd ha végzett, elindítja a helyszíni virtuális gépeket.  
3. **A visszirányú replikálás a helyszíni virtuális gépek**: Után sikertelen a helyszíni, fordított replikálja a helyszíni virtuális gépek őket az Azure-ba történő elindításához.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvétel előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel az [Azure-követelményeknek](hyper-v-azure-support-matrix.md#replicated-vms).

A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.

1. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.

1. A **Számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, a [rendelkezésre állási csoportot](../virtual-machines/windows/tutorial-availability-sets.md) és a felügyelt lemez beállításait.

1. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.

1. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="failover-to-azure"></a>Feladatátvétel az Azure-ba

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza a **Legújabb** helyreállítási pontot. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery a feladatátvitel indítása előtt megkísérli leállítani a forrás virtuális gépeket. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. A feladatátvétel ellenőrzése után kattintson a **Véglegesítés** lehetőségre. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: Ha megszakítja a feladatátvételt, az leáll, a folyamatban lévő, de a virtuális gép újra nem folytatódik.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Azure-beli virtuális gép feladatainak visszavétele a helyszínre és a helyszíni virtuális gép visszirányú replikációja

A feladat-visszavétel művelete alapvetően egy feladatátvétel az Azure-ból a helyszíni helyre, a visszirányú replikáció során pedig a rendszer újra elkezdi replikálni a helyszíni virtuális gépeket az Azure-ba.

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Tervezett feladatátvétel** ikonra.
2. A **Tervezett feladatátvétel megerősítése** területen ellenőrizze a feladatátvétel irányát (az Azure-ból a helyszínre), és válassza ki a forrás- és célhelyeket.
3. Válassza ki az **Adatok szinkronizálása a feladatátvétel előtt (csak a változáskülönbözetek szinkronizálása)** lehetőséget. Ez a beállítás minimálisra csökkenti a virtuális gép állásidejét, mivel a virtuális gép leállítása nélkül szinkronizál.
4. Indítsa el a feladatátvételt. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
5. Miután a kezdeti adatszinkronizálás megtörtént, és készen áll az Azure-beli virtuális gépek leállítására, kattintson a **Feladatok** > tervezett feladatátvételi feladat neve > **Feladatátvétel befejezése** lehetőségre. Ekkor a rendszer leállítja az Azure-beli virtuális gépet, átviszi a legfrissebb módosításokat a helyszíni virtuális gépre, majd elindítja a helyszíni virtuális gépet.
6. A helyszíni virtuális gépre bejelentkezve ellenőrizze, hogy a várakozásnak megfelelően elérhető-e.
7. A helyszíni virtuális gép most **Véglegesítés függőben** állapotban van. Kattintson a **Véglegesítés** gombra. A rendszer törli az Azure-beli virtuális gépet és annak lemezeit, és előkészíti a helyszíni virtuális gépet a visszirányú replikáláshoz.
A helyszíni virtuális gép az Azure-ba való replikálásának megkezdéséhez engedélyezze a **Visszirányú replikáció** lehetőséget. Ez elindítja a változáskülönbözetek replikálását, amelyek az Azure-beli virtuális gép kikapcsolása óta történtek.  
