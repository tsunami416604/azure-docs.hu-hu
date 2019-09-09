---
title: A VMM-felhőkben lévő helyszíni Hyper-V virtuális gépek vész-helyreállításának beállítása az Azure-ba Site Recovery
description: Megtudhatja, hogyan állíthatja be a System Center VMM-felhőkben futó helyszíni Hyper-V virtuális gépek vész-helyreállítását az Azure-ba Site Recovery használatával.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 59e6bbbca982d428d4e590cb647f186e1c3fec3a
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813769"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>A VMM-felhőkben lévő helyszíni Hyper-V virtuális gépek vész-helyreállításának beállítása az Azure-ba

Ez a cikk azt ismerteti, hogyan engedélyezhető a System Center Virtual Machine Manager (VMM) által felügyelt helyszíni Hyper-V virtuális gépek replikálása az Azure-ba való vész-helyreállításhoz a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával. Ha nem használja a VMM-t, [kövesse ezt az oktatóanyagot](hyper-v-azure-tutorial.md) .

Ez a harmadik oktatóanyag egy sorozatban, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba helyszíni VMware virtuális gépekre. Az előző oktatóanyagban [elkészítettük a helyszíni Hyper-V környezetet](hyper-v-prepare-on-premises-tutorial.md) az Azure-ba való vész-helyreállításhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * Állítsa be a forrás replikációs környezetet, beleértve a helyszíni Site Recovery összetevőket és a cél replikációs környezetet.
> * Hálózati leképezés beállítása a VMM virtuálisgép-hálózatok és az Azure-beli virtuális hálózatok közötti leképezéshez.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a [site Recovery dokumentációjának](https://docs.microsoft.com/azure/site-recovery) **útmutatók** szakaszának cikkeit.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag egy sorozat harmadik része. Feltételezi, hogy már végrehajtotta a feladatokat az előző oktatóanyagokban:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A Azure Portal lépjen a **Recovery Services** -tárolók elemre, és válassza ki a tárolót. Elkészítettük a tár **ContosoVMVault** az előző oktatóanyagban.
2. A **első lépések**területen válassza a **site Recovery**lehetőséget, majd válassza az **infrastruktúra előkészítése**lehetőséget.
3. A **védelmi cél** > **Hol találhatók a gépek?** területen válassza **a**helyszíni lehetőséget.
4. A **hová szeretné replikálni a gépeket?** területen válassza **Az Azure**lehetőséget.
5. A-ben **a gépek virtualizáltak?** , válassza az **Igen, a Hyper-V**lehetőséget.
6. A-ben a **System Center VMM használatával felügyelheti a Hyper-V-gazdagépeket?** válassza az **Igen**lehetőséget.
7.  Kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. Ha nagyméretű központi telepítést tervez, a **központi telepítés tervezése**során töltse le a Hyper-V Deployment Planner a lapon található hivatkozásra kattintva. [További](hyper-v-deployment-planner-overview.md) információ a Hyper-V üzembe helyezésének megtervezéséről.
2. Ebben az oktatóanyagban nincs szükségünk a Deployment Plannerra. A-ben **befejezte az üzembe helyezés megtervezését?** területen válassza a **később**lehetőséget, majd kattintson **az OK gombra**.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forrás-környezet beállításakor telepíti a Azure Site Recovery szolgáltatót a VMM-kiszolgálóra, és regisztrálja a kiszolgálót a tárolóban. Minden Hyper-V-gazdagépen telepítenie kell az Azure Recovery Services-ügynököt.

1. Az **infrastruktúra előkészítése**területen válassza a **forrás**lehetőséget.
2. A **forrás előkészítése**lapon válassza a **+ VMM** elemet a VMM-kiszolgáló hozzáadásához. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e.
3. Töltse le a Microsoft Azure Site Recovery Provider telepítőjét.
4. Töltse le a tárolóregisztrációs kulcsot. Ezt a kulcsot a szolgáltató telepítőjének futtatásakor kell megadnia. A kulcs a generálásától számított öt napig érvényes.
5. Töltse le a Microsoft Azure Recovery Services-ügynök telepítőjét.

    ![Szolgáltató, regisztrációs kulcs és ügynök letöltése](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>A szolgáltató telepítése a VMM-kiszolgálón

1. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
2. A **telepítés**területen fogadja el a szolgáltató alapértelmezett telepítési helyét, majd válassza a **telepítés**lehetőséget.
3. A telepítés után a Microsoft Azure Site Recovery regisztrációs varázsló > tár **beállításai**területen válassza a **Tallózás**lehetőséget, majd a **kulcsfájl**területen válassza ki a letöltött tároló-kulcsot.
4. Adja meg a Azure Site Recovery-előfizetést és a tár nevét (**ContosoVMVault**). Adjon meg egy rövid nevet a VMM-kiszolgálónak a tárolóban való azonosításához.
5. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
6. Fogadja el az adattitkosításhoz használt tanúsítvány alapértelmezett helyét. A rendszer a feladatátvétel során visszafejti a titkosított adattitkosítást.
7. A **felhő metaadatainak szinkronizálása**lapon válassza **a Felhőbeli meta-adatok szinkronizálása site Recovery portálra**lehetőséget. Ennek a műveletnek csak egyszer kell történnie az egyes kiszolgálókon. Ezután válassza a **regisztráció**lehetőséget.
8. Miután regisztrálta a kiszolgálót a tárolóban, válassza a **Befejezés**lehetőséget.

A regisztráció befejeződése után Azure Site Recovery lekéri a kiszolgálóról a metaadatokat, a VMM-kiszolgáló pedig **site Recovery infrastruktúrában**jelenik meg.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>A Recovery Services-ügynök telepítése Hyper-V-gazdagépekre

Telepítse az ügynököt minden olyan Hyper-V-gazdagépre, amely a replikálni kívánt virtuális gépeket tartalmazza.

1. A Microsoft Azure Recovery Services ügynök telepítése varázslóban > **Előfeltételek ellenőrzése**lapon válassza a **tovább**lehetőséget. A hiányzó Előfeltételek telepítése automatikusan megtörténik.
2. A **telepítési beállítások**területen fogadja el a telepítési helyet és a gyorsítótár helyét. A gyorsítótár meghajtójának legalább 5 GB tárhellyel kell rendelkeznie. Legalább 600 GB szabad területtel rendelkező meghajtót ajánlunk. Ezt követően válassza az **Install** (Telepítés) parancsot.
3. A **telepítés**során a telepítés befejezésekor válassza a **Bezárás** lehetőséget a varázsló befejezéséhez.

    ![Ügynök telepítése](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

1. Válassza **Az infrastruktúra előkészítése** > **Cél** lehetőséget.
2. Válassza ki azt az előfizetést és erőforráscsoportot (**ContosoRG**), amelyben az Azure-beli virtuális gépeket a feladatátvétel után létre kívánja hozni.
3. Válassza ki a **Resource Manager** -alapú üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

1. **Site Recovery infrastruktúra** > **hálózati** leképezések hálózati leképezése területen válassza a + hálózat leképezése ikont. > 
2. A **hálózati leképezés hozzáadása**lapon válassza ki a forrás VMM-kiszolgálót. Válassza az **Azure** lehetőséget célként.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **forrásoldali hálózaton**válassza ki a helyszíni virtuálisgép-hálózatot.
5. A **célként megadott hálózat**területen válassza ki azt az Azure-hálózatot, amelyben a replika Azure virtuális gépek a feladatátvételt követően jönnek létre. Ezután válassza az **OK** lehetőséget.

    ![Hálózatleképezés](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Válassza az **infrastruktúra** > előkészítése**replikációs beállítások** >  **+ Létrehozás és hozzárendelés**lehetőséget.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. **ContosoReplicationPolicy**használunk.
3. Hagyja meg az alapértelmezett beállításokat, majd kattintson **az OK gombra**.
    - A **másolási gyakoriság** azt jelzi, hogy a kezdeti replikálás után öt percenként replikálja a rendszer a különbözeti adataikat.
    - A **helyreállítási pont megőrzése** azt jelzi, hogy az egyes helyreállítási pontok két óráig maradnak.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - A **kezdeti replikálás kezdő időpontja** azt jelzi, hogy a kezdeti replikáció azonnal elindul.
    - Az **Azure-on tárolt adatok titkosítása** az alapértelmezett (**kikapcsolt**) értékre van állítva, és azt jelzi, hogy az Azure-on kívüli adatok nem titkosítottak.
4. A szabályzat létrehozása után kattintson **az OK gombra**. Új szabályzat létrehozásakor a rendszer automatikusan hozzárendeli a VMM-felhőhöz.

## <a name="enable-replication"></a>Replikáció engedélyezése

1. Az **alkalmazás replikálása**területen válassza a **forrás**lehetőséget.
2. A **forrás**területen válassza ki a VMM-felhőt. Ezután válassza az **OK** lehetőséget.
3. A **cél**lapon ellenőrizze a célt (Azure), a tár előfizetését, és válassza ki a **Resource Manager** -modellt.
4. Válassza ki a **contosovmsacct1910171607** Storage-fiókot és a **ContosoASRnet** Azure-hálózatot.
5. A **virtuális gépek** > **területen**válassza ki a replikálni kívánt virtuális gépet. Ezután válassza az **OK** lehetőséget.

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **védelem véglegesítése** művelet befejezése után a kezdeti replikálás befejeződött, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
