---
title: Azure-ba irányuló vészhelyreállítási próba végrehajtása helyszíni gépeken az Azure Site Recoveryvel | Microsoft Docs
description: Arra vonatkozó ismeretek, hogyan hajthat végre a helyszínről az Azure-ba irányuló vészhelyreállítási próbát az Azure Site Recoveryvel
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: fa66e47715940584259e5cf555f3f6cd6f07e267
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437212"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Vészhelyreállítási próba végrehajtása az Azure-ba

Ebben a cikkben bemutatjuk, hogyan futtathat egy helyszíni gépről az Azure-ba irányuló vészhelyreállítási próbát egy feladatátvételi teszt segítségével. A próba adatveszteség nélkül ellenőrzi a replikációs stratégiát.

Ez az oktatóanyag a negyedik rész abban a sorozatban, amely bemutatja, hogyan állíthat be Azure-ba irányuló vészhelyreállítást helyszíni VMware vagy Hyper-V virtuális gépekhez.

Az oktatóanyag feltételezi, hogy az első három oktatóanyag végére ért: 
    - Az [első oktatóanyagban](tutorial-prepare-azure.md) [előkészítettük az Azure-összetevőket](tutorial-prepare-azure.md) a VMware vagy Hyper-V vészhelyreállításához.
    - A második oktatóanyagban előkészítettük a helyszíni összetevőket a [VMware](vmware-azure-tutorial-prepare-on-premises.md) vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) vészhelyreállításához.
    - A harmadik oktatóanyagban beállítottuk és engedélyeztük a helyszíni [VMware virtuális gépek](vmware-azure-tutorial.md), [System Center VMM-mel rendelkező Hyper-V virtuális gépek](hyper-v-vmm-azure-tutorial.md) vagy [VMM nélküli Hyper-V virtuális gépek](hyper-v-azure-tutorial.md) replikációját.
- Az oktatóanyagokat úgy terveztük meg, hogy az adott forgatókönyvhöz a legegyszerűbb üzembehelyezési utat mutassák be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. A Site Recovery beállítása mindegyik oktatóanyagban a legegyszerűbb módon történt, ahol ez megfelelő, az alapértelmezett beállítások használatával. Ha további információt szeretne a feladatátvételi teszt lépéseiről, olvassa el az [útmutatót](site-recovery-test-failover-to-azure.md).

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Elkülönített hálózat beállítása a feladatátvételi teszthez
> * Felkészülés az Azure-beli virtuális géphez való kapcsolódásra a feladatátvételt követően
> * Feladatátvételi teszt futtatása egyetlen gép esetén

Ez az oktatóanyag

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvételi teszt futtatása előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a [Hyper-V virtuális gép](hyper-v-azure-support-matrix.md#replicated-vms) vagy a [VMware virtuális gép](vmware-physical-azure-support-matrix.md#replicated-machines) megfelel az Azure követelményeinek.

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.
2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **Számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, a rendelkezésre állási csoportot és a felügyelt lemez beállításait.
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.
5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="run-a-test-failover-for-a-single-vm"></a>Feladatátvételi teszt futtatása egyetlen virtuális gép esetén

Feladatátvételi teszt futtatásakor a következő történik:

1. A rendszer lefuttatja az előfeltételek ellenőrzését, hogy a feladatátvételhez szükséges feltételek biztosan teljesüljenek.
2. A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
3. A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

A következőképpen futtassa a feladatátvételi tesztet:

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gép > **+Feladatátvételi teszt** ikonra.
2. Ehhez az oktatóanyaghoz válassza a **Legutóbb feldolgozott** helyreállítási pontot. Ez a lehetőség a virtuális gépet a rendelkezésre álló legújabb helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
3. A **Feladatátvételi teszt** területen válassza ki, hogy az Azure virtuális gépek mely cél Azure-hálózathoz csatlakozzanak majd a feladatátvételt követően.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A folyamat előrehaladásának megtekintéséhez kattintson a virtuális gépre, és nyissa meg a tulajdonságait. Azt is megteheti, hogy a **Feladatátvételi teszt** feladatra kattint a tároló neve > **Beállítások** > **Feladatok** >
   **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, illetve, hogy fut-e.
6. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban.
7. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez kattintson a **Feladatátvételi teszt eltávolítása** elemre a virtuális gépen. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Bizonyára észrevette, hogy hosszabb a feladatátvételi teszt ideje a VMware Linux gépeken, a nem DHCP-vel kiosztott IP-című VMware virtuális gépeken, valamint a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Feladatátvétel és feladat-visszavétel futtatása helyszíni VMware virtuális gépekhez](vmware-azure-tutorial-failover-failback.md).
> [Feladatátvétel és feladat-visszavétel futtatása helyszíni Hyper-V rendszerű virtuális gépekhez](hyper-v-azure-failover-failback-tutorial.md).
