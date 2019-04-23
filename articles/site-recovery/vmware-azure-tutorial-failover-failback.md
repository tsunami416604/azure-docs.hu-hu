---
title: VMware virtuális gépek és fizikai kiszolgálók feladatátvétele és feladat-visszavétele a Site Recoveryvel az Azure-ba végzett vészhelyreállítás során | Microsoft Docs
description: Megtudhatja, hogy végezheti el VMware virtuális gépek és fizikai kiszolgálók feladatátvételét az Azure-ba, illetve a feladatok visszavételét a helyszíni kiszolgálókra az Azure Site Recoveryvel végzett, Azure-ba történő vészhelyreállítás során
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797665"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Átadása és visszavétele VMware virtuális gépek

Ez a cikk azt ismerteti, hogyan végezhet feladatátvételt egy helyszíni VMware virtuális gép az Azure-bA [Azure Site Recovery](site-recovery-overview.md) szolgáltatás. 

Ez az az ötödik oktatóanyag egy sorozat, amely bemutatja, hogyan állíthat be vészhelyreállítást az Azure-bA a helyszíni gépek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A VMware virtuális gép tulajdonságainak ellenőrzése abból a szempontból, hogy az megfelel-e az Azure-követelményeknek
> * Feladatátvétel futtatása az Azure-ban


> [!NOTE]
> Az oktatóanyagok bemutatják a legegyszerűbb telepítési út esetén. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Ha azt szeretné, ismerje meg részletesen, feladatátvételi [ebből a cikkből](site-recovery-failover.md).

## <a name="before-you-start"></a>Előkészületek
Végezze el az előző oktatóanyagok:

1. Győződjön meg arról, hogy [beállítása az Azure](tutorial-prepare-azure.md) vészhelyreállításhoz helyszíni VMware virtuális gépek, Hyper-V virtuális gépek és fizikai gépek az Azure-bA.
2. Készítse elő a helyi [VMware](vmware-azure-tutorial-prepare-on-premises.md) vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) vész-helyreállítási környezetet. Ha a fizikai kiszolgálók vészhelyreállítása beállítása, tekintse át a [támogatási mátrix](vmware-physical-secondary-support-matrix.md).
3. Vészhelyreállítás beállítása [VMware virtuális gépek](vmware-azure-tutorial.md), [Hyper-V virtuális gépek](hyper-v-azure-tutorial.md), vagy [fizikai gépek](physical-azure-disaster-recovery.md).
4. Futtassa a [vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md) , győződjön meg arról, hogy minden a várt módon működik.


## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Az Azure-bA feladatátvételt**: A helyszíni elsődleges hely leáll, amikor feladatátvételt gépek az Azure-bA. A feladatátvételt követően Azure virtuális gépek jönnek létre a replikált adatokat.
2. **Az Azure virtuális gépek ismételt védelme**: Az Azure-ban az Azure virtuális gépek ismételt védelme, hogy megkezdődhessen a replikálás vissza a helyszíni VMware virtuális gépeket. Ismételt védelem érdekében az adatok konzisztenciájának biztosítása során a helyszíni virtuális gép ki van kapcsolva.
3. **Átadja a feladatokat a helyszíni**: Ha a helyszíni helyen működik, és fut, feladatátvételt végez az Azure-ból sikertelen.
4. **Az ismételt védelem a helyszíni virtuális gépek**: Miután adatokat vissza nem sikerült, ismételt védelme, amelyhez Ön részt a feladatátvételben, a helyszíni virtuális gépek, hogy megkezdődhessen a replikálás az Azure-bA.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvétel előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gépek megfelelnek a [Azure-követelmények](vmware-physical-azure-support-matrix.md#replicated-machines).

Ellenőrizze a következő tulajdonságait:

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.

2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.

3. A **számítás és hálózat**, módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md), és a felügyelt lemez beállításait

4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.

5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási időkorlátot (RPO) nyújtja, mert a feladatátvétel után létrehozott Azure-beli virtuális gép rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.
   - **Legutóbb feldolgozott**: Ez a beállítás a virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi helyreállítási pontot. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás nem sikerül a virtuális Gépet a Site Recovery által feldolgozott legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** való megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Előfordulhat, hogy láthatja, hogy már feladatátvételi teszt ideje a:
- VMware virtuális gépeken futó mobilitási szolgáltatás verziójának 9.8 régebbiek
- Fizikai kiszolgálók
- A VMware Linux rendszerű virtuális gépek
- Fizikai kiszolgálóként védett Hyper-V virtuális gépek
- VMware virtuális gépek, amelyek nem rendelkeznek a DHCP-szolgáltatás engedélyezve van
- A következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

## <a name="connect-to-failed-over-vm"></a>Csatlakozás virtuális gép feladatátvétele

1. Ha az Azure virtuális gépek a feladatátvételt követően RDP/SSH segítségével kapcsolódni szeretne [ezek a követelmények ellenőrzéséhez](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. A feladatátvételt követően nyissa meg a virtuális Gépet, és úgy ellenőrizze, hogy [csatlakozás](../virtual-machines/windows/connect-logon.md) rá.
3. Használjon **helyreállítási pont módosítása** Ha szeretné használni egy másik helyreállítási pontot a feladatátvétel után. Miután véglegesíti a feladatátvételt a következő lépésben, ez a beállítás már nem érhető el.
4. Ellenőrzést követően kattintson a **véglegesítése** véglegesítéséhez, a helyreállítási pont a virtuális gép a feladatátvételt követően.
5. Véglegesítés, után a rendszer törli a többi rendelkezésre álló helyreállítási pontok. Ezzel befejeződik a feladatátvétel.

>[!TIP]
> Ha a feladatátvételt követően a csatlakozási problémát tapasztal, kövesse ezt [hibaelhárítási útmutató](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>További lépések

A feladatátvételt követően ismételt védelme az Azure virtuális gépek, a helyszínen. Ezt követően után a virtuális gépek, a feladatátvételen és a helyszíni helyre való replikációt, a feladatokat az Azure-ból Ha készen áll.

> [!div class="nextstepaction"]
> [Azure virtuális gépek ismételt védelme](vmware-azure-reprotect.md)
> [sikertelen lesz az Azure-ból](vmware-azure-failback.md) 
