---
title: Állítsa be a helyszíni Hyper-V virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recovery VMM-felhőkben |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a helyszíni Hyper-V virtuális gépek vészhelyreállítása az Azure Site Recovery szolgáltatással az Azure-bA System Center VMM-felhőkben.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 64559f653ba8a466de7bec10db34383b508e3e4b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361291"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>A helyszíni Hyper-V virtuális gépek vészhelyreállítása beállítása a VMM-felhőkben az Azure-bA

Ez a cikk ismerteti a vész-helyreállítási Azure-ban a System Center Virtual Machine Manager (VMM) által felügyelt helyszíni Hyper-V virtuális gépek replikálásának engedélyezéséhez a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás. Ha nem használja a VMM-ben, majd [ezt az oktatóanyagot követve](hyper-v-azure-tutorial.md).

Ez az a harmadik oktatóanyag egy sorozat, amely bemutatja, hogyan állítható be vészhelyreállítást az Azure-bA a helyszíni VMware virtuális gépekhez. Az előző oktatóanyagban azt [a helyszíni Hyper-V környezetben készített](hyper-v-prepare-on-premises-tutorial.md) vész-helyreállítási az Azure-bA. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:


> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * A forrás replikációs környezet (többek között a helyszíni Site Recovery-összetevők), valamint a cél replikálási környezet beállítása.
> * Állítsa be a hálózatleképezést, a VMM-Virtuálisgép-hálózatok és az Azure virtuális hálózatok közötti megfeleltethetőségének.
> * Replikációs házirend létrehozása
> * Virtuális gép replikálásának engedélyezése


> [!NOTE]
> Az oktatóanyagok bemutatják a legegyszerűbb telepítési út esetén. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a cikk az útmutató szakaszban a Site Recovery a tartalom.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [A helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md) Ez az oktatóanyag egy sorozat harmadik. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:


## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **Recovery Services-tárolók** listából válassza ki a tárolót. A tároló előkészített **ContosoVMVault** az előző oktatóanyagban.
2. Az **Első lépések** területen kattintson a **Site Recovery** elemre. Ezt követően kattintson **Az infrastruktúra előkészítése** elemre.
3. A **védelmi cél** > **hol találhatók a gépek??** válassza **helyszíni**.
4. A **hol szeretné replikálni a gépeket?** válassza **az Azure-bA**.
5. A **virtualizáltak a gépek?** kiválasztása **Igen, a Hyper-V**.
6. A **használ a System Center VMM**válassza **Igen**. Ezután kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)


## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. A **üzembe helyezésének tervezése**, ha egy nagyobb méretű központi telepítések, töltse le a Deployment Planner a Hyper-V a hivatkozásból az oldalon. [További](hyper-v-deployment-planner-overview.md) Hyper-V központi telepítési tervezésével kapcsolatban.
2. Ez az oktatóanyag az alkalmazásában a deployment planner nincs szükségünk. A **végzett az üzembe helyezés megtervezése?** válassza **végzem el később**. Ezután kattintson az **OK** gombra.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállítása, ha az Azure Site Recovery Provider telepítése a VMM-kiszolgálón, és regisztrálja a kiszolgálót a tárolóban. Az Azure Recovery Services ügynököt minden Hyper-V gazdagépen telepítenie. 

1. **Az infrastruktúra előkészítése** területen kattintson a **Forrás** lehetőségre.
2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e.
3. Töltse le a Microsoft Azure Site Recovery Provider telepítőjét.
4. Töltse le a tároló regisztrációs kulcsát. Erre szüksége lesz a szolgáltató telepítésének futtatásakor. A kulcs a generálásától számított öt napig érvényes.
5. Töltse le a telepítőt, a Microsoft Azure Recovery Services ügynök.

    ![Letöltés](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>A szolgáltató telepítése a VMM-kiszolgálón

1. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
2. A **telepítési**, fogadja el az alapértelmezett telepítési hely a szolgáltató, és kattintson a **telepítése**. 
3. Telepítést követően a Microsoft Azure Site Recovery regisztrációs varázsló > **tároló beállításai**, kattintson a **Tallózás**, és a **titkosításikulcs-fájl**, jelölje be a kulcsot fájlt letöltve.
4. Adja meg az Azure Site Recovery-előfizetést, és a tároló nevét (**ContosoVMVault**). Adjon meg egy rövid nevet a VMM-kiszolgálón, a tároló azonosításához.
5. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
6. Fogadja el az alapértelmezett helyet, az adatok titkosításához használt tanúsítvány. Amikor feladatátvételt hajt végre rendszer visszafejti a titkosított adatok.
7. A **felhőmetaadatok szinkronizálása**válassza **szinkronizálja a felhőmetaadatokat a Site Recovery portálon**. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Kattintson a **regisztrálása**.
8. Miután a kiszolgáló regisztrálva van a tárolóban, kattintson a **Befejezés**.

Regisztráció befejezését követően az Azure Site Recovery lekéri a metaadatokat a kiszolgálóról, és a VMM-kiszolgáló megjelenik a **Site Recovery-infrastruktúra**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>A Recovery Services agent telepítése a Hyper-V-gazdagépek

Telepítse az ügynököt minden replikálni kívánt virtuális gépeket tartalmazó Hyper-V gazdagépen.

1. A Microsoft Azure Recovery Services ügynök telepítővarázslójának lépéseit a > **szükséges előfeltételek ellenőrzése**, kattintson a **tovább**. Automatikusan települ a hiányzó előfeltételeket.
2. A **telepítési beállítások**, fogadja el a telepítés helyét, és a gyorsítótár helyét. A gyorsítótár-meghajtón legalább 5 GB adattárolás van szüksége. Azt javasoljuk, hogy olyan meghajtót, amelyen legalább 600 GB szabad terület. Ezt követően kattintson a **Telepítés** gombra.
3. A **telepítési**, a telepítés befejezését követően, kattintson a **Bezárás** a varázsló befejezéséhez.

    ![Ügynök telepítése](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

1. Kattintson **Az infrastruktúra előkészítése** > **Cél** lehetőségre.
2. Válassza ki az előfizetést és az erőforráscsoport (**ContosoRG**) található, amely az Azure virtuális gépek jön létre a feladatátvételt követően.
3. Válassza ki a **Resource Manager** üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.


## <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

1. A **Site Recovery-infrastruktúra** > **Hálózatleképezések** > **Hálózatleképezés** menüpontban kattintson a **+Hálózatleképezés** ikonra.
2. A **hálózatleképezés hozzáadása**, válassza ki a forrás VMM-kiszolgálón. Válassza ki **Azure** célként.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **Forráshálózat**, válassza ki a forrás helyi Virtuálisgép-hálózathoz.
5. A **célhálózat**, válassza ki az Azure-hálózatot, amelyhez replika Azure virtuális gépek kerülnek, a feladatátvételt követően felálló. Ezután kattintson az **OK** gombra.

    ![Hálózatleképezés](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Kattintson **Az infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** lehetőségre.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. Használunk **ContosoReplicationPolicy**.
3. Hagyja meg az alapértelmezett beállításokat, és kattintson az **OK** gombra.
    - A **Másolás gyakorisága** azt jelzi, hogy a különbözeti adatok (a kezdeti replikációt követően) ötpercenként replikálódnak.
    - **Helyreállítási pont megőrzése** azt jelzi, hogy az egyes helyreállítási pontok két órával lesznek megőrizve.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - A **Kezdeti replikáció kezdési ideje** azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
    - **Az Azure-ban tárolt adatok titkosítása** – az alapértelmezett **ki** beállítás azt jelzi, hogy inaktív adatok az Azure-ban nincs titkosítva.
4. A szabályzat létrehozása után kattintson az **OK** gombra. Az újonnan létrehozott szabályzatokat a rendszer automatikusan társítja a VMM-felhővel.

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Az **Alkalmazás replikálása** lépésben kattintson a **Forrás** lehetőségre. 
2. A **forrás**, válassza ki a VMM-felhőben. Ezután kattintson az **OK** gombra.
3. A **cél**, ellenőrizze az Azure a cél, a tároló előfizetését, és válassza ki a **Resource Manager** modell.
4. Válassza ki a **contosovmsacct1910171607** tárfiók, és a **ContosoASRnet** Azure-hálózatot.
5. A **Virtuális gépek** > **Kiválasztás** menüben jelölje ki a replikálni kívánt virtuális gépeket. Ezután kattintson az **OK** gombra.

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeztével a kezdeti replikálás is befejeződik, a virtuális gép pedig készen áll a feladatátvételre.



## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
