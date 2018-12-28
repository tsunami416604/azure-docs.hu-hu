---
title: Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépekhez (VMM nélkül) az Azure-ba az Azure Site Recovery használatával  | Microsoft Docs
description: Ismerje meg, hogyan állíthat be Azure-ba irányuló vészhelyreállítást helyszíni Hyper-V virtuális gépekhez az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 80cc1d06285ff35b8b334630cd8ec129799d6b10
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788157"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépekhez az Azure-ba

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Az oktatóanyag bemutatja, hogyan állíthatja be a helyszíni Hyper-V virtuális gépek Azure-ba irányuló vészhelyreállítását. Az oktatóanyag a System Center Virtual Machine Manager (VMM) által nem kezelt Hyper-V virtuális gépek esetén fontos. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * A forrás replikációs környezet (többek között a helyszíni Site Recovery-összetevők), valamint a cél replikálási környezet beállítása.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)

Mielőtt elkezdené, érdemes [áttekinteni az architektúrát](concepts-hyper-v-to-azure-architecture.md), hogy szerepel-e benne ez a vészhelyreállítási forgatókönyv.

## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása


1. A **Minden szolgáltatás** > **Recovery Services-tárolók** menüben válassza ki az előző oktatóanyagban előkészített tár nevét (**ContosoVMVault**).
2. Az **Első lépések** területen kattintson a **Site Recovery** elemre. Ezt követően kattintson **Az infrastruktúra előkészítése** elemre.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. Azon a területen, amely rákérdez, hogy **System Center VMM segítségével kezeli-e a Hyper-V-gazdagépeket**, válassza a **Nem** lehetőséget. Ezután kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

Ha nagy léptékű üzembe helyezésre készül, győződjön meg róla, hogy [az üzembe helyezést mindenre kiterjedően megtervezte a Hyper-V-replikációhoz](hyper-v-deployment-planner-overview.md). Ebben az oktatóanyagban a **Végzett az üzembe helyezés tervezésével?** területen válassza a **Később végzem el** lehetőséget a legördülő listából.

![Az üzembe helyezés megtervezése](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállításához hozzon létre egy Hyper-V-helyet, és adja hozzá a Hyper-V-gazdagépeket. Ezután töltse le és telepítse az Azure Site Recovery Providert és az Azure Recovery Services-ügynököt minden gazdagépen, majd regisztrálja a Hyper-V-helyet a tárolóban. 

1. **Az infrastruktúra előkészítése** területen kattintson a **Forrás** lehetőségre.
2. Kattintson a **+Hyper-V hely** elemre, majd adja meg az előző oktatóanyagban létrehozott hely nevét (**ContosoHyperVSite**).

    ![Hyper-V-hely](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. A hely létrehozása után kattintson a **+Hyper-V-kiszolgáló** elemre.

    ![Hyper-V-kiszolgáló](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Töltse le a szolgáltató telepítőfájlját.
6. Töltse le a tároló regisztrációs kulcsát. Erre a kulcsra szüksége lesz a szolgáltató telepítésének futtatásakor. A kulcs a generálásától számított öt napig érvényes.

    ![Szolgáltató letöltése](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Szolgáltató telepítése

Futtassa a szolgáltató telepítőfájlját (AzureSiteRecoveryProvider.exe) minden egyes Hyper-V gazdagépen, amelyet hozzáadott a **ContosoHyperVSite** helyhez. A telepítő feltelepíti az Azure Site Recovery szolgáltatást és a Recovery Services ügynököt minden Hyper-V gazdagépre.

1. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
2. A **Telepítés** területen fogadja el az alapértelmezett telepítési helyet a szolgáltatóhoz és az ügynökhöz, és kattintson a **Telepítés** lehetőségre.
3. A telepítést követően a Microsoft Azure Site Recovery regisztrációs varázsló **Tár beállításai** területén kattintson a **Tallózás** gombra, majd a **Kulcsfájl** mezőben válassza ki a letöltött tárolókulcsfájlt. 
4. Adja meg az Azure Site Recovery-előfizetést, a tároló nevét (**ContosoVMVault**) és a Hyper-V helyet (**ContosoHyperVSite**), amelyhez a Hyper-V kiszolgáló tartozik.
5. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
6. A **Regisztráció** területen, miután regisztrálta a kiszolgálót a tárolóban, kattintson a **Befejezés** lehetőségre.

Az Azure Site Recovery lekéri a metaadatokat a Hyper-V kiszolgálóról, és a kiszolgáló megjelenik a **Site Recovery-infrastruktúra** > **Hyper-V gazdagépek** listában. Ez a folyamat akár 30 percet is igénybe vehet.


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat. 

1. Kattintson **Az infrastruktúra előkészítése** > **Cél** lehetőségre.
2. Válassza ki az előfizetést és a **ContosoRG** erőforráscsoportot, amelyben az Azure-beli virtuális gépek a feladatátvételt követően létrejönnek.
3. Válassza ki a **Resource Manager**-alapú üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.


## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

> [!NOTE]
> A Hyper-V és az Azure közötti replikációs szabályzat esetében a 15 perces másolási gyakorisági beállítás helyét az 5 perces és a 30 másodperces gyakorisági beállítás vette át. A 15 perces másolási gyakorisággal dolgozó replikációs szabályzatok automatikusan frissülni fognak az 5 perces másolási gyakorisági beállításra. Az 5 perces és 30 másodperces másolási gyakorisági beállítások a 15 perces másolási gyakorisághoz képest jobb replikációs teljesítményt és helyreállításipont-célkitűzéseket biztosítanak, miközben a sávszélesség használatára és az adatátvitel mennyiségére gyakorolt hatásuk elenyésző.

1. Kattintson **Az infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** lehetőségre.
2. A **Szabályzat létrehozása és társítása** területen adja meg a szabályzat nevét (**ContosoReplicationPolicy**).
3. Hagyja meg az alapértelmezett beállításokat, és kattintson az **OK** gombra.
    - A **Másolás gyakorisága** azt jelzi, hogy a különbözeti adatok (a kezdeti replikációt követően) ötpercenként replikálódnak.
    - A **Helyreállítási pont megőrzése** azt jelzi, hogy az egyes helyreállítási pontok megőrzési ideje két óra.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - A **Kezdeti replikáció kezdési ideje** azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
4. A szabályzat létrehozása után kattintson az **OK** gombra. Amikor létrehoz egy új szabályzatot, az automatikusan társítva lesz a megadott Hyper-V hellyel (**ContosoHyperVSite**)

    ![Replikációs szabályzat](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>A replikáció engedélyezése


1. Az **Alkalmazás replikálása** lépésben kattintson a **Forrás** lehetőségre. 
2. A **Forrás** területen, válassza ki a **ContosoHyperVSite** helyet. Ezután kattintson az **OK** gombra.
3. A **Cél** területen győződjön meg arról, hogy az Azure a cél, és ellenőrizze a tároló előfizetését, valamint a **Resource Manager**-alapú üzemi modellt.
4. Válassza ki az előző oktatóanyag során létrehozott **contosovmsacct1910171607** tárfiókot a replikált adatokhoz és a **ContosoASRnet** hálózatot, amelybe a feladatátvételt követően kerülnek az Azure-beli virtuális gépek.
5. A **Virtuális gépek** > **Kiválasztás** menüben jelölje ki a replikálni kívánt virtuális gépeket. Ezután kattintson az **OK** gombra.

 A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeztével a kezdeti replikálás is befejeződik, a virtuális gép pedig készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések
[Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
