---
title: VMware virtuális gépek feladatátvételaz Azure-ba a Site Recovery segítségével
description: Megtudhatja, hogy miként lehet átkerülni a VMware virtuális gépeket az Azure-ba az Azure Site Recovery szolgáltatásban
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: 8501bb1a998eb08984a118bfa5d52d1e3f3e4f84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498084"
---
# <a name="fail-over--vmware-vms"></a>VMware virtuális gépek feletti feladatátvétel

Ez a cikk azt ismerteti, hogyan lehet feladatátvételegy helyszíni VMware virtuális gép (VM) az [Azure-ba](site-recovery-overview.md)az Azure Site Recovery.

Ez az ötödik oktatóanyag egy sorozatban, amely bemutatja, hogyan állíthatja be a vészhelyreállítást az Azure-ban a helyszíni gépekhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy a VMware VM-tulajdonságok megfelelnek-e az Azure követelményeinek.
> * Feladatátvétel az Azure-ba adott virtuális gépek felett.

> [!NOTE]
> Oktatóanyagok megmutatja a legegyszerűbb telepítési útvonalat egy forgatókönyv. Ahol csak lehetséges, alapértelmezett beállításokat használnak, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Ha részletesen szeretne többet megtudni a feladatátvételről, olvassa el a [Virtuális gépek és a fizikai kiszolgálók feladatátvételcímű témakört.](site-recovery-failover.md)

[Ismerje meg](failover-failback-overview.md#types-of-failover) a feladatátvétel különböző típusait. Ha helyreállítási tervben több virtuális gépet szeretne átvenni, tekintse át [ezt a cikket.](site-recovery-failover.md)

## <a name="before-you-start"></a>Előkészületek

Töltse ki az előző oktatóanyagokat:

1. Győződjön meg arról, hogy beállította az [Azure-t](tutorial-prepare-azure.md) a vmware virtuális gépek, a Hyper-V vm-ek és a fizikai gépek helyszíni vészhelyreállításához az Azure-ba.
2. Készítse elő a helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md) környezetet a vészhelyreállításhoz. 
3. Állítsa be a vészhelyreállítást a [VMware virtuális gépekhez.](vmware-azure-tutorial.md)
4. [Futtasson vész-helyreállítási gyakorlatot,](tutorial-dr-drill-azure.md) hogy megbizonyosodjon arról, hogy minden a várt módon működik.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

Feladatátvétel futtatása előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gépek megfelelnek az [Azure követelményeinek.](vmware-physical-azure-support-matrix.md#replicated-machines)

Ellenőrizze a tulajdonságokat az alábbiak szerint:

1. A **Védett elemek csoportban**válassza **a Replikált elemek**lehetőséget, majd jelölje ki az ellenőrizni kívánt virtuális gépét.

2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. További részletek megtekintéséhez válassza a **Tulajdonságok** lehetőséget.

3. A **Számítás és hálózat alkalmazásban**szükség szerint módosíthatja ezeket a tulajdonságokat:
    * Azure-név
    * Erőforráscsoport
    * Cél mérete
    * [Rendelkezésre állási készlet](../virtual-machines/windows/tutorial-availability-sets.md)
    * Felügyelt lemez beállításai

4. Megtekintheti és módosíthatja a hálózati beállításokat, többek között a következőket:

    * A hálózat és az alhálózat, amelyben az Azure virtuális gép lesz található feladatátvétel után.
    * A hozzá rendelt IP-cím.

5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A**Replikált elemek** **beállításai** > ban jelölje ki a feladatátvételt megadó virtuális gépét, majd válassza a **Feladatátvétel**lehetőséget.
2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:
   * **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállításipont-célkitűzést (RPO) biztosítja, mert az Azure virtuális gép, amely feladatátvétel után jön létre, rendelkezik az összes olyan adatkal, amely replikált a Site Recovery-be a feladatátvétel aktiválásakor.
   * **Legutóbbi feldolgozott:** Ez a beállítás nem felel meg a virtuális gép nek a Site Recovery által feldolgozott legújabb helyreállítási pont. Ez a beállítás alacsony RTO (helyreállítási idő célkitűzés) lehetőséget biztosít, mert a feldolgozatlan adatok feldolgozásával nem töltött idő.
   * **Legújabb alkalmazás-konzisztens:** Ez a beállítás nem felel meg a virtuális gép át a legújabb alkalmazás-konzisztens helyreállítási pont által feldolgozott Site Recovery.
   * **Egyéni**: Ez a beállítás lehetővé teszi a helyreállítási pont megadását.

3. Válassza **a Számítógép leállítása a feladatátvétel megkezdése előtt** lehetőséget a forrásvirtuális gépek leállításához a feladatátvétel elkísérése előtt. A feladatátvétel akkor is folytatódik, ha a leállítás sikertelen. A feladatátvételi folyamatot a Feladatok lapon **követheti.**

Bizonyos esetekben a feladatátvétel további feldolgozást igényel, amely körülbelül 8–10 percet vesz igénybe. Előfordulhat, hogy hosszabb tesztelési feladatátvételi időket észlel:

* A 9.8-nál régebbi Mobility szolgáltatásverziót futtató VMware virtuális gépek.
* Fizikai szerverek.
* VMware Linux virtuális gépek.
* A fizikai kiszolgálókként védett hyper-V virtuális gépek.
* VMware virtuális gépek, amelyek nem rendelkeznek a DHCP-szolgáltatás engedélyezve van.
* VMware virtuális gépek, amelyek nem rendelkeznek a következő rendszerindító illesztőprogramokkal: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt. A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

## <a name="connect-to-failed-over-vm"></a>Csatlakozás a feladatátvételt átvevő virtuális géphez

1. Ha a Távoli asztali protokoll (RDP) és a Secure Shell (SSH) használatával feladatátvétel után szeretne csatlakozni az Azure-beli virtuális gépekhez, [ellenőrizze, hogy a követelmények teljesültek-e]((ailover-failback-overview.md#connect-to-azure-after-feladatátvétel).
2. Feladatátvétel után lépjen a virtuális gépre, és ellenőrizze, hogy [csatlakozik](../virtual-machines/windows/connect-logon.md) hozzá.
3. Használja **a Helyreállítási pont módosítása,** ha egy másik helyreállítási pontot szeretne használni a feladatátvétel után. Miután véglegesíti a feladatátvételt a következő lépésben, ez a beállítás már nem lesz elérhető.
4. Az ellenőrzés után válassza **a Véglegesítés** lehetőséget a virtuális gép helyreállítási pontjának véglegesítéséhez a feladatátvétel után.
5. A véglegesítés után az összes többi elérhető helyreállítási pont törlődik. Ez a lépés befejezi a feladatátvételt.

>[!TIP]
> Ha a feladatátvétel után kapcsolódási problémákmerülnek fel, kövesse a [hibaelhárítási útmutatót.](site-recovery-failover-to-azure-troubleshoot.md)

## <a name="next-steps"></a>További lépések

Feladat-átvétel után az Azure-beli virtuális gépek a helyszíni. Ezt követően a virtuális gépek újra védett, és replikálja a helyszíni helyre, feladat-vissza az Azure-ból, ha készen áll.

> [!div class="nextstepaction"]
> [Az Azure-beli virtuális gépek](vmware-azure-reprotect.md)
> [visszavétele az Azure-ból](vmware-azure-failback.md)
