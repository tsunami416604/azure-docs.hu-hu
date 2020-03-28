---
title: Hyper-V (VMM-mel) vész-helyreállítási szolgáltatás beállítása az Azure Site Recovery használatával
description: Ismerje meg, hogyan állíthatja be a helyszíni Hyper-V virtuális gépek vészutáni helyreállítását a System Center VMM-felhőiben az Azure-ba a Site Recovery használatával.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067579"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>VMM-felhőkben található, Hyper-V rendszerű helyszíni virtuális gépek Azure-ba történő vészhelyreállításának beállítása

Ez az oktatóanyag bemutatja, hogyan engedélyezheti a system center virtuálisgép-kezelő (VMM) által az Azure-ba az Azure-ba az Azure-ba az Azure-ba az Azure-ba az Azure-ba az Azure-ba az Azure-ba az [Azure-ba az Azure-ba az Azure-ba](site-recovery-overview.md)felügyelt helyszíni Hyper-V virtuális gépek (VM) replikációját az Azure Site Recovery használatával. Ha nem használja a VMM-et, [kövesse ezt az oktatóanyagot.](hyper-v-azure-tutorial.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * Állítsa be a forrásreplikációs környezetet, beleértve a helyszíni site recovery összetevőket és a célreplikációs környezetet.
> * Állítsa be a virtuális gépészeti hálózatok és az Azure virtuális hálózatok közötti hálózati leképezést.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

> [!NOTE]
> Oktatóanyagok megmutatja a legegyszerűbb telepítési útvonalat egy forgatókönyv. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a [Webhely-helyreállítási dokumentáció](/azure/site-recovery/) **Útmutató** útmutatójában található cikkeket.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már befejezte a következő oktatóanyagokat:

1. [Készítse elő az Azure-t](tutorial-prepare-azure.md)
1. [Helyszíni Hyper-V-kiszolgálók előkészítése](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. Az Azure Portalon nyissa meg a **Recovery Services-tárolók,** és válassza ki a **ContosoVMVault-tároló,** amely az [Azure-oktatóanyag](tutorial-prepare-azure.md#create-a-recovery-services-vault) ban létrehozott.
1. Az **Első lépések csoportban**válassza a Site Recovery Prepare Infrastructure **(Webhely-helyreállítási** > **infrastruktúra előkészítése) lehetőséget,** és adja meg a következő beállításokat:
    1. **Védelmi cél** > Hol találhatók a **On-premises****gépek?**
    1. **Hol szeretné replikálni a gépeket?** **To Azure**
    1. **A gépek virtualizáltak?**, válassza az **Igen, a Hyper-V**lehetőséget.
    1. **A System Center VMM segítségével kezeli a Hyper-V állomásokat?** **Yes**
1. Válassza **az OK gombot.**

   ![Replikációs cél](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. Ha **Deployment planning**nagy telepítést tervez, töltse le a Telepítéstervező a Hyper-V-höz című témakört a lap hivatkozásáról. [További információ](hyper-v-deployment-planner-overview.md) a Hyper-V üzembe helyezési tervezéséről.
1. Ebben az oktatóanyagban nincs szükségünk a Központi telepítéstervezőre. A **Van már befejeződött a telepítés tervezése?**, válassza **a I will do it later**lehetőséget, majd válassza az **OK gombot.**

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállításakor telepítse az Azure Site Recovery Provider a VMM-kiszolgálón, és regisztrálja a kiszolgálót a tárolóban. Az Azure Recovery Services-ügynököt minden Hyper-V gazdagépen telepíti.

1. **Infrastruktúra előkészítése**. Válassza a **Forrás**lehetőséget.
1. **Forrás előkészítése**. VMM-kiszolgáló hozzáadásához válassza a **+ VMM** lehetőséget. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e.
1. Töltse le a Microsoft Azure webhely-helyreállítási szolgáltató telepítőjének letöltését.
1. Töltse le a tároló regisztrációs kulcsát. Erre a kulcsra a Szolgáltató beállításának futtatásakor van szükség. A kulcs a generálásától számított öt napig érvényes.
1. Töltse le a Microsoft Azure Recovery Services ügynök telepítőjének letöltését.

   ![Letöltésszolgáltató, regisztrációs kulcs és ügynök](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>A szolgáltató telepítése a VMM-kiszolgálón

1. Az Azure site recovery szolgáltató telepítővarázslójában a **Microsoft Update**. Engedélyezve, hogy a Microsoft Update szolgáltatás segítségével keresse meg a szolgáltató frissítéseit.
1. **Telepítés**. Fogadja el a szolgáltató alapértelmezett telepítési helyét, és válassza a **Telepítés**lehetőséget.
1. A telepítés után a Microsoft Azure Site Recovery Registration wizardban válassza a **Vault beállításai**, **Tallózás**lehetőséget, és a **Kulcsfájl**ban válassza ki a letöltött tárolókulcsfájlt.
1. Adja meg az Azure Site Recovery-előfizetést és a tároló nevét (**ContosoVMVault).** Adja meg a VMM-kiszolgáló rövid nevét a tárolóban való azonosításához.
1. **Proxy beállítások**. Válassza **a Csatlakozás közvetlenül az Azure Site Recovery-hez proxy nélkül**lehetőséget.
1. Fogadja el az adatok titkosításához használt tanúsítvány alapértelmezett helyét. A titkosított adatok visszafejtése a feladatátvételkor történik.
1. **Felhőmetaadatok szinkronizálása**. Válassza **a Felhőalapú metaadatok szinkronizálása a Site Recovery portálra lehetőséget.** Ennek a műveletnek minden kiszolgálón csak egyszer kell megtörténnie. Ezután válassza a **Regisztráció**lehetőséget.
1. Miután regisztrálta a kiszolgálót a tárolóban, válassza a **Befejezés**lehetőséget.

A regisztráció befejezése után az Azure Site Recovery lekéri a kiszolgálóról származó metaadatokat, és a VMM-kiszolgáló megjelenik a **Site Recovery Infrastructure alkalmazásban.**

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>A Recovery Services-ügynök telepítése Hyper-V-gazdagépekre

Telepítse az ügynököt minden olyan Hyper-V gazdagépre, amely replikálni kívánt virtuális gépeket tartalmaz.

A Microsoft Azure Recovery Services Agent telepítővarázslójában adja meg a következő beállításokat:

1. **Előfeltételek ellenőrzése**. Válassza a **Tovább lehetőséget.** A hiányzó előfeltételek automatikusan települnek.
1. **Telepítési beállítások**. Fogadja el a telepítési helyet és a gyorsítótár helyét. A gyorsítótár-meghajtónak legalább 5 GB tárhelyre van szüksége. 600 GB vagy több szabad hellyel rendelkező meghajtó használatát javasoljuk. Ezt követően válassza az **Install** (Telepítés) parancsot.
1. **Telepítés**. Amikor a telepítés befejeződik, a varázsló befejezéséhez válassza a **Bezárás** gombot.

   ![Ügynök telepítése](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

1. Válassza **az Infrastruktúra cél előkészítése** > **lehetőséget.**
1. Válassza ki az előfizetést és az erőforráscsoportot (**ContosoRG**), amelyben az Azure-beli virtuális gépek jönnek létre a feladatátvétel után.
1. Válassza ki az **Erőforrás-kezelő** telepítési modelljét.

A Site Recovery ellenőrzi, hogy van-e egy vagy több kompatibilis Azure-tárfiók és -hálózat.

## <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

1. **Hely-helyreállítási infrastruktúra** > **hálózat leképezése** > **hálózati leképezések**. Válassza a **+Hálózati leképezés** ikont.
1. **Hálózati leképezés hozzáadása**. Válassza ki a **Forrásrendszerközpont** VMM-kiszolgálóját. A **cél,** válassza az Azure.For the Target , select Azure.
1. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
1. **Forráshálózat**. Válassza ki a helyszíni virtuális gép forráshálózatát.
1. **Célhálózat**. Válassza ki azt az Azure-hálózatot, amelyben az Azure-beli virtuális gépek replikája található lesz, amikor feladatátvétel után jönnek létre. Ezután válassza **az OK gombot.**

   ![Hálózatleképezés](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Válassza **az Infrastruktúra** > **replikációs beállításainak** > előkészítése **+Létrehozás és társítás lehetőséget.**
1. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. A **ContosoReplicationPolicy házirendet**használjuk.
1. Fogadja el az alapértelmezett beállításokat, és válassza **az OK gombot:**
   - **A másolásgyakoriság** azt jelzi, hogy a kezdeti replikáció után a különbözeti adatok ötpercenként replikálódnak.
   - **A helyreállítási pontok megőrzése** azt jelzi, hogy minden helyreállítási pont két órán keresztül megmarad.
   - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
   - **A kezdeti replikáció indítási ideje** azt jelzi, hogy a kezdeti replikáció azonnal megkezdődik.
   - **Az Azure-ban tárolt adatok titkosítása** az alapértelmezett (**Ki**) értékre van állítva, és azt jelzi, hogy az Azure-ban az inaktív adatok nincsenek titkosítva.
1. A házirend létrehozása után válassza az **OK gombot.** Amikor új szabályzatot hoz létre, az automatikusan a VMM-felhőhöz lesz társítva.

## <a name="enable-replication"></a>A replikáció engedélyezése

1. **Alkalmazás replikálása**. Válassza a **Forrás**lehetőséget.
1. **Forrás**. Válassza ki a VMM-felhőt. Ezután válassza **az OK gombot.**
1. **Célpont**. Ellenőrizze a cél (Azure), a tároló előfizetés, és válassza ki a **Resource Manager** modell.
1. Válassza ki a **contosovmsacct1910171607** tárfiókot és a **ContosoASRnet** Azure-hálózatot.
1. **Virtuális gépek:** > Válassza ki a**lehetőséget.** Válassza ki a replikálni kívánt virtuális gép. Ezután válassza **az OK gombot.**

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **véglegesítési védelem** feladat befejezése után a kezdeti replikáció befejeződik, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
