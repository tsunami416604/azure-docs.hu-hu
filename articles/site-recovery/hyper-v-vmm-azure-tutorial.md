---
title: Állítsa be a helyszíni Hyper-V virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recovery VMM-felhőkben |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a helyszíni Hyper-V virtuális gépek vészhelyreállítása az Azure Site Recovery szolgáltatással az Azure-bA System Center VMM-felhőkben.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f57dd806e345cad80b8c60424e12211fc74814d6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213219"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>A helyszíni Hyper-V virtuális gépek vészhelyreállítása beállítása a VMM-felhőkben az Azure-bA

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Az oktatóanyag bemutatja, hogyan állíthatja be a helyszíni Hyper-V virtuális gépek Azure-ba irányuló vészhelyreállítását. Az oktatóanyag a System Center Virtual Machine Manager (VMM) által felügyelt Hyper-V virtuális gépek fontos. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * A forrás replikációs környezet (többek között a helyszíni Site Recovery-összetevők), valamint a cél replikálási környezet beállítása.
> * Állítsa be a hálózatleképezést, a VMM-Virtuálisgép-hálózatok és az Azure virtuális hálózatok közötti megfeleltethetőségének.
> * Replikációs házirend létrehozása
> * Virtuális gép replikálásának engedélyezése

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)

Mielőtt elkezdené, érdemes [áttekinteni az architektúrát](concepts-hyper-v-to-azure-architecture.md), hogy szerepel-e benne ez a vészhelyreállítási forgatókönyv.



## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **minden szolgáltatás** > **Recovery Services-tárolók**, kattintson a tároló nevére az ezekben az oktatóanyagokban használunk **ContosoVMVault**.
2. Az **Első lépések** területen kattintson a **Site Recovery** elemre. Ezt követően kattintson **Az infrastruktúra előkészítése** elemre.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **virtualizáltak a gépek**válassza **Igen, a Hyper-V**.
6. A **használ a System Center VMM**válassza **Igen**. Ezután kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállítása, ha az Azure Site Recovery Provider és az Azure Recovery Services agent telepítése, és a helyszíni kiszolgálók regisztrálja a tárolóban. 

1. **Az infrastruktúra előkészítése** területen kattintson a **Forrás** lehetőségre.
2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez. A **-kiszolgáló hozzáadása**, ellenőrizze, hogy **System Center VMM-kiszolgáló** megjelenik **kiszolgálótípus**.
3. Töltse le a Microsoft Azure Site Recovery Provider telepítőjét.
4. Töltse le a tároló regisztrációs kulcsát. Erre szüksége lesz a szolgáltató telepítésének futtatásakor. A kulcs a generálásától számított öt napig érvényes.
5. Töltse le a Recovery Services Agent ügynököt.

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

### <a name="install-the-recovery-services-agent"></a>A Recovery Services agent telepítése

Telepítse az ügynököt minden replikálni kívánt virtuális gépeket tartalmazó Hyper-V gazdagépen.

1. A Microsoft Azure Recovery Services ügynök telepítővarázslójának lépéseit a > **szükséges előfeltételek ellenőrzése**, kattintson a **tovább**. Automatikusan települ a hiányzó előfeltételeket.
2. A **telepítési beállítások**, fogadja el a telepítés helyét, és a gyorsítótár helyét. A gyorsítótár-meghajtón legalább 5 GB adattárolás van szüksége. Azt javasoljuk, hogy olyan meghajtót, amelyen legalább 600 GB szabad terület. Ezt követően kattintson a **Telepítés** gombra.
3. A **telepítési**, a telepítés befejezését követően, kattintson a **Bezárás** a varázsló befejezéséhez.

    ![Ügynök telepítése](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

1. Kattintson **Az infrastruktúra előkészítése** > **Cél** lehetőségre.
2. Válassza ki az előfizetést és az erőforráscsoport (**ContosoRG**) található, amely az Azure virtuális gépek jön létre a feladatátvételt követően.
3. Válassza ki a **Resource Manager**-alapú üzemi modellt.

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
2. A **Szabályzat létrehozása és társítása** területen adja meg a szabályzat nevét (**ContosoReplicationPolicy**).
3. Hagyja meg az alapértelmezett beállításokat, és kattintson az **OK** gombra.
    - A **Másolás gyakorisága** azt jelzi, hogy a különbözeti adatok (a kezdeti replikációt követően) ötpercenként replikálódnak.
    - A **Helyreállítási pont megőrzése** azt jelzi, hogy az egyes helyreállítási pontok megőrzési ideje két óra.
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

 A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. Miután a **védelem véglegesítése** feladat befejeződik, a kezdeti replikálás is befejeződik, és a virtuális gép készen áll a feladatátvételre.


## <a name="next-steps"></a>További lépések
[Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
