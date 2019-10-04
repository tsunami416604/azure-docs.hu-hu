---
title: VMware virtuális gépek és fizikai kiszolgálók feladatátvétele és feladat-visszavétele a Site Recoveryvel az Azure-ba végzett vészhelyreállítás során | Microsoft Docs
description: Megtudhatja, hogyan hajthatja végre a VMware virtuális gépeket és a fizikai kiszolgálókat az Azure-ban, és hogyan térhet vissza a helyszíni helyre az Azure-ba való vész-helyreállítás során Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 08/22/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 852193e137eab10d1e46c5ba6ae6636d530095be
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972193"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>VMware virtuális gépek feladatátvétele és visszavétele

Ez a cikk azt ismerteti, hogyan végezhet feladatátvételt egy helyszíni VMware virtuális gépen (VM) [Azure site Recovery](site-recovery-overview.md).

Ez egy sorozat ötödik oktatóanyaga, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba a helyszíni gépekhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy a VMware virtuális gép tulajdonságai megfelelnek-e az Azure követelményeinek.
> * Futtasson feladatátvételt az Azure-ban.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Ha részletes tájékoztatást szeretne a feladatátvételről, tekintse meg a [virtuális gépek és a fizikai kiszolgálók](site-recovery-failover.md)feladatátvétele című témakört.

## <a name="before-you-start"></a>Előkészületek

Fejezze be az előző oktatóanyagokat:

1. Győződjön meg arról, hogy beállította az [Azure](tutorial-prepare-azure.md) -t a VMWare virtuális gépek, a Hyper-V virtuális gépek és a fizikai gépek helyszíni vész-helyreállításához az Azure-ban.
2. Készítse elő a helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md) vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) környezetet a vész-helyreállításhoz. Ha fizikai kiszolgálók vész-helyreállítását állítja be, tekintse át a [támogatási mátrixot](vmware-physical-secondary-support-matrix.md).
3. Állítsa be a vész-helyreállítást [VMWare virtuális](vmware-azure-tutorial.md)gépek, [Hyper-V virtuális](hyper-v-azure-tutorial.md) [gépek vagy fizikai gépek](physical-azure-disaster-recovery.md)számára.
4. A vész- [helyreállítási gyakorlat](tutorial-dr-drill-azure.md) futtatásával győződjön meg róla, hogy minden a vártnak megfelelően működik-e.

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Feladatátvétel az Azure-ba:** Ha a helyszíni elsődleges hely leáll, a meghibásodik a gépek az Azure-ba. A feladatátvételt követően az Azure-beli virtuális gépek a replikált adatokból jönnek létre.
2. **Azure-beli virtuális gépek ismételt védetté:** Az Azure-ban az Azure-beli virtuális gépeket újra kell védelemmel ellátni, hogy azok a helyszíni VMware virtuális gépekre replikálódnak. Az adatkonzisztencia biztosítása érdekében a helyszíni virtuális gép ki van kapcsolva az ismételt védelem során.
3. **Feladatátvétel a helyszíni rendszerbe:** Ha a helyszíni hely működik és fut, futtasson feladatátvételt az Azure-ból való visszatéréshez.
4. **Helyszíni virtuális gépek ismételt védetté:** Az adatműveletek visszautasítása után állítsa be újra a helyszíni virtuális gépeket, amelyeken a művelet sikertelen volt, hogy megkezdje az Azure-ba való replikálást.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvétel futtatása előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gépek megfelelnek az [Azure követelményeinek](vmware-physical-azure-support-matrix.md#replicated-machines).

A tulajdonságok ellenőrzése a következőképpen történik:

1. A **védett elemek**területen válassza a **replikált elemek**lehetőséget, majd válassza ki az ellenőrizni kívánt virtuális gépet.

2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Válassza a **Tulajdonságok** lehetőséget a további részletek megtekintéséhez.

3. A **számítás és hálózat**szolgáltatásban szükség szerint módosíthatja ezeket a tulajdonságokat:
    * Azure-beli név
    * Resource group
    * Cél mérete
    * [Rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md)
    * Felügyelt lemez beállításai

4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve a következőket:

    * A hálózat és az alhálózat, amelyben az Azure-beli virtuális gép a feladatátvétel után lesz elhelyezve.
    * Az IP-cím, amely hozzá lesz rendelve.

5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **Beállítások** > **replikált elemek**területen válassza ki azt a virtuális gépet, amelyen át szeretné adni a feladatátvételt, majd válassza a **feladatátvétel**lehetőséget.
2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:
   * **Legutóbbi**: Ez a beállítás először a Site Recoveryba elküldett összes adatfeldolgozást feldolgozza. Ez a legalacsonyabb helyreállítási időkorlátot (RPO) biztosítja, mivel a feladatátvételt követően létrehozott Azure virtuális gép minden olyan adattal rendelkezik, amelyet a feladatátvétel elindításakor Site Recovery replikált a rendszer.
   * **Legutóbb feldolgozott**: Ez a beállítás a virtuális gépet a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Ez a beállítás alacsony RTO (helyreállítási idő célkitűzés) biztosít, mivel a feldolgozatlan adatmennyiségek feldolgozása nem történik meg.
   * **Legújabb alkalmazás – konzisztens**: Ez a beállítás a virtuális gép feladatátvételét a Site Recovery által feldolgozott, az alkalmazással konzisztens legújabb helyreállítási pontra hajtja végre.
   * **Egyéni**: Ez a beállítás lehetővé teszi egy helyreállítási pont megadását.

3. A feladatátvétel elindítása előtt válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget. A feladatátvétel akkor is folytatódik, ha a leállás sikertelen. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

Bizonyos esetekben a feladatátvételhez további feldolgozásra van szükség, amely körülbelül 8 – 10 percet vesz igénybe. Előfordulhat, hogy a feladatátvételi teszt időpontját a következőre észleli:

* A 9,8-nál régebbi mobilitási szolgáltatást futtató VMware virtuális gépek.
* Fizikai kiszolgálók.
* VMware Linux rendszerű virtuális gépek.
* Fizikai kiszolgálóként védett Hyper-V virtuális gépek.
* Azok a VMware virtuális gépek, amelyeken nincs engedélyezve a DHCP szolgáltatás.
* A következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépek: storvsc, VMBus, storflt, Intelide, ATAPI.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt. A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

## <a name="connect-to-failed-over-vm"></a>Kapcsolódás a feladatátvételi virtuális géphez

1. Ha RDP protokoll (RDP) és Secure Shell (SSH) használatával szeretne feladatátvételt létesíteni az Azure virtuális gépekkel, ellenőrizze, hogy teljesülnek-e [a követelmények](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. A feladatátvétel után lépjen a virtuális gépre, és ellenőrizze, hogy [csatlakozik](../virtual-machines/windows/connect-logon.md) -e hozzá.
3. Ha a feladatátvételt követően eltérő helyreállítási pontot szeretne használni, használja a **helyreállítási pont módosítása** lehetőséget. Miután a következő lépésben véglegesíti a feladatátvételt, ez a lehetőség többé nem lesz elérhető.
4. Az ellenőrzés után válassza a **véglegesítés** lehetőséget a virtuális gép helyreállítási pontjának véglegesítéséhez a feladatátvétel után.
5. A véglegesítés után a rendszer az összes többi elérhető helyreállítási pontot is törli. Ez a lépés befejezi a feladatátvételt.

>[!TIP]
> Ha a feladatátvételt követően bármilyen csatlakozási probléma merül fel, kövesse a [hibaelhárítási útmutatót](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>További lépések

A feladatátvételt követően az Azure-beli virtuális gépeket a helyszíni környezetbe újra kell védetté tenni. Ezt követően a virtuális gépek ismételt védelemmel és a helyszíni helyre történő replikálásával visszatérhet az Azure-ból, ha elkészült.

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek](vmware-azure-reprotect.md)
> újravédése feladat[-visszavétel az Azure-ból](vmware-azure-failback.md)
