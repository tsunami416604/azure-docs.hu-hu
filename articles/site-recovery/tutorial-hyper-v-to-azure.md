---
title: "A helyszíni Hyper-V virtuális gépek (VMM nélkül) az Azure-bA az Azure Site Recovery vész-helyreállítási beállítása |} Microsoft Docs"
description: "Megtudhatja, hogyan vész-helyreállítási helyszíni Hyper-V virtuális gépek (VMM nélkül) beállítása az Azure-bA az Azure Site Recovery szolgáltatással."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31`/2017
ms.author: raynew
ms.openlocfilehash: 633c14bd25bc8a1419196b2c76ca94c26db68991
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Az Azure-bA helyszíni Hyper-V virtuális gépek vész-helyreállítási beállítása

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás vész-helyreállítási stratégiát infrastruktúrája azzal segíti a kezelése és koordinálása replikációjának, feladatátvételének és feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

Az oktatóanyag bemutatja, hogyan állíthat be az Azure-bA helyszíni Hyper-V virtuális gépek vész-helyreállítási. Az oktatóanyag fontos Hyper-V virtuális gépek, amelyeket nem kezel a System Center Virtual Machine Manager (VMM). Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a forrásként és célként.
> * A forrás replikációs környezet beállítása, beleértve a helyszíni Site Recovery-összetevőkhöz, és a cél replikálási környezetet.
> * Hozzon létre egy replikációs házirendet.
> * Engedélyezze a virtuális gép replikációját.

Ez az egy sorozat harmadik oktatóanyaga. Ez az oktatóanyag feltételezi, hogy már végrehajtotta a feladatokat a az előző oktatóanyagok:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [A helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)

Mielőtt elkezdené, érdemes [tekintse át a architektúra](concepts-hyper-v-to-azure-architecture.md) a vész-helyreállítási forgatókönyv esetében.

## <a name="select-a-replication-goal"></a>Replikációs cél


1. A **minden szolgáltatás** > **Recovery Services-tárolók**, kattintson a nevére, azt az előző oktatóanyag előkészített tároló **ContosoVMVault**.
2. A **bevezetés**, kattintson a **Site Recovery**. Kattintson a **infrastruktúra előkészítése**
3. A **védelmi cél** > **hol vannak a található gép**, jelölje be **helyszíni**.
4. A **hol szeretné replikálni a gépeket**, jelölje be **az Azure-bA**.
5. A **a virtuális gép**, jelölje be **Igen, a Hyper-V-vel**.
6. A **System Center VMM használatával**, jelölje be **nem**. Ezután kattintson az **OK** gombra.

    ![Replikációs cél](./media/tutorial-hyper-v-to-azure/replication-goal.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezettel beállításához hozzáadása a Hyper-V gazdagépeken a Hyper-V hely töltse le és telepítse az Azure Site Recovery Provider és az Azure Recovery Services Agent ügynököt, és a Hyper-V hely regisztrálja a tárolóban. 

1. A **infrastruktúra előkészítése**, kattintson a **forrás**.
2. Kattintson a **+ Hyper-V hely**, és adja meg a nevét, a webhely, az előző oktatóanyag létrehozott **ContosoHyperVSite**.
3. Kattintson a **+ Hyper-V Server**.
4. A szolgáltató telepítőfájl letöltéséhez.
5. Töltse le a tárolóregisztrációs kulcsot. Ez szükséges a szolgáltató telepítése futtatásakor. A kulcs a generálásától számított öt napig érvényes.

    ![Szolgáltatás letöltése](./media/tutorial-hyper-v-to-azure/download.png)
    

### <a name="install-the-provider"></a>A szolgáltató telepítése

Futtassa a szolgáltató telepítőfájl (AzureSiteRecoveryProvider.exe) minden egyes Hyper-V gazdagép meg hozzáadni a **ContosoHyperVSite** hely. A telepítő telepíti az Azure Site Recovery Provider és Recovery Services Agent ügynököt, minden Hyper-V gazdagépen.

1. Az Azure Site Recovery Provider telepítése varázslóban > **Microsoft Update**, részt vevő szolgáltató frissítések keresése a Microsoft Update segítségével.
2. A **telepítési**, fogadja el az alapértelmezett telepítési hely a szolgáltató és az ügynök, és kattintson a **telepítése**.
3. A telepítést követően a Microsoft Azure Site Recovery regisztrációs varázsló > **tároló beállításait**, kattintson **Tallózás**, és a **kulcsfájlja**, jelölje be a tárolóbeli kulccsal fájlt letöltve. 
4. Adja meg az Azure Site Recovery-előfizetést, a tároló neve (**ContosoVMVault**), és a Hyper-V hely (**ContosoHyperVSite**), amely a Hyper-V kiszolgáló tartozik.
5. A **proxybeállítások**, jelölje be **közvetlen kapcsolódás proxy nélkül Azure Site Recovery**.
6. A **regisztrációs**, miután a kiszolgáló regisztrálva van a tárolóban, kattintson a **Befejezés**.

Azure Site Recovery lekéri a Hyper-V kiszolgálóról metaadatok, és a kiszolgáló megjelenik **Site Recovery-infrastruktúra** > **Hyper-V-gazdagépek**. Ez akár 30 percet is igénybe vehet.


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki, és ellenőrizze a tároló erőforrásait. 

1. Kattintson a **infrastruktúra előkészítése** > **cél**.
2. Válassza ki az előfizetés és az erőforráscsoport **ContosoRG**, amely az Azure virtuális gépek létrehozza a feladatátvételt követően a.
3. Válassza ki a **erőforrás-kezelő "** üzembe helyezési modellben.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.


## <a name="set-up-a-replication-policy"></a>A replikációs házirend beállítása

1. Kattintson a **infrastruktúra előkészítése** > **replikációs beállítások** > **+ létrehozás és társítás**.
2. A **házirend létrehozása és társítása**, megadhatja a házirend nevét **ContosoReplicationPolicy**.
3. Hagyja meg az alapértelmezett beállításokat, és kattintson a **OK**.
    - **Másolás gyakorisága** azt jelzi, hogy a különbözeti adatok (utáni kezdeti replikálás) replikálja, ötpercenként.
    - **Helyreállítási pontok megőrzésének ideje** azt jelzi, hogy az egyes helyreállítási pontok megőrzési windows lesz két két óra.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat létrehozza-e minden órában.
    - **Kezdeti replikáció kezdési ideje**, azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
4. A házirend létrehozása után kattintson **OK**. Amikor létrehoz egy új házirend a megadott Hyper-V-hely automatikusan van társítva (**ContosoHyperVSite**)

    ![Replikációs szabályzat](./media/tutorial-hyper-v-to-azure/replication-policy.png)


## <a name="enable-replication"></a>A replikáció engedélyezése


1. A **alkalmazás replikálása**, kattintson a **forrás**. 
2. A **forrás**, jelölje be a **ContosoHyperVSite** hely. Ezután kattintson az **OK** gombra.
3. A **cél**, Azure ellenőrzése és a cél, a tároló előfizetést, és a **erőforrás-kezelő** üzembe helyezési modellben.
4. Válassza ki a **contosovmsacct1910171607** replikált adatok az előző oktatóanyag létrehozott tárfiók és a **ContosoASRnet** hálózatot, mely Azure virtuális gépek a feladatátvételt követően található.
5. A **virtuális gépek** > **válasszon**, válassza ki a replikálni kívánt virtuális Gépet. Ezután kattintson az **OK** gombra.

 Előrehaladásának nyomon követheti a **Védelemengedélyezési** műveletét **feladatok** > **Site Recovery-feladatok**. Miután a **Védelemvéglegesítési** feladat befejeződik, a kezdeti replikálás befejeződik, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések
[Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
