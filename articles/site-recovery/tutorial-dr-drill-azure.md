---
title: Azure-ba irányuló vészhelyreállítási próba végrehajtása helyszíni gépeken az Azure Site Recoveryvel | Microsoft Docs
description: Arra vonatkozó ismeretek, hogyan hajthat végre a helyszínről az Azure-ba irányuló vészhelyreállítási próbát az Azure Site Recoveryvel
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b8c8d1a867f6872c5e3ec9e1b48dac8f80c84950
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602135"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Vészhelyreállítási próba végrehajtása az Azure-ba

Ez a cikk bemutatja, hogyan futtathat vész-helyreállítási gyakorlatot egy helyszíni gépen az Azure-ba a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával. A próba adatveszteség nélkül ellenőrzi a replikációs stratégiát.


Ez egy sorozat negyedik oktatóanyaga, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba a helyszíni gépekhez.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Elkülönített hálózat beállítása a feladatátvételi teszthez
> * Felkészülés az Azure-beli virtuális géphez való kapcsolódásra a feladatátvételt követően
> * Futtasson feladatátvételi tesztet egyetlen gépen.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Ha többet szeretne megtudni a vész-helyreállítási részletezés részletes lépéseiről, tekintse [át ezt a cikket](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Előkészületek

Fejezze be az előző oktatóanyagokat:

1. Győződjön meg arról, hogy beállította az [Azure](tutorial-prepare-azure.md) -t a VMWare virtuális gépek, a Hyper-V virtuális gépek és a fizikai gépek helyszíni vész-helyreállításához az Azure-ban.
2. Készítse elő a helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md) vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) környezetet a vész-helyreállításhoz. Ha fizikai kiszolgálók vész-helyreállítását állítja be, tekintse át a [támogatási mátrixot](vmware-physical-secondary-support-matrix.md).
3. Állítsa be a vész-helyreállítást [VMWare virtuális](vmware-azure-tutorial.md)gépek, [Hyper-V virtuális](hyper-v-azure-tutorial.md) [gépek vagy fizikai gépek](physical-azure-disaster-recovery.md)számára.
 

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvételi teszt futtatása előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a [Hyper-V virtuális gép](hyper-v-azure-support-matrix.md#replicated-vms) vagy a [VMware virtuális gép](vmware-physical-azure-support-matrix.md#replicated-machines) megfelel az Azure követelményeinek.

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.
2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **Számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, a rendelkezésre állási csoportot és a felügyelt lemez beállításait.
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.
5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="create-a-network-for-test-failover"></a>Hálózat létrehozása feladatátvételi teszt számára

Azt javasoljuk, hogy a feladatátvétel teszteléséhez olyan hálózatot válasszon, amely el van különítve az éles helyreállítási oldal hálózatától, amely az egyes virtuális gépek **számítási és hálózati** beállításaiban van megadva. Alapértelmezés szerint egy Azure-beli virtuális hálózat létrehozásakor a hálózat el van különítve az egyéb hálózatoktól. A teszthálózatnak az éles hálózatot kell utánoznia:

- A teszthálózatnak ugyanannyi alhálózattal kell rendelkeznie, mint az éles hálózatnak. Az alhálózatoknak ugyanazzal a névvel kell rendelkezniük.
- A teszthálózatnak ugyanazt az IP-címtartományt kell használnia.
- Frissítse a teszthálózat DNS-ét a DNS VM-hez a **számítási és hálózati** beállítások között megadott IP-címmel. További információkért olvassa el az [Active Directoryra vonatkozó feladatátvételi szempontokat](site-recovery-active-directory.md#test-failover-considerations) ismertető cikket.

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

## <a name="connect-after-failover"></a>Csatlakozás a feladatátvétel után

Ha az Azure-beli virtuális gépeket a feladatátvételt követően RDP/SSH használatával szeretné összekapcsolni, [készüljön fel a csatlakozásra](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Ha a feladatátvételt követően bármilyen csatlakozási probléma merül fel, [](site-recovery-failover-to-azure-troubleshoot.md) kövesse a hibaelhárítási útmutatót.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Feladatátvétel és feladat-visszavétel futtatása VMWare virtuális](vmware-azure-tutorial-failover-failback.md)
> gépeken feladatátvétel és feladat-visszavétel futtatása a[Hyper-V virtuális](hyper-v-azure-failover-failback-tutorial.md)
> gépek számára feladatátvétel és feladat-visszavétel futtatása[fizikai gépek](physical-to-azure-failover-failback.md) esetén
