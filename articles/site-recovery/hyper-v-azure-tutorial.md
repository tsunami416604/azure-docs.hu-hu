---
title: Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépekhez (VMM nélkül) az Azure-ba az Azure Site Recovery használatával  | Microsoft Docs
description: Ismerje meg, hogyan állíthat be Azure-ba irányuló vészhelyreállítást helyszíni Hyper-V virtuális gépekhez az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da643a4d7a1dc74385b3854c1952af5ba93bd241
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358098"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépekhez az Azure-ba

c

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * A forrás replikációs környezet (többek között a helyszíni Site Recovery-összetevők), valamint a cél replikálási környezet beállítása.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

> [!NOTE]
> Az oktatóanyagok bemutatják a legegyszerűbb telepítési út esetén. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a cikk az útmutató szakaszban a Site Recovery a tartalom.

## <a name="before-you-begin"></a>Előkészületek
Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)



## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **Recovery Services-tárolók** listából válassza ki a tárolót. A tároló előkészített **ContosoVMVault** az előző oktatóanyagban.
2. Az **Első lépések** területen kattintson a **Site Recovery** elemre. Ezt követően kattintson **Az infrastruktúra előkészítése** elemre.
3. A **védelmi cél** > **hol találhatók a gépek??** kiválasztása **helyszíni**.
4. A **hol szeretné replikálni a gépeket?** válassza **az Azure-bA**.
5. A **virtualizáltak a gépek?** kiválasztása **Igen, a Hyper-V**.
6. Azon a területen, amely rákérdez, hogy **System Center VMM segítségével kezeli-e a Hyper-V-gazdagépeket**, válassza a **Nem** lehetőséget. Ezután kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. A **üzembe helyezésének tervezése**, ha egy nagyobb méretű központi telepítések, töltse le a Deployment Planner a Hyper-V a hivatkozásból az oldalon. [További](hyper-v-deployment-planner-overview.md) Hyper-V központi telepítési tervezésével kapcsolatban.
2. Ez az oktatóanyag az alkalmazásában a deployment planner nincs szükségünk. A **végzett az üzembe helyezés megtervezése?** kiválasztása **végzem el később**. Ezután kattintson az **OK** gombra.

    ![Az üzembe helyezés megtervezése](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállításához hozzon létre egy Hyper-V helyhez, és adja hozzá a helyre replikálni kívánt virtuális gépeket tartalmazó Hyper-V-gazdagépek. Ezután töltse le és telepítse az Azure Site Recovery Providert és az Azure Recovery Services-ügynököt minden gazdagépen, majd regisztrálja a Hyper-V-helyet a tárolóban. 

1. A **infrastruktúra előkészítése**, kattintson a **forrás**.
2. A **forrás előkészítése**, kattintson a **+ Hyper-V hely**.
3. A **hozzon létre Hyper-V hely**, és adja meg hely nevét. Használunk **ContosoHyperVSite**.

    ![Hyper-V-hely](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. A webhely létrehozása után a **forrás előkészítése** > **1. lépés: Válassza ki a Hyper-V hely**, válassza ki a létrehozott helyet.
4. Kattintson a **+Hyper-V Server** elemre.

    ![Hyper-V-kiszolgáló](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Töltse le a Microsoft Azure Site Recovery Provider telepítőjét.
6. Töltse le a tároló regisztrációs kulcsát. Szüksége lesz a szolgáltató telepítéséhez ezt a kulcsot. A kulcs a generálásától számított öt napig érvényes.

    ![Szolgáltató letöltése](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Szolgáltató telepítése

Telepítse a letöltött telepítőfájlját (AzureSiteRecoveryProvider.exe) minden egyes Hyper-V-gazdagép hozzáadása a Hyper-V helyhez szeretne. A telepítő feltelepíti az Azure Site Recovery szolgáltatást és a Recovery Services ügynököt minden Hyper-V gazdagépre.

1. Futtassa a telepítőfájlt.
2. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
3. A **Telepítés** területen fogadja el az alapértelmezett telepítési helyet a szolgáltatóhoz és az ügynökhöz, és kattintson a **Telepítés** lehetőségre.
4. A telepítést követően a Microsoft Azure Site Recovery regisztrációs varázsló **Tár beállításai** területén kattintson a **Tallózás** gombra, majd a **Kulcsfájl** mezőben válassza ki a letöltött tárolókulcsfájlt. 
5. Adja meg az Azure Site Recovery-előfizetést, a tároló nevét (**ContosoVMVault**) és a Hyper-V helyet (**ContosoHyperVSite**), amelyhez a Hyper-V kiszolgáló tartozik.
6. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
7. A **Regisztráció** területen, miután regisztrálta a kiszolgálót a tárolóban, kattintson a **Befejezés** lehetőségre.

Az Azure Site Recovery lekéri a metaadatokat a Hyper-V kiszolgálóról, és a kiszolgáló megjelenik a **Site Recovery-infrastruktúra** > **Hyper-V gazdagépek** listában. Ez a folyamat akár 30 percet is igénybe vehet.        

#### <a name="install-on-hyper-v-core-server"></a>Telepítse a Hyper-V core kiszolgálóra

Ha egy Hyper-V core kiszolgálón futtatja, a telepítési fájl letöltéséhez, és tegye a következőket:

1. Csomagolja ki a fájlokat AzureSiteRecoveryProvider.exe egy helyi könyvtárba futtatásával

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2.  Futtatás ".\setupdr.exe /i. Eredmények % Programdata%\ASRLogs\DRASetupWizard.log naplózása.

3.  Regisztrálja a kiszolgálót az alábbi paranccsal:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```
 

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat. 

1. Kattintson **Az infrastruktúra előkészítése** > **Cél** lehetőségre.
2. Válassza ki az előfizetést és a **ContosoRG** erőforráscsoportot, amelyben az Azure-beli virtuális gépek a feladatátvételt követően létrejönnek.
3. Válassza ki a **Resource Manager**-alapú üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.


## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Kattintson **Az infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** lehetőségre.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. Használunk **ContosoReplicationPolicy**.
3. Ebben az oktatóanyagban hagyjuk az alapértelmezett beállításokat.
    - **Másolás gyakorisága** azt jelzi, hogy milyen gyakran replikálja a különbözeti adatok (kezdeti replikációt követően), minden öt perc alapértelmezés szerint.
    - **Helyreállítási pont megőrzése** azt jelzi, hogy a helyreállítási pontok két órával lesznek megőrizve.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - A **Kezdeti replikáció kezdési ideje** azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
4. A szabályzat létrehozása után kattintson az **OK** gombra. Amikor létrehoz egy új szabályzatot, akkor automatikusan társítva a megadott Hyper-V-hely (a jelen oktatóanyagban hogy **ContosoHyperVSite**)

    ![Replikációs szabályzat](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>A replikáció engedélyezése


1. Az **Alkalmazás replikálása** lépésben kattintson a **Forrás** lehetőségre. 
2. A **Forrás** területen, válassza ki a **ContosoHyperVSite** helyet. Ezután kattintson az **OK** gombra.
3. A **Cél** területen győződjön meg arról, hogy az Azure a cél, és ellenőrizze a tároló előfizetését, valamint a **Resource Manager**-alapú üzemi modellt.
4. Ha oktatóanyag beállításokat használja, jelölje be a **contosovmsacct1910171607** replikált adatok tárolására, az előző oktatóanyagban létrehozott tárfiókot és a **ContosoASRnet** hálózathoz, mely Azure-beli virtuális gépek lesz a található a feladatátvételt követően.
5. A **Virtuális gépek** > **Kiválasztás** menüben jelölje ki a replikálni kívánt virtuális gépeket. Ezután kattintson az **OK** gombra.

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeztével a kezdeti replikálás is befejeződik, a virtuális gép pedig készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
