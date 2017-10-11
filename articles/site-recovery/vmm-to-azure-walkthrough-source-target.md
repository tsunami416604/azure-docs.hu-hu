---
title: "Állítsa be a forrás és cél Hyper-V replikáció az Azure-ba (a System Center VMM) az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Forrás és cél Hyper-V virtuális gépek replikálás beállításainak beállítása az Azure storage az Azure Site Recovery VMM-felhőkben ismertetett lépéseket foglalja"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>8. lépés: A forrás- és a cél az Azure-ba (VMM) szolgáltatással a Hyper-V replikáció beállítása

Miután [létrehozni egy tárolót](vmm-to-azure-walkthrough-create-vault.md) , és adja meg, mit szeretne replikálni, ez a cikk forrású és beállítások konfigurálása a System Center Virtual Machine Manager (VMM) felhők helyszíni Hyper-V virtuális gépek replikálása esetén az Azure-bA használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.

Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

S1. Kattintson **Az infrastruktúra előkészítése** > **Forrás** lehetőségre.

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez.

    ![A forrás beállítása](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e, illetve, hogy a VMM-kiszolgáló megfelel-e [az előfeltételeknek és az URL-követelményeknek](#prerequisites).
4. Töltse le az Azure Site Recovery Provider telepítőfájlját.
5. Töltse le a regisztrációs kulcsot. Erre a telepítő futtatása során lesz szükség. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>A szolgáltató telepítése a VMM-kiszolgálón

1. Futtassa a szolgáltató telepítőfájlját a VMM-kiszolgálón.
2. A **Microsoft Update** lapon kérheti a frissítések beszerzését, így a rendszer a Microsoft Update-szabályzatnak megfelelően telepíteni fogja a Providerhez kiadott frissítéseket.
3. A **Telepítés** lapon tetszés szerint fogadja el vagy módosítsa a Provider alapértelmezett telepítési helyét, majd kattintson a **Telepítés** gombra.

    ![Telepítés helye](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. A telepítés befejezését követően a VMM-kiszolgálónak a tárolóban való regisztrálásához kattintson a **Regisztráció** gombra.
5. A **Tároló beállításai** lapon kattintson a **Tallózás** gombra, és válassza ki a tároló kulcsfájlját. Adja meg az Azure Site Recovery-előfizetést és a tároló nevét.

    ![Kiszolgáló regisztrációja](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. Az **Internetkapcsolat** beállításnál adja meg, hogy a VMM-kiszolgálón futó Provider hogyan csatlakozzon a Site Recoveryhez az interneten keresztül.

   * Ha azt szeretné, hogy a Provider közvetlenül kapcsolódjon, válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
   * Ha a meglévő proxy hitelesítést igényel, illetve ha egyéni proxyt szeretne használni, válassza a **Csatlakozás az Azure Site Recoveryhez proxykiszolgálóval** lehetőséget.
   * Ha az egyéni proxy használatát választja, meg kell adnia a címet, a portot és a hitelesítő adatokat.
   * Ha proxyt használt, ellenőrizze, hogy engedélyezte-e az [előfeltételekben](#on-premises-prerequisites) leírt URL-címek elérését.
   * Ha egyéni proxyt használ, a rendszer automatikusan létrehoz egy, a megadott hitelesítő adatokat alkalmazó VMM RunAs-fiókot (DRAProxyAccount). Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A VMM RunAs-fiók beállításait a VMM-konzolban módosíthatja. A **Beállítások** menüben bontsa ki a **Biztonság** > **Futtató fiókok** lehetőséget, majd módosítsa a DRAProxyAccount jelszavát. A beállítás akkor lép érvénybe, ha újraindítja a VMM szolgáltatást.

     ![internet](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. Fogadja el vagy módosítsa az adatok titkosításához automatikusan létrehozott SSL-tanúsítvány helyét. Ez a tanúsítvány akkor használatos, ha bekapcsolja az Azure által védett felhőre vonatkozó adattitkosítást az Azure Site Recovery portálon. Őrizze biztonságos helyen a tanúsítványt. Ha feladatátvételt végez az Azure-ba, és korábban bekapcsolta az adattitkosítást, szüksége lesz rá a titkosítás feloldásához.
8. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Fürtkonfiguráció használata esetén adja meg a VMM-fürtszerepkör nevét.
9. Jelölje be a **Felhőmetaadatok szinkronizálása** jelölőnégyzetet, ha szeretné a VMM-kiszolgálón futó összes felhő metaadatait szinkronizálni a tárolóval. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem szeretné az összes felhőt szinkronizálni, ne jelölje be a jelölőnégyzetet, és szinkronizálja egyenként a felhőket a VMM-konzolban, a felhők tulajdonságainál. A folyamat befejezéséhez kattintson a **Regisztráció** elemre.

    ![Kiszolgáló regisztrációja](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. Elindul a regisztráció. A regisztráció befejezését követően a kiszolgáló megjelenik a **Site Recovery-infrastruktúra** > **VMM-kiszolgálók** panelen.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Az Azure Recovery Services Agent ügynök telepítése a Hyper-V gazdagépekre

1. A Provider beállítását követően töltse le az Azure Recovery Services Agent telepítőfájlját. Végezze el a telepítést a CMM-felhőben futó összes Hyper-V-kiszolgálón.

    ![Hyper-V helyek](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. Az **Előfeltételek ellenőrzése** részben kattintson a **Tovább** gombra. A rendszer automatikusan telepíti a hiányzó előfeltételeket.

    ![Recovery Services Agent, előfeltételek](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. A **Telepítési beállítások** részben fogadja el vagy módosítsa a telepítés, illetve a gyorsítótár helyét. A gyorsítótár már akár 5 GB-os kapacitású meghajtókon is beállítható, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely. Ezt követően kattintson a **Telepítés** gombra.
4. A telepítés befejezését követően kattintson a **Bezárás** gombra.

    ![A MARS Agent regisztrálása](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>Parancssori telepítés
A Microsoft Azure Recovery Services Agent a következő paranccsal telepíthető a parancssorból:

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Internetes proxyn keresztüli elérés beállítása a Site Recoveryhez Hyper-V gazdagépekről

A Hyper-V gazdagépeken futó Recovery Services Agent ügynöknek a virtuális gépek replikálása céljából az interneten keresztül el kell érnie az Azure-t. Ha proxyn keresztül éri el az internetet, adja meg a következő beállításokat:

1. Nyissa meg a Microsoft Azure Backup MMC beépülő modult a Hyper-V gazdagépen. Alapértelmezés szerint a Microsoft Azure Backup parancsikonja az asztalon vagy a következő mappában érhető el: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.
3. A **Proxykonfiguráció** lapon adja meg a proxykiszolgálóra vonatkozó információkat.

    ![A MARS Agent regisztrálása](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. Ellenőrizze, hogy az ügynök el tudja-e érni az [előfeltételek](#on-premises-prerequisites) között megadott URL-címeket.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása
Adja meg a replikációhoz használni kívánt Azure-tárfiókot, valamint az Azure-hálózatot, amelyhez az Azure virtuális gépek a feladatátvételt követően csatlakozhatnak.

1. Kattintson **Az infrastruktúra előkészítése** > **Cél** elemre, és válassza ki az előfizetést és az erőforráscsoportot, ahol a feladatátviteli virtuális gépeket létre szeretné hozni. Kattintson a feladatátvételi virtuális gépekre használni kívánt Azure üzembe helyezési modellre (klasszikus vagy erőforrás-kezelés).

    ![Tárolás](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

    ![Tárolás](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. Ha még nem hozott létre tárfiókot, és ezt szeretné most megtenni a Resource Managerben, kattintson a **+Tárfiók** elemre.  A **Tárfiók létrehozása** panelen adja meg a fiók nevét és típusát, az előfizetést, valamint a helyet. A fióknak és a Recovery Services-tárolónak ugyanazon a helyen kell lennie.

   ![Tárolás](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * Ha a klasszikus modellt használó tárfiókot szeretne létrehozni, azt az Azure-portálon teheti meg. [További információ](../storage/common/storage-create-storage-account.md)
   * Ha Prémium szintű tárfiókot használ a replikált adatok tárolására, a helyszíni adatokban bekövetkező változásokat rögzítő replikálási naplók tárolására be kell állítania egy standard tárfiókot is.
5. Ha még nem hozott létre Azure-hálózatot, és ezt szeretné most megtenni a Resource Managerben, kattintson a **+Hálózat** elemre. A **Virtuális hálózat létrehozása** panelen adja meg a hálózat nevét, a címtartományt, az alhálózati adatokat, az előfizetést, valamint a helyet. A hálózatnak és a Recovery Services-tárolónak ugyanazon a helyen kell lennie.

   ![Network (Hálózat)](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   Ha a klasszikus modellt használó hálózatot szeretne létrehozni, azt az Azure-portálon teheti meg. [További információk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).





## <a name="next-steps"></a>Következő lépések

Ugrás a [9. lépés: hálózatleképezés konfigurálása](vmm-to-azure-walkthrough-network-mapping.md)
