---
title: VMware virtuális gépek és fizikai kiszolgálók feladatátvétele és feladat-visszavétele a Site Recoveryvel az Azure-ba végzett vészhelyreállítás során | Microsoft Docs
description: Ismerje meg, hogyan végezhet feladatátvételt a VMware virtuális gépek és fizikai kiszolgálók Azure-ba, és hogyan feladat-visszavételhez helyszíni hely, a vészhelyreállítás során az Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7a089b3e4d7b8a38f2bf88c8ccf6e269331589be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966278"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Átadása és visszavétele VMware virtuális gépek

Ez a cikk bemutatja, hogyan végezhet feladatátvételt egy helyszíni VMware virtuális gép (VM) a [Azure Site Recovery](site-recovery-overview.md).

Ez az az ötödik oktatóanyag egy sorozat, amely bemutatja, hogyan állíthat be vészhelyreállítást az Azure-bA a helyszíni gépek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Győződjön meg arról, hogy megfelelnek-e a VMware virtuális gép tulajdonságaihoz az Azure követelményeinek.
> * Feladatátvétel futtatása az Azure-bA.

> [!NOTE]
> Az oktatóanyagok bemutatják a legegyszerűbb telepítési út esetén. Ezek alapértelmezett beállításokat használja, ahol csak lehetséges, és ne jelenjen meg az összes lehetséges beállítások és elérési út. Ha azt szeretné, feladatátvételi részletesen megismerheti, [virtuális gépek és fizikai kiszolgálók feladatátvételét](site-recovery-failover.md).

## <a name="before-you-start"></a>Előkészületek

Végezze el az előző oktatóanyagok:

1. Győződjön meg arról, hogy [beállítása az Azure](tutorial-prepare-azure.md) vészhelyreállításhoz helyszíni VMware virtuális gépek, Hyper-V virtuális gépek és fizikai gépek az Azure-bA.
2. Készítse elő a helyi [VMware](vmware-azure-tutorial-prepare-on-premises.md) vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) vész-helyreállítási környezetet. Ha a fizikai kiszolgálók vészhelyreállítása beállítása, tekintse át a [támogatási mátrix](vmware-physical-secondary-support-matrix.md).
3. Vészhelyreállítás beállítása [VMware virtuális gépek](vmware-azure-tutorial.md), [Hyper-V virtuális gépek](hyper-v-azure-tutorial.md), vagy [fizikai gépek](physical-azure-disaster-recovery.md).
4. Futtassa a [vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md) , győződjön meg arról, hogy minden a várt módon működik.

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Feladatátvétel az Azure-bA:** A helyszíni elsődleges hely leáll, amikor feladatátvételt gépek az Azure-bA. A feladatátvételt követően Azure virtuális gépek jönnek létre a replikált adatokat.
2. **Az Azure virtuális gépek ismételt védelme:** Az Azure-ban az Azure virtuális gépek ismételt védelme, hogy megkezdődhessen a replikálás vissza a helyszíni VMware virtuális gépeket. Ismételt védelem érdekében az adatok konzisztenciájának biztosítása során a helyszíni virtuális gép ki van kapcsolva.
3. **Átadja a feladatokat a helyszíni:** Ha a helyszíni helyen működik, és fut, feladatátvételt végez az Azure-ból sikertelen.
4. **A helyszíni virtuális gépek ismételt védelme:** Miután adatokat vissza nem sikerült, ismételt védelme, amelyhez Ön részt a feladatátvételben, a helyszíni virtuális gépek, hogy megkezdődhessen a replikálás az Azure-bA.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvétel előtt győződjön meg arról, hogy a virtuális gépek megfelelnek-e, hogy a virtuális gép tulajdonságainak ellenőrzése [Azure-követelmények](vmware-physical-azure-support-matrix.md#replicated-machines).

Ellenőrizze a következő tulajdonságait:

1. A **védett elemek**válassza **replikált elemek**, és válassza ki az ellenőrizni kívánt virtuális Gépet.

2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Válassza ki **tulajdonságok** további részletek megtekintéséhez.

3. A **számítás és hálózat**, igény szerint módosíthatja ezeket a tulajdonságokat:
    * Azure-nevet
    * Erőforráscsoport
    * Cél mérete
    * [A rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md)
    * Felügyelt lemez beállításait

4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve:

    * A hálózatot és alhálózatot, amelyben az Azure virtuális gép található a feladatátvételt követően.
    * A hozzá rendelt IP-címe.

5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **beállítások** > **replikált elemek**, válassza ki a virtuális gép átadja a feladatokat, és válassza ki a kívánt **feladatátvételi**.
2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:
   * **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. Mivel a feladatátvételt követően létrehozott Azure virtuális gép rendelkezik a feladatátvétel elindításakor Site recoverybe replikált összes adattal a legalacsonyabb helyreállítási időkorlátot (RPO) biztosít.
   * **Legutóbb feldolgozott**: Ez a beállítás a virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi helyreállítási pontot. Ez a beállítás RTO alacsony (helyreállítási időre vonatkozó célkitűzés), mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozása.
   * **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás a virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi alkalmazáskonzisztens helyreállítási pontnak.
   * **Egyéni**: Ezzel a beállítással adható meg egy helyreállítási pontot.

3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** megpróbálja leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. Feladatátvételi továbbra is fennáll, akkor is, ha a Leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

Bizonyos példahelyzetekben a feladatátvételi további feldolgozás szükséges, elvégzése körülbelül 8 – 10 percet vesz igénybe. Előfordulhat, hogy láthatja, hogy már feladatátvételi teszt ideje a:

* VMware virtuális gépeken futó mobilitási szolgáltatás verziójának 9.8 régebbiek.
* Fizikai kiszolgálók.
* A VMware Linux rendszerű virtuális gépekhez.
* Hyper-V virtuális gépek védett, mint a fizikai kiszolgálókat.
* VMware virtuális gépek, amelyek nem rendelkeznek a DHCP-szolgáltatás engedélyezve van.
* A következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Ne szakítsa meg a feladatátvételt, folyamatban van. A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

## <a name="connect-to-failed-over-vm"></a>Csatlakozás átvevő virtuális Géphez

1. Ha azt szeretné, az Azure virtuális géphez való kapcsolódásra a feladatátvételt követően távoli asztal protokoll (RDP) és a Secure Shell (SSH), [győződjön meg arról, hogy teljesülnek-e a követelményeknek](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. A feladatátvételt követően nyissa meg a virtuális Gépet, és úgy ellenőrizze, hogy [csatlakozás](../virtual-machines/windows/connect-logon.md) rá.
3. Használjon **helyreállítási pont módosítása** Ha szeretné használni egy másik helyreállítási pontot a feladatátvétel után. Miután véglegesíti a feladatátvételt a következő lépésben, ez a beállítás már nem érhető el.
4. Ellenőrzést követően válassza ki a **véglegesítése** véglegesítéséhez, a helyreállítási pont a virtuális gép a feladatátvételt követően.
5. Miután véglegesítése után, a többi rendelkezésre álló helyreállítási pontok törlődnek. Ez a lépés befejezi a feladatátvétel.

>[!TIP]
> Ha a feladatátvételt követően a csatlakozási problémát tapasztal, kövesse a [hibaelhárítási útmutató](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>További lépések

A feladatátvételt követően ismételt védelme az Azure virtuális gépek, a helyszínen. Ezt követően után a virtuális gépek, a feladatátvételen és a helyszíni helyre való replikációt, a feladatokat az Azure-ból Ha készen áll.

> [!div class="nextstepaction"]
> [Azure virtuális gépek ismételt védelme](vmware-azure-reprotect.md)
> [sikertelen lesz az Azure-ból](vmware-azure-failback.md)
