---
title: Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépek a VMM-felhőkben az Azure Site recoveryvel |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a helyszíni Hyper-V virtuális gépek vészhelyreállítása a System Center VMM-felhőben futó Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 653db1497fcce5981bba7416f073b0330ca2861f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66398149"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>A helyszíni Hyper-V virtuális gépek vészhelyreállítása beállítása a VMM-felhőkben az Azure-bA

Ez a cikk bemutatja, hogyan vész-helyreállítási Azure-ban a System Center Virtual Machine Manager (VMM) által felügyelt helyszíni Hyper-V virtuális gépek replikálásának engedélyezéséhez a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás. Ha nem használja a VMM-ben, [ezt az oktatóanyagot követve](hyper-v-azure-tutorial.md) helyette.

Ez az a harmadik oktatóanyag egy sorozat, amely bemutatja, hogyan állítható be vészhelyreállítást az Azure-bA a helyszíni VMware virtuális gépekhez. Az előző oktatóanyagban azt [a helyszíni Hyper-V környezetben készített](hyper-v-prepare-on-premises-tutorial.md) vész-helyreállítási az Azure-bA.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * A forrás replikációs környezet, beleértve a helyszíni Site Recovery-összetevők és a replikálási célkörnyezet beállítása.
> * Hálózatleképezés beállítása a VMM Virtuálisgép-hálózatok és az Azure virtuális hálózatok közötti megfeleltethetőségének.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

> [!NOTE]
> Az oktatóanyagok bemutatják a legegyszerűbb telepítési út esetén. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a cikkek a **útmutatók** szakaszában a [Site Recovery dokumentációja](https://docs.microsoft.com/azure/site-recovery).

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
6. A **használ a System Center VMM-ben a Hyper-V gazdagépek felügyeletéhez?** válassza **Igen**.
7.  Kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. A **üzembe helyezésének tervezése**, ha egy nagyobb méretű központi telepítések, töltse le a Deployment Planner a Hyper-V a hivatkozásból az oldalon. [További](hyper-v-deployment-planner-overview.md) Hyper-V központi telepítési tervezésével kapcsolatban.
2. Ebben az oktatóanyagban a Deployment Planner nincs szükségünk. A **végzett az üzembe helyezés megtervezése?** válassza **végzem el később**, majd válassza ki **OK**.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállítása, ha az Azure Site Recovery Provider telepítése a VMM-kiszolgálón, és regisztrálja a kiszolgálót a tárolóban. Az Azure Recovery Services ügynököt minden Hyper-V gazdagépen telepítenie.

1. A **infrastruktúra előkészítése**válassza **forrás**.
2. A **forrás előkészítése**válassza **+ VMM** hozzáadása a VMM-kiszolgáló. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e.
3. Töltse le a Microsoft Azure Site Recovery Provider telepítőjét.
4. Töltse le a tárolóregisztrációs kulcsot. A szolgáltató telepítésének futtatásakor kell ezt a kulcsot. A kulcs a generálásától számított öt napig érvényes.
5. Töltse le a telepítőt, a Microsoft Azure Recovery Services ügynök.

    ![Töltse le a szolgáltató regisztrációs kulcs és az ügynök](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>A szolgáltató telepítése a VMM-kiszolgálón

1. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
2. A **telepítési**, fogadja el az alapértelmezett telepítési hely a szolgáltató, és válassza ki **telepítése**.
3. Telepítést követően a Microsoft Azure Site Recovery regisztrációs varázsló > **tároló beállításai**, jelölje be **Tallózás**, és a **titkosításikulcs-fájl**, jelölje be a kulcsot, amely a fájl letöltött.
4. Adja meg az Azure Site Recovery-előfizetést, és a tároló nevét (**ContosoVMVault**). Adjon meg egy rövid nevet a VMM-kiszolgálón, a tároló azonosításához.
5. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
6. Fogadja el az alapértelmezett helyet, az adatok titkosításához használt tanúsítvány. Amikor feladatátvételt hajt végre rendszer visszafejti a titkosított adatok.
7. A **felhőmetaadatok szinkronizálása**válassza **szinkronizálja a felhőmetaadatokat a Site Recovery portálon**. Ez a művelet egyszer szükséges elvégezni csak minden kiszolgálón. Ezután válassza ki **regisztrálása**.
8. Miután a kiszolgáló regisztrálva van a tárolóban, válassza ki **Befejezés**.

Regisztráció befejezését követően az Azure Site Recovery lekéri a metaadatokat a kiszolgálóról, és a VMM-kiszolgáló megjelenik a **Site Recovery-infrastruktúra**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>A Recovery Services agent telepítése a Hyper-V-gazdagépek

Telepítse az ügynököt minden replikálni kívánt virtuális gépeket tartalmazó Hyper-V gazdagépen.

1. A Microsoft Azure Recovery Services ügynök telepítővarázslójának lépéseit a > **szükséges előfeltételek ellenőrzése**válassza **tovább**. Hiányzó előfeltételeket telepítése automatikusan megtörténik.
2. A **telepítési beállítások**, fogadja el a telepítési hely és a gyorsítótár helyét. A gyorsítótár-meghajtón legalább 5 GB adattárolás van szüksége. Azt javasoljuk, hogy olyan meghajtót, amelyen legalább 600 GB szabad terület. Ezt követően válassza az **Install** (Telepítés) parancsot.
3. A **telepítési**, a telepítés befejezését követően, válassza ki **Bezárás** a varázsló befejezéséhez.

    ![Ügynök telepítése](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

1. Válassza **Az infrastruktúra előkészítése** > **Cél** lehetőséget.
2. Válassza ki az előfizetést és az erőforráscsoport (**ContosoRG**) található, amely az Azure virtuális gépek jön létre a feladatátvételt követően.
3. Válassza ki a **Resource Manager** üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

1. A **Site Recovery-infrastruktúra** > **Hálózatleképezések** > **Hálózatleképezés**, jelölje be a **+ Hálózatleképezés** ikonra.
2. A **hálózatleképezés hozzáadása**, válassza ki a forrás VMM-kiszolgálón. Válassza ki **Azure** célként.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **Forráshálózat**, válassza ki a forrás helyi Virtuálisgép-hálózathoz.
5. A **célhálózat**, válassza ki az Azure-hálózatot, amelyhez replika Azure virtuális gépek kerülnek, a feladatátvételt követően felálló. Ezután válassza az **OK** lehetőséget.

    ![Hálózatleképezés](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Válassza ki **infrastruktúra előkészítése** > **replikációs beállítások** >  **+ létrehozás és társítás**.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. Használunk **ContosoReplicationPolicy**.
3. Hagyja bejelölve az alapértelmezett beállításokat, és válassza ki **OK**.
    - **Másolás gyakorisága** azt jelzi, hogy a kezdeti replikációt követően a különbözeti adatokat fog ötpercenként replikálódnak.
    - **Helyreállítási pont megőrzése** azt jelzi, hogy minden helyreállítási pont két órával lesznek megőrizve.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - **Kezdeti replikáció kezdési ideje** azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
    - **Az Azure-ban tárolt adatok titkosítása** az alapértelmezett értékre van állítva (**ki**), és azt jelzi, hogy inaktív adatok az Azure-ban nem titkosított.
4. Miután létrehozta a szabályzatot, válassza ki a **OK**. Amikor létrehoz egy új szabályzatot, akkor automatikusan a VMM-felhő társítva.

## <a name="enable-replication"></a>A replikáció engedélyezése

1. A **alkalmazás replikálása**válassza **forrás**.
2. A **forrás**, válassza ki a VMM-felhőben. Ezután válassza az **OK** lehetőséget.
3. A **cél**, ellenőrizze a cél (Azure), a tároló előfizetését, és válassza ki a **Resource Manager** modell.
4. Válassza ki a **contosovmsacct1910171607** storage-fiók és a **ContosoASRnet** Azure-hálózatot.
5. A **virtuális gépek** > **kiválasztása**, válassza ki a replikálni kívánt virtuális Gépet. Ezután válassza az **OK** lehetőséget.

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. Miután a **védelem véglegesítése** feladat befejeződik, a kezdeti replikálás is befejeződik, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
