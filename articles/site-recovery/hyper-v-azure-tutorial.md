---
title: Hyper-V vészhelyreállítás beállítása az Azure Site Recovery használatával
description: Ismerje meg, hogyan állíthatja be a helyszíni Hyper-V virtuális gépek (VMM nélküli) vészutáni helyreállítását az Azure-ba a Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 741d4718b5e6140f4ddd2bb22e1a2ec830763176
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239846"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépekhez az Azure-ba

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni gépek és az Azure virtuális gépek replikációjának, feladatátvételének és feladat-visszavételének kezelésével és vezénylésével járul hozzá a vész-helyreállítási stratégiához.

Ez az oktatóanyag egy sorozat harmadik része. Bemutatja, hogyan állíthatja be a helyszíni Hyper-V virtuális gépek vészutáni helyreállítását az Azure-ba. Ez az oktatóanyag olyan Hyper-V virtuális gépeket alkalmaz, amelyeket nem a Microsoft System Center Virtual Machine Manager (VMM) kezel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * Állítsa be a forrásreplikációs környezetet, beleértve a helyszíni site recovery összetevőket és a célreplikációs környezetet.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

> [!NOTE]
> Oktatóanyagok megmutatja a legegyszerűbb telepítési útvonalat egy forgatókönyv. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a [Webhely-helyreállítási dokumentáció](https://docs.microsoft.com/azure/site-recovery) **Útmutató** útmutatójában található cikkeket.



## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag egy sorozat harmadik része. Azt feltételezi, hogy már elvégezte a feladatokat az előző oktatóanyagok:

1. [Készítse elő az Azure-t](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. Az Azure Portalon nyissa meg a **Recovery Services-tárolók,** és válassza ki a tárolót. Készítettük a boltozat **ContosoVMVault** az előző oktatóanyag.
2. Az **Első lépések csoportban**válassza a **Site Recovery (Hely helyreállítása)** lehetőséget, majd az **Infrastruktúra előkészítése**lehetőséget.
3. A **Védelem célBan** > Hol találhatók **On-premises****a gépek?**
4. A **Hol szeretné replikálni a gépeket?** lehetőséget válassza **az Azure-ba**lehetőséget.
5. A **Gépek virtualizáltak?** **Yes, with Hyper-V**
6. A **No** **Rendszerközpont VMM szolgáltatását használja a Hyper-V gazdagépek kezeléséhez?**
7. Válassza **az OK gombot.**

    ![Replikációs cél](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. Ha **Deployment planning**nagy telepítést tervez, töltse le a Telepítéstervező a Hyper-V-höz című témakört a lap hivatkozásáról. [További információ](hyper-v-deployment-planner-overview.md) a Hyper-V üzembe helyezési tervezéséről.
2. Ebben az oktatóanyagban nincs szükségünk a Központi telepítéstervezőre. A **Van már befejeződött a telepítés tervezése?**, válassza **a I will do it later**lehetőséget, majd válassza az **OK gombot.**

    ![Az üzembe helyezés megtervezése](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállításához hozzon létre egy Hyper-V-helyet, és adja hozzá a helyhez a replikálni kívánt virtuális gépeket tartalmazó Hyper-V állomásokat. Ezután töltse le és telepítse az Azure Site Recovery Provider és az Azure Recovery Services ügynök minden gazdagépen, és regisztrálja a Hyper-V hely a tárolóban.

1. Az **Infrastruktúra előkészítése**csoportban válassza a **Forrás**lehetőséget.
2. A **Forrás előkészítése csoportban**válassza a **+ Hyper-V hely**lehetőséget.
3. A **Hyper-V hely létrehozása csoportban**adja meg a hely nevét. **A ContosoHyperVSite webhelyet**használjuk.

    ![Hyper-V-hely](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. A hely létrehozása után a **Forrás előkészítése** > **1.**
5. Válassza a **+ Hyper-V kiszolgáló**lehetőséget .

    ![Hyper-V-kiszolgáló](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Töltse le a Microsoft Azure webhely-helyreállítási szolgáltató telepítőjének letöltését.
7. Töltse le a tároló regisztrációs kulcsát. A szolgáltató telepítéséhez szüksége van erre a kulcsra. A kulcs a generálásától számított öt napig érvényes.

    ![Letöltésszolgáltató és regisztrációs kulcs](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Szolgáltató telepítése

Telepítse a letöltött telepítőfájlt (AzureSiteRecoveryProvider.exe) minden olyan Hyper-V állomásra, amelyet hozzá szeretne adni a Hyper-V webhelyhez. A telepítő minden Hyper-V gazdagépen telepíti az Azure Site Recovery Provider and Recovery Services ügynököt.

1. Futtassa a telepítőfájlt.
2. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
3. A **telepítés ben**fogadja el a szolgáltató és az ügynök alapértelmezett telepítési helyét, és válassza a **Telepítés**lehetőséget.
4. A telepítés után a Microsoft Azure Site Recovery Registration wizard > Vault settings ( Webhely-helyreállítási regisztrációs varázsló> A **Triptok beállításai**párbeszédpanelen válassza a **Tallózás**gombot, és a **Kulcsfájl**ban jelölje ki a letöltött tárolókulcsfájlt.
5. Adja meg az Azure Site Recovery-előfizetést, a tároló nevét (**ContosoVMVault**) és a Hyper-V helyet (**ContosoHyperVSite**), amelyhez a Hyper-V kiszolgáló tartozik.
6. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
7. A **Regisztráció ban,** miután a kiszolgáló regisztrálva van a tárolóban, válassza a **Befejezés**lehetőséget.

A Hyper-V kiszolgáló metaadatait az Azure Site Recovery olvassa be, és a kiszolgáló megjelenik a **Site Recovery Infrastructure** > **Hyper-V hosts alkalmazásban.** Ez a folyamat akár 30 percet is igénybe vehet.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>A szolgáltató telepítése Hyper-V magkiszolgálóra

Ha Hyper-V alapkiszolgálót futtat, töltse le a telepítőfájlt, és kövesse az alábbi lépéseket:

1. A parancs futtatásával bontsa ki a fájlokat az AzureSiteRecoveryProvider.exe webhelyről egy helyi könyvtárba:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Futtassa az `.\setupdr.exe /i` parancsot. Az eredmények et a %Programdata%\ASRLogs\DRASetupWizard.log fájl naplózza.

3. Regisztrálja a kiszolgálót a következő parancs futtatásával:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

A célerőforrások kiválasztása és ellenőrzése:

1. Válassza **az Infrastruktúra cél előkészítése** > **lehetőséget.**
2. Válassza ki az előfizetést és a **ContosoRG** erőforráscsoportot, amelyben az Azure-beli virtuális gépek jönnek létre a feladatátvétel után.
3. Válassza ki a **Resource Manager**-alapú üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Válassza **az Infrastruktúra** > **replikációs beállításainak** > előkészítése **+Létrehozás és társítás lehetőséget.**
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. A **ContosoReplicationPolicy házirendet**használjuk.
3. Ebben az oktatóanyagban az alapértelmezett beállításokat hagyjuk meg:
    - **A másolásgyakoriság** azt jelzi, hogy a különbözeti adatok (a kezdeti replikáció után) milyen gyakran replikálódnak. Az alapértelmezett gyakoriság öt percenként.
    - **A helyreállítási pontok megőrzése** azt jelzi, hogy a helyreállítási pontok két órán keresztül megmaradnak.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - **A kezdeti replikáció indítási ideje** azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
4. A házirend létrehozása után válassza az **OK gombot.** Amikor új házirendet hoz létre, az automatikusan a megadott Hyper-V helyhez lesz társítva. A mi bemutató, ez **ContosoHyperVSite**.

    ![Replikációs szabályzat](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Az **Alkalmazás replikálása csoportban**válassza a **Forrás**lehetőséget.
2. A **Forrás** területen, válassza ki a **ContosoHyperVSite** helyet. Ezután válassza **az OK gombot.**
3. A **Target**alkalmazásban ellenőrizze a cél (Azure), a tároló-előfizetés és a **Resource Manager** telepítési modell.
4. Ha oktatóanyag-beállításokat használ, válassza ki a **contosovmsacct1910171607** tárfiókot, amelyet az előző oktatóanyagban hoztak létre a replikált adatokhoz. Is válassza ki a **ContosoASRnet-hálózatot,** amelyben az Azure-beli virtuális gépek a feladatátvétel után fognak elhelyezkedni.
5. A **Virtuális gépek** > **válassza ki**a replikálni kívánt virtuális gépet. Ezután válassza **az OK gombot.**

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **véglegesítési védelem** feladat befejezése után a kezdeti replikáció befejeződik, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
