---
title: A helyszíni Hyper-V virtuális gépek vész-helyreállítási beállítása az Azure-bA az Azure Site Recovery VMM-felhőkben |} Microsoft Docs
description: Útmutató a helyszíni Hyper-V virtuális gépek vész-helyreállítási beállítása a System Center VMM-felhőkben az Azure-ba, az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8bbbe0a5aca20222ff7385be9d0ecf0a4224d5c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Állítsa be a helyszíni Hyper-V virtuális gépek vész-helyreállítási VMM-felhőkben az Azure-bA

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Az oktatóanyag bemutatja, hogyan állíthatja be a helyszíni Hyper-V virtuális gépek Azure-ba irányuló vészhelyreállítását. Az oktatóanyag fontos, hogy a System Center Virtual Machine Manager (VMM) által felügyelt Hyper-V virtuális gépek. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * A forrás replikációs környezet (többek között a helyszíni Site Recovery-összetevők), valamint a cél replikálási környezet beállítása.
> * A hálózatleképezés, beállítása a VMM Virtuálisgép-hálózatok és az Azure virtuális hálózatok közötti.
> * Replikációs házirend létrehozása
> * Virtuális gép replikálásának engedélyezése

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)

Mielőtt elkezdené, érdemes [áttekinteni az architektúrát](concepts-hyper-v-to-azure-architecture.md), hogy szerepel-e benne ez a vészhelyreállítási forgatókönyv.



## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **minden szolgáltatás** > **Recovery Services-tárolók**, kattintson a tároló nevére, ezek az oktatóanyagok a használjuk **ContosoVMVault**.
2. Az **Első lépések** területen kattintson a **Site Recovery** elemre. Ezt követően kattintson **Az infrastruktúra előkészítése** elemre.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **a virtuális gép**, jelölje be **Igen, a Hyper-V-vel**.
6. A **System Center VMM használatával**, jelölje be **Igen**. Ezután kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezettel beállításakor az Azure Site Recovery Provider és az Azure Recovery Services Agent ügynök telepítése, és regisztrálja a helyszíni kiszolgálók a tárolóban. 

1. **Az infrastruktúra előkészítése** területen kattintson a **Forrás** lehetőségre.
2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **System Center VMM-kiszolgáló** megjelenik **kiszolgálótípus**.
3. Töltse le a Microsoft Azure Site Recovery Provider telepítőjéhez.
4. Töltse le a tároló regisztrációs kulcsát. Erre szüksége lesz a szolgáltató telepítésének futtatásakor. A kulcs a generálásától számított öt napig érvényes.
5. Töltse le a Recovery Services Agent ügynököt.

    ![Letöltés](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>A szolgáltató telepítése a VMM-kiszolgálón

1. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
2. A **telepítési**fogadja el az alapértelmezett telepítési hely a szolgáltató, és kattintson a **telepítése**. 
3. A telepítést követően a Microsoft Azure Site Recovery regisztrációs varázsló > **tároló beállításait**, kattintson **Tallózás**, és a **kulcsfájlja**, jelölje be a tárolóbeli kulccsal fájlt letöltve.
4. Adja meg az Azure Site Recovery-előfizetést, és a tároló neve (**ContosoVMVault**). Adjon meg egy rövid nevet a VMM-kiszolgálón való azonosításának megkönnyítése érdekében a tárolóban lévő állapottal.
5. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
6. Fogadja el az alapértelmezett helyet, az adatok titkosítására használt tanúsítvány. Amikor a rendszer átadja a titkosított adatok lesz visszafejtve.
7. A **Synchronize cloud metaadatok**, jelölje be **szinkronizálási felhőalapú meta adatokat a Site Recovery portálhoz**. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Kattintson a **regisztrálása**.
8. Miután a kiszolgáló regisztrálva van a tárolóban, kattintson **Befejezés**.

Regisztráció befejezése után az Azure Site Recovery lekéri a kiszolgálóról metaadatok, és a VMM-kiszolgáló megjelenik **Site Recovery-infrastruktúra**.

### <a name="install-the-recovery-services-agent"></a>A Recovery Services agent telepítése

Az ügynök telepítése minden replikálni kívánt virtuális gépeket tartalmazó Hyper-V gazdagépen.

1. A Microsoft Azure helyreállítási szolgáltatások ügynök telepítővarázslójának > **szükséges előfeltételek ellenőrzése**, kattintson a **következő**. Automatikusan telepíti a hiányzó előfeltételeket.
2. A **telepítési beállítások**, fogadja el a telepítés helyét, illetve a gyorsítótár helyét. A gyorsítótár-meghajtón kell legalább 5 GB tárhelyet. Azt javasoljuk, hogy olyan meghajtót, amelyen legalább 600 GB szabad terület. Ezt követően kattintson a **Telepítés** gombra.
3. A **telepítési**, a telepítés befejezését követően, kattintson a **Bezárás** a varázsló befejezéséhez.

    ![Ügynök telepítése](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

1. Kattintson **Az infrastruktúra előkészítése** > **Cél** lehetőségre.
2. Válassza ki az előfizetés és az erőforráscsoport (**ContosoRG**) található, amely az Azure virtuális gépek létrehozza a feladatátvételt követően.
3. Válassza ki a **Resource Manager**-alapú üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.


## <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

1. A **Site Recovery-infrastruktúra** > **Hálózatleképezések** > **Hálózatleképezés** menüpontban kattintson a **+Hálózatleképezés** ikonra.
2. A **hálózatleképezés hozzáadása**, válassza ki a forrás VMM-kiszolgálón. Válassza ki **Azure** céljaként.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **Forráshálózat**, válassza ki a forrás helyi Virtuálisgép-hálózatot.
5. A **célhálózat**, válassza ki az Azure-hálózatot, mely replika Azure virtuális gépek kerülnek, ha a feladatátvételt követően jönnek létre. Ezután kattintson az **OK** gombra.

    ![Hálózatleképezés](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Kattintson **Az infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** lehetőségre.
2. A **Szabályzat létrehozása és társítása** területen adja meg a szabályzat nevét (**ContosoReplicationPolicy**).
3. Hagyja meg az alapértelmezett beállításokat, és kattintson az **OK** gombra.
    - A **Másolás gyakorisága** azt jelzi, hogy a különbözeti adatok (a kezdeti replikációt követően) ötpercenként replikálódnak.
    - A **Helyreállítási pont megőrzése** azt jelzi, hogy az egyes helyreállítási pontok megőrzési ideje két óra.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - A **Kezdeti replikáció kezdési ideje** azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
    - **Az Azure-on tárolt adatok titkosítása** -az alapértelmezett **ki** beállítás jelzi, hogy aktívan adatok Azure-ban nincs titkosítva.
4. A szabályzat létrehozása után kattintson az **OK** gombra. Az újonnan létrehozott szabályzatokat a rendszer automatikusan társítja a VMM-felhővel.

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Az **Alkalmazás replikálása** lépésben kattintson a **Forrás** lehetőségre. 
2. A **forrás**, válassza ki a VMM-felhőben. Ezután kattintson az **OK** gombra.
3. A **cél**, Azure ellenőrizze a tároló előfizetés célként, és válassza ki a **erőforrás-kezelő** modell.
4. Válassza ki a **contosovmsacct1910171607** tárfiók, és a **ContosoASRnet** Azure-hálózatot.
5. A **Virtuális gépek** > **Kiválasztás** menüben jelölje ki a replikálni kívánt virtuális gépeket. Ezután kattintson az **OK** gombra.

 A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. Miután a **Védelemvéglegesítési** feladat befejeződik, a kezdeti replikálás befejeződik, és a virtuális gép készen áll a feladatátvételre.


## <a name="next-steps"></a>További lépések
[Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
