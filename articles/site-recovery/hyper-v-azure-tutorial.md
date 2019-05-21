---
title: Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépek (VMM nélkül) az Azure Site recoveryvel |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be vész-helyreállítási a helyszíni Hyper-V virtuális gépek (VMM nélkül) az Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7a27aa0a6e91347db3aab560157e2cba6c43d584
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966584"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépekhez az Azure-ba

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul a vész-helyreállítási stratégiát kezelésével és irányításával replikálása, feladatátvétele és feladat-visszavételhez helyszíni gépek és Azure-beli virtuális gépek (VM).

Ez az oktatóanyag egy sorozat harmadik része. Ez bemutatja, hogyan állíthatja be az Azure-bA a helyszíni Hyper-V virtuális gépek vész-helyreállítási. Ebben az oktatóanyagban, amely nem a Microsoft System Center Virtual Machine Manager (VMM) felügyelt Hyper-V virtuális gépek vonatkozik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * A forrás replikációs környezet, beleértve a helyszíni Site Recovery-összetevők és a replikálási célkörnyezet beállítása.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

> [!NOTE]
> Az oktatóanyagok bemutatják a legegyszerűbb telepítési út esetén. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a cikkek a **útmutatók** szakaszában a [Site Recovery dokumentációja](https://docs.microsoft.com/en-us/azure/site-recovery).

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag egy sorozat harmadik része. Feltételezi, hogy már végrehajtotta a feladatokat az előző oktatóanyagok:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. Az Azure Portalon lépjen a **Recovery Services-tárolók** , és válassza ki a tárolót. A tároló előkészített **ContosoVMVault** az előző oktatóanyagban.
2. A **bevezetés**válassza **Site Recovery**, majd válassza ki **infrastruktúra előkészítése**.
3. A **védelmi cél** > **hol találhatók a gépek??** válassza **helyszíni**.
4. A **hol szeretné replikálni a gépeket?** válassza **az Azure-bA**.
5. A **virtualizáltak a gépek?** válassza **Igen, a Hyper-V**.
6. A **használ a System Center VMM-ben a Hyper-V gazdagépek felügyeletéhez?** válassza **nem**.
7. Kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. A **üzembe helyezésének tervezése**, ha egy nagyobb méretű központi telepítések, töltse le a Deployment Planner a Hyper-V a hivatkozásból az oldalon. [További](hyper-v-deployment-planner-overview.md) Hyper-V központi telepítési tervezésével kapcsolatban.
2. Ebben az oktatóanyagban a Deployment Planner nincs szükségünk. A **végzett az üzembe helyezés megtervezése?** válassza **végzem el később**, majd válassza ki **OK**.

    ![Üzembe helyezés tervezése](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállításához hozzon létre egy Hyper-V helyhez, és a replikálni kívánt virtuális gépeket tartalmazó Hyper-V-gazdagépek hozzáadása a hely. Ezután, töltse le és telepítse az Azure Site Recovery Provider és az Azure Recovery Services ügynököt minden gazdagépre, és regisztrálja a Hyper-V helyet a tárolóban.

1. A **infrastruktúra előkészítése**válassza **forrás**.
2. A **forrás előkészítése**válassza **+ Hyper-V hely**.
3. A **hozzon létre Hyper-V hely**, adja meg a webhely nevét. Használunk **ContosoHyperVSite**.

    ![Hyper-V-hely](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. A webhely létrehozása után a **forrás előkészítése** > **1. lépés: Válassza ki a Hyper-V hely**, válassza ki a létrehozott helyet.
5. Válassza ki **+ Hyper-V Server**.

    ![Hyper-V-kiszolgáló](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Töltse le a Microsoft Azure Site Recovery Provider telepítőjét.
7. Töltse le a tárolóregisztrációs kulcsot. Szüksége lesz a szolgáltató telepítéséhez ezt a kulcsot. A kulcs a generálásától számított öt napig érvényes.

    ![Szolgáltató és a regisztrációs kulcs letöltése](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Szolgáltató telepítése

Telepítse a letöltött telepítőfájlját (AzureSiteRecoveryProvider.exe) minden egyes Hyper-V-gazdagép hozzáadása a Hyper-V webhelyhez kívánt. A telepítő telepíti az Azure Site Recovery Provider és Recovery Services Agent ügynököt minden Hyper-V gazdagépen.

1. Futtassa a telepítőfájlt.
2. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
3. A **telepítési**, fogadja el az alapértelmezett telepítési hely a Provider és az ügynök számára, és válassza ki **telepítése**.
4. Telepítést követően a Microsoft Azure Site Recovery regisztrációs varázsló > **tároló beállításai**, jelölje be **Tallózás**, és a **titkosításikulcs-fájl**, jelölje be a kulcsot, amely a fájl letöltött.
5. Adja meg az Azure Site Recovery-előfizetést, a tároló nevét (**ContosoVMVault**) és a Hyper-V helyet (**ContosoHyperVSite**), amelyhez a Hyper-V kiszolgáló tartozik.
6. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
7. A **regisztrációs**, a tárolóban, válassza ki a kiszolgáló regisztrálása után **Befejezés**.

Az Azure Site Recovery lekéri a metaadatokat a Hyper-V kiszolgálóról, és a kiszolgáló megjelenik a **Site Recovery-infrastruktúra** > **Hyper-V gazdagépek** listában. Ez a folyamat akár 30 percet is igénybe vehet.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Telepítse a szolgáltatót a Hyper-V core-kiszolgáló

Ha egy Hyper-V core kiszolgálón futtatja, a telepítési fájl letöltéséhez, és kövesse az alábbi lépéseket:

1. Csomagolja ki a fájlokat AzureSiteRecoveryProvider.exe egy helyi könyvtárban a következő parancs futtatásával:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Futtassa az `.\setupdr.exe /i` parancsot. Eredmények % Programdata%\ASRLogs\DRASetupWizard.log naplózása.

3. Regisztrálja a kiszolgálót a következő parancs futtatásával:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki, és ellenőrizze a célerőforrásokat:

1. Válassza **Az infrastruktúra előkészítése** > **Cél** lehetőséget.
2. Válassza ki az előfizetést és az erőforráscsoport **ContosoRG** található, amely az Azure virtuális gépek jön létre a feladatátvételt követően.
3. Válassza ki a **Resource Manager**-alapú üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Válassza ki **infrastruktúra előkészítése** > **replikációs beállítások** > **+ létrehozás és társítás**.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. Használunk **ContosoReplicationPolicy**.
3. Ebben az oktatóanyagban hagyjuk az alapértelmezett beállításokat:
    - **Másolás gyakorisága** azt jelzi, hogy milyen gyakran replikálja a különbözeti adatok (kezdeti replikációt követően). Az alapértelmezett gyakoriság 5 percenként van.
    - **Helyreállítási pont megőrzése** azt jelzi, hogy a helyreállítási pontok két órával lesznek megőrizve.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - **Kezdeti replikáció kezdési ideje** azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
4. Miután létrehozta a szabályzatot, válassza ki a **OK**. Amikor létrehoz egy új szabályzatot, akkor automatikusan a megadott Hyper-V hely társítva. A jelen oktatóanyagban hogy **ContosoHyperVSite**.

    ![Replikációs házirend](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Replikáció engedélyezése

1. A **alkalmazás replikálása**válassza **forrás**.
2. A **Forrás** területen, válassza ki a **ContosoHyperVSite** helyet. Ezután válassza az **OK** lehetőséget.
3. A **cél**, ellenőrizze a cél (Azure), a tároló előfizetését, és a **Resource Manager** üzemi modellt.
4. Ha oktatóanyag beállításokat használja, jelölje be a **contosovmsacct1910171607** a replikált adatokat az előző oktatóanyag során létrehozott tárfiókba. Kiválaszthatja a **ContosoASRnet** hálózati, mely Azure-beli virtuális gépeken található a feladatátvételt követően.
5. A **virtuális gépek** > **kiválasztása**, válassza ki a replikálni kívánt virtuális Gépet. Ezután válassza az **OK** lehetőséget.

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. Miután a **védelem véglegesítése** feladat befejeződik, a kezdeti replikálás is befejeződik, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
