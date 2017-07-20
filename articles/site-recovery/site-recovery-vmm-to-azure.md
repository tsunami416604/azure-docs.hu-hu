---
title: "VMM-felhőben lévő Hyper-V virtuális gépek replikálása az Azure-ba | Microsoft Docs"
description: "System Center VMM-felhőben felügyelt Hyper-V virtuális gépek replikálása, feladatátvétele és helyreállítása az Azure-ba"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: b53e7f5454cd97f013fdce052f0a990a44958dee
ms.contentlocale: hu-hu
ms.lasthandoff: 07/14/2017

---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>VMM-felhőben lévő Hyper-V virtuális gépek replikálása az Azure-ba az Azure Portal Site Recovery szolgáltatásának használatával
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [Klasszikus Azure portál](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Klasszikus PowerShell](site-recovery-deploy-with-powershell.md)


Ez a cikk azt ismerteti, hogy hogyan replikálhat System Center VMM-felhőkben felügyelt helyszíni Hyper-V virtuális gépeket az Azure-ba az Azure Portal [Azure Site Recovery](site-recovery-overview.md) szolgáltatásának segítségével.

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.

Ha gépeket szeretne migrálni az Azure-ba (feladat-visszavétel nélkül), [ebben a cikkben](site-recovery-migrate-to-azure.md) további információkat talál.


## <a name="deployment-steps"></a>A központi telepítés lépései

Kövesse a cikkben leírtakat az üzembe helyezési lépések elvégzéséhez:


1. [További információk](site-recovery-components.md) az ehhez az üzembe helyezéshez szükséges architektúráról. Emellett [többet megtudhat](site-recovery-hyper-v-azure-architecture.md) arról, hogyan működik a Hyper-V-replikáció a Site Recoveryben.
2. Előfeltételek és korlátozások ellenőrzése.
3. Azure-hálózat és tárfiókok beállítása.
4. A helyszíni VMM-kiszolgáló és a Hyper-V gazdagépek előkészítése.
5. Recovery Services-tároló létrehozása. A tároló konfigurációs beállításokat tartalmaz, és koordinálja a replikációt.
6. Forrásbeállítások megadása. A VMM-kiszolgáló regisztrálása a tárolóban. Telepítse az Azure Site Recovery Providert a VMM-kiszolgálón. Telepítse a Microsoft Recovery Services-ügynököt a Hyper-V-gazdagépekre.
7. Adja meg a cél- és replikációs beállításokat.
8. Engedélyezze a replikálást a virtuális gépek számára.
9. Egy feladatátvételi teszt futtatásával ellenőrizze, hogy minden a vártnak megfelelően működik-e.



## <a name="prerequisites"></a>Előfeltételek


**Támogatási követelmények** | **Részletek**
--- | ---
**Azure** | Az [Azure-követelmények](site-recovery-prereq.md#azure-requirements) ismertetése.
**Helyszíni kiszolgálók** | A helyszíni VMM-kiszolgáló és a Hyper-V-gazdagépek követelményeinek [ismertetése](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-azure).
**Helyszíni Hyper-V rendszerű virtuális gépek** | A replikálni kívánt virtuális gépeknek [támogatott operációs rendszert](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) kell futtatniuk, valamint meg kell felelniük az [Azure-előfeltételeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**Azure-beli URL-címek** | A VMM-kiszolgálónak hozzáférésre van szüksége a következő URL-címekhez:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Ha IP-címeken alapuló tűzfalszabályokat használ, ellenőrizze, hogy engedélyezik-e az Azure-ral való kommunikációt.<br/></br> Engedélyezze az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).<br/></br> Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).


## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre
Az üzembe helyezésre a következő műveletekkel készülhet fel:

1. [Állítson be egy Azure-hálózatot](#set-up-an-azure-network), amely az Azure virtuális gépeket fogja tartalmazni a feladatátvétel után.
2. [Állítsa be az Azure-tárfiókot](#set-up-an-azure-storage-account) a replikált adatok tárolásához.
3. [Készítse elő a VMM-kiszolgálót](#prepare-the-vmm-server) a Site Recovery üzembe helyezésére.
4. Készüljön fel a hálózatleképezésre. Állítsa be úgy a hálózatokat, hogy a Site Recovery üzembe helyezésekor lehessen konfigurálni a hálózatleképezéseket.

### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása
Szükség van egy Azure-hálózatra, amelyhez a feladatátvételt követően létrehozott Azure virtuális gépek csatlakozni tudnak.

* A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
* Az átadott Azure virtuális gépekhez használni kívánt erőforrásmodelltől függően az Azure-hálózatot is [Resource Manager üzemmódban](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) vagy [klasszikus üzemmódban](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) kell beállítani.
* Javasoljuk, hogy a kezdés előtt hozza létre a hálózatot. Ha most kihagyja ezt a lépést, a Site Recovery üzembe helyezése közben kell majd elvégeznie.
A Site Recovery által használt Azure-hálózatok nem [mozgathatók](../azure-resource-manager/resource-group-move-resources.md) az előfizetésen belül vagy előfizetések között.

### <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása
* Az Azure-ba replikált adatok tárolásához standard/prémium Azure Storage-fiókra lesz szüksége. A [Prémium Storage-ot](../storage/storage-premium-storage.md) azok a virtuális gépek használják, amelyeknek folyamatosan nagy I/O-teljesítményre és alacsony késleltetésre van szüksége az I/O-igényes számítási feladatokhoz. Ha Prémium szintű fiókot kíván használni a replikált adatok tárolására, a helyszíni adatokban bekövetkező változásokat rögzítő replikálási naplók tárolására be kell állítania egy standard szintű Storage-fiókot is. A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
* Az átadott Azure virtuális gépekhez használni kívánt erőforrásmodelltől függően a fiókot is [Resource Manager üzemmódban](../storage/storage-create-storage-account.md) vagy [klasszikus üzemmódban](../storage/storage-create-storage-account-classic-portal.md) kell beállítani.
* Javasoljuk, hogy még a kezdés előtt állítsa be a fiókot. Ha most kihagyja ezt a lépést, a Site Recovery üzembe helyezése közben kell majd elvégeznie.
- Vegye figyelembe, hogy a Site Recovery által használt tárfiókok nem [mozgathatók](../azure-resource-manager/resource-group-move-resources.md) az előfizetésen belül vagy eltérő előfizetések között.

### <a name="prepare-the-vmm-server"></a>A VMM-kiszolgáló előkészítése
* Ellenőrizze, hogy a VMM-kiszolgáló megfelel-e az [előfeltételeknek](#prerequisites).
* A Site Recovery üzembe helyezése során megadhatja, hogy a VMM-kiszolgálón futó összes felhő elérhető legyen-e az Azure Portalon. Ha azt szeretné, hogy csak bizonyos felhők jelenjenek meg a portálon, ezt a beállítást a felhőben, a VMM felügyeleti konzoljában adhatja meg.

### <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése
A Site Recovery üzembe helyezésekor a hálózatleképezést is be kell állítania. A hálózatleképezés kapcsolatot hoz létre a forrás VMM-virtuálisgép-hálózatok és a cél Azure-hálózatok között, így biztosítja a következőket:

* A feladataikat azonos hálózatba átadó gépek képesek lesznek csatlakozni egymáshoz, még akkor is, ha nem ugyanazzal a módszerrel vagy ugyanabban a helyreállítási tervben hajtották végre a feladatátvételt.
* Ha a cél Azure-hálózatban hálózati átjáró működik, az Azure virtuális gépek képesek csatlakozni a helyszíni virtuális gépekhez.
* A hálózatleképezés beállításához a következőkre van szükség:

  * Ellenőrizze, hogy a forrás Hyper-V gazdakiszolgálón futó virtuális gépek csatlakoznak-e egy VMM-virtuálisgép-hálózathoz. Ezt a hálózatot kösse össze egy, a felhőhöz társított logikai hálózattal.
  * Rendelkezzen egy, a [fentiekben](#set-up-an-azure-network) leírt Azure-hálózattal.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Kattintson az **Új** > **Felügyelet és kezelés** > **Backup és Site Recovery (OMS)** lehetőségre.

    ![Új tároló](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válasszon egyet ezek közül.
4. [Hozzon létre egy erőforráscsoportot](../azure-resource-manager/resource-group-template-deploy-portal.md), vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. A gépeket ebbe a régióba replikálja a rendszer. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
5. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton** > **Tároló létrehozása** elemre.

    ![Új tároló](./media/site-recovery-vmm-to-azure/new-vault.png)

Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** panelen.


## <a name="select-the-protection-goal"></a>Védelmi cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és hová.

1. A **Recovery Services-tárolók** listából válassza ki a tárolót.
2. A **Bevezetés** ablakban kattintson a **Site Recovery** > **Az infrastruktúra előkészítése** > **Védelmi cél** lehetőségre.

    ![Célok megválasztása](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. A **Védelmi cél** ablakban válassza **Az Azure-ba**, valamint a **Igen, a következővel: Hyper-V** lehetőséget. Az **Igen** lehetőség kiválasztásával erősítse meg, hogy VMM-mel felügyeli a Hyper-V gazdagépeket és a helyreállítási helyet. Ezután kattintson az **OK** gombra.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Telepítse a VMM-kiszolgálóra az Azure Site Recovery Providert, és regisztrálja a kiszolgálót a tárolóban. Telepítse az Azure Recovery Services Agent ügynököt a Hyper-V gazdagépekre.

1. Kattintson **Az infrastruktúra előkészítése** > **Forrás** lehetőségre.

    ![A forrás beállítása](./media/site-recovery-vmm-to-azure/set-source1.png)

2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez.

    ![A forrás beállítása](./media/site-recovery-vmm-to-azure/set-source2.png)

3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e, illetve, hogy a VMM-kiszolgáló megfelel-e [az előfeltételeknek és az URL-követelményeknek](#prerequisites).
4. Töltse le az Azure Site Recovery Provider telepítőfájlját.
5. Töltse le a regisztrációs kulcsot. Erre a telepítő futtatása során lesz szükség. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/site-recovery-vmm-to-azure/set-source3.png)


## <a name="install-the-provider-on-the-vmm-server"></a>A szolgáltató telepítése a VMM-kiszolgálón

1. Futtassa a szolgáltató telepítőfájlját a VMM-kiszolgálón.
2. A **Microsoft Update** lapon kérheti a frissítések beszerzését, így a rendszer a Microsoft Update-szabályzatnak megfelelően telepíteni fogja a Providerhez kiadott frissítéseket.
3. A **Telepítés** lapon tetszés szerint fogadja el vagy módosítsa a Provider alapértelmezett telepítési helyét, majd kattintson a **Telepítés** gombra.

    ![Telepítés helye](./media/site-recovery-vmm-to-azure/provider2.png)
4. A telepítés befejezését követően a VMM-kiszolgálónak a tárolóban való regisztrálásához kattintson a **Regisztráció** gombra.
5. A **Tároló beállításai** lapon kattintson a **Tallózás** gombra, és válassza ki a tároló kulcsfájlját. Adja meg az Azure Site Recovery-előfizetést és a tároló nevét.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. Az **Internetkapcsolat** beállításnál adja meg, hogy a VMM-kiszolgálón futó Provider hogyan csatlakozzon a Site Recoveryhez az interneten keresztül.

   * Ha azt szeretné, hogy a Provider közvetlenül kapcsolódjon, válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
   * Ha a meglévő proxy hitelesítést igényel, illetve ha egyéni proxyt szeretne használni, válassza a **Csatlakozás az Azure Site Recoveryhez proxykiszolgálóval** lehetőséget.
   * Ha az egyéni proxy használatát választja, meg kell adnia a címet, a portot és a hitelesítő adatokat.
   * Ha proxyt használt, ellenőrizze, hogy engedélyezte-e az [előfeltételekben](#on-premises-prerequisites) leírt URL-címek elérését.
   * Ha egyéni proxyt használ, a rendszer automatikusan létrehoz egy, a megadott hitelesítő adatokat alkalmazó VMM RunAs-fiókot (DRAProxyAccount). Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A VMM RunAs-fiók beállításait a VMM-konzolban módosíthatja. A **Beállítások** menüben bontsa ki a **Biztonság** > **Futtató fiókok** lehetőséget, majd módosítsa a DRAProxyAccount jelszavát. A beállítás akkor lép érvénybe, ha újraindítja a VMM szolgáltatást.

     ![internet](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. Fogadja el vagy módosítsa az adatok titkosításához automatikusan létrehozott SSL-tanúsítvány helyét. Ez a tanúsítvány akkor használatos, ha bekapcsolja az Azure által védett felhőre vonatkozó adattitkosítást az Azure Site Recovery portálon. Őrizze biztonságos helyen a tanúsítványt. Ha feladatátvételt végez az Azure-ba, és korábban bekapcsolta az adattitkosítást, szüksége lesz rá a titkosítás feloldásához.
8. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Fürtkonfiguráció használata esetén adja meg a VMM-fürtszerepkör nevét.
9. Jelölje be a **Felhőmetaadatok szinkronizálása** jelölőnégyzetet, ha szeretné a VMM-kiszolgálón futó összes felhő metaadatait szinkronizálni a tárolóval. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem szeretné az összes felhőt szinkronizálni, ne jelölje be a jelölőnégyzetet, és szinkronizálja egyenként a felhőket a VMM-konzolban, a felhők tulajdonságainál. A folyamat befejezéséhez kattintson a **Regisztráció** elemre.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. Elindul a regisztráció. A regisztráció befejezését követően a kiszolgáló megjelenik a **Site Recovery-infrastruktúra** > **VMM-kiszolgálók** panelen.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Az Azure Recovery Services Agent ügynök telepítése a Hyper-V gazdagépekre

1. A Provider beállítását követően töltse le az Azure Recovery Services Agent telepítőfájlját. Végezze el a telepítést a CMM-felhőben futó összes Hyper-V-kiszolgálón.

    ![Hyper-V helyek](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. Az **Előfeltételek ellenőrzése** részben kattintson a **Tovább** gombra. A rendszer automatikusan telepíti a hiányzó előfeltételeket.

    ![Recovery Services Agent, előfeltételek](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. A **Telepítési beállítások** részben fogadja el vagy módosítsa a telepítés, illetve a gyorsítótár helyét. A gyorsítótár már akár 5 GB-os kapacitású meghajtókon is beállítható, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely. Ezt követően kattintson a **Telepítés** gombra.
4. A telepítés befejezését követően kattintson a **Bezárás** gombra.

    ![A MARS Agent regisztrálása](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

### <a name="command-line-installation"></a>Parancssori telepítés
A Microsoft Azure Recovery Services Agent a következő paranccsal telepíthető a parancssorból:

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Internetes proxyn keresztüli elérés beállítása a Site Recoveryhez Hyper-V gazdagépekről

A Hyper-V gazdagépeken futó Recovery Services Agent ügynöknek a virtuális gépek replikálása céljából az interneten keresztül el kell érnie az Azure-t. Ha proxyn keresztül éri el az internetet, adja meg a következő beállításokat:

1. Nyissa meg a Microsoft Azure Backup MMC beépülő modult a Hyper-V gazdagépen. Alapértelmezés szerint a Microsoft Azure Backup parancsikonja az asztalon vagy a következő mappában érhető el: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.
3. A **Proxykonfiguráció** lapon adja meg a proxykiszolgálóra vonatkozó információkat.

    ![A MARS Agent regisztrálása](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. Ellenőrizze, hogy az ügynök el tudja-e érni az [előfeltételek](#on-premises-prerequisites) között megadott URL-címeket.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása
Adja meg a replikációhoz használni kívánt Azure-tárfiókot, valamint az Azure-hálózatot, amelyhez az Azure virtuális gépek a feladatátvételt követően csatlakozhatnak.

1. Kattintson **Az infrastruktúra előkészítése** > **Cél** elemre, és válassza ki az előfizetést és az erőforráscsoportot, ahol a feladatátviteli virtuális gépeket létre szeretné hozni. Kattintson a feladatátvételi virtuális gépekre használni kívánt Azure üzembe helyezési modellre (klasszikus vagy erőforrás-kezelés).

    ![Tárolás](./media/site-recovery-vmm-to-azure/enablerep3.png)

2. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

    ![Tárolás](./media/site-recovery-vmm-to-azure/compatible-storage.png)

3. Ha még nem hozott létre tárfiókot, és ezt szeretné most megtenni a Resource Managerben, kattintson a **+Tárfiók** elemre.  A **Tárfiók létrehozása** panelen adja meg a fiók nevét és típusát, az előfizetést, valamint a helyet. A fióknak és a Recovery Services-tárolónak ugyanazon a helyen kell lennie.

   ![Tárolás](./media/site-recovery-vmm-to-azure/gs-createstorage.png)


   * Ha a klasszikus modellt használó tárfiókot szeretne létrehozni, azt az Azure-portálon teheti meg. [További információ](../storage/storage-create-storage-account-classic-portal.md)
   * Ha Prémium szintű tárfiókot használ a replikált adatok tárolására, a helyszíni adatokban bekövetkező változásokat rögzítő replikálási naplók tárolására be kell állítania egy standard tárfiókot is.
5. Ha még nem hozott létre Azure-hálózatot, és ezt szeretné most megtenni a Resource Managerben, kattintson a **+Hálózat** elemre. A **Virtuális hálózat létrehozása** panelen adja meg a hálózat nevét, a címtartományt, az alhálózati adatokat, az előfizetést, valamint a helyet. A hálózatnak és a Recovery Services-tárolónak ugyanazon a helyen kell lennie.

   ![Network (Hálózat)](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

   Ha a klasszikus modellt használó hálózatot szeretne létrehozni, azt az Azure-portálon teheti meg. [További információk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

* [Itt elolvashatja](#prepare-for-network-mapping) a hálózatleképezés működésének rövid ismertetését.
* Ellenőrizze, hogy a VMM-kiszolgálón futó virtuális gépek csatlakoznak-e a virtuálisgép-hálózathoz, illetve, hogy létrehozott-e legalább egy Azure virtuális hálózatot. Egyetlen Azure-hálózatra több virtuálisgép-hálózatot is le lehet képezni.

Konfigurálja az alábbiak szerint a leképezéseket:

1. A **Site Recovery-infrastruktúra** > **Hálózatleképezések** > **Hálózatleképezés** menüpontban kattintson a **+Hálózatleképezés** ikonra.

    ![Hálózatleképezés](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. A **Hálózatleképezés hozzáadása** részben válassza ki a forrás VMM-kiszolgálót, célként pedig az **Azure-t**.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **Forráshálózat** panelen a VMM-kiszolgálóhoz társítottak listájából válassza ki azt a helyszíni virtuálisgép-hálózatot, amelyet le kíván képezni.
5. A **Célhálózat** panelen válassza ki azt az Azure-hálózatot, amelyre a létrehozott replika Azure virtuális gépek kerülnek. Ezután kattintson az **OK** gombra.

    ![Hálózatleképezés](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Ez történik a hálózatleképezés megkezdésekor:

* A forrás virtuálisgép-hálózatban található meglévő virtuális gépek a leképezés elkezdésekor csatlakoznak a célhálózathoz. A forrás virtuálisgép-hálózathoz csatlakoztatott új virtuális gépek a replikálás végrehajtásakor csatlakoznak a leképezett Azure-hálózathoz.
* Meglévő hálózatleképezés módosítása esetén a replika virtuális gépek az új beállításokkal fognak csatlakozni.
* Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni.
* Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális alhálózatához csatlakozik.

## <a name="configure-replication-settings"></a>Replikációs beállítások konfigurálása
1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.

    ![Network (Hálózat)](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét.
3. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).

    > [!NOTE]
    >  Prémium szintű tárterületre replikálás esetén nem támogatott a 30 másodperces gyakoriság. A korlátozás a Prémium szintű Storage által támogatott blobonkénti pillanatképek számától (100) függ. [További információ](../storage/storage-premium-storage.md#snapshots-and-copy-blob)

4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy hány órás legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A védelemmel ellátott gépeket az időtartamon belüli bármelyik pontra visszaállíthatja.
5. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke 1 és 12 óra között változhat). A Hyper-V két különböző pillanatképtípust használ: az egyik a standard pillanatkép, amely a virtuális gép egészét lefedő növekményes pillanatképet, a másik pedig az alkalmazáskonzisztens pillanatkép, amely a virtuális gépen futó alkalmazások adatairól készült, időponthoz kötött pillanatképet jelent. Az alkalmazáskonzisztens pillanatképek a kötet árnyékmásolása szolgáltatás (VSS) segítségével garantálják, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésének időpontjában. Ne feledje, hogy az alkalmazáskonzisztens pillanatképek bekapcsolása negatív hatással lesz a forrás virtuális gépeken futó alkalmazások teljesítményére. Ügyeljen rá, hogy az itt megadott érték kisebb legyen a további beállított helyreállítási pontok számánál.
6. A **Kezdeti replikáció kezdési ideje** a kezdeti replikáció kezdésének időpontját határozza meg. A replikálási folyamat az internetes sávszélességet használja, így érdemes a műveletet olyankorra ütemezni, amikor kevesen használják az internetet.
7. **Az Azure-on tárolt adatok titkosítása** beállításnál adhatja meg, hogy szeretné-e titkosítani az Azure-tárfiókban elhelyezett inaktív adatokat. Végül kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. Az újonnan létrehozott szabályzatokat a rendszer automatikusan társítja a VMM-felhővel. Kattintson az **OK** gombra. A további VMM-felhőket (és a rajtuk futó virtuális gépeket) a **Replikáció** > szabályzat neve > **VMM-felhő társítása** menüpontban társíthatja az adott replikációs szabályzathoz.

    ![Replikációs szabályzat](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="capacity-planning"></a>Kapacitástervezés

Most, hogy az alapszintű infrastruktúra készen áll, fogjon hozzá a kapacitás megtervezéséhez, és döntse el, hogy szüksége van-e további erőforrásokra.

A Site Recovery Capacity Planner szolgáltatása segít a forráskörnyezethez, a Site Recovery-összetevőkhöz, valamint a hálózathoz és a tárterülethez szükséges erőforrások lefoglalásában. A Planner segédprogram gyors módját használva a virtuális gépek, a lemezek, a tárhely átlagos számán alapuló becsléseket kérhet, vagy használhatja a részletes módot, amelyben a különböző számítási feladatok szintjén Ön adhatja meg a számokat. Előkészületek:

* Gyűjtse össze a replikálási környezetre vonatkozó adatokat, többek között a virtuális gépeket, a virtuális gépekhez tartozó lemezeket, valamint a lemezenkénti tárterületeket.
* Becsülje meg a replikált adatokra vonatkozóan a napi adatváltozás (forgalom) mértékét. Ebben a [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) is segítséget nyújthat.

1. A segédprogram letöltéséhez kattintson a **Download** (Letöltés) gombra, majd futtassa a letöltött fájlt. Olvassa el a segédprogramhoz kapcsolódó [cikket](site-recovery-capacity-planner.md).
2. Ha elkészült, a **Have you run the Capacity Planner?** (Lefuttatta a Capacity Plannert?) kérdésnél válassza a **Yes** (Igen) lehetőséget.

   ![Kapacitástervezés](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

   További információk [a hálózati sávszélesség szabályozásáról](#network-bandwidth-considerations)




## <a name="enable-replication"></a>A replikáció engedélyezése

Mielőtt hozzákezd, győződjön meg róla, hogy az Azure-beli felhasználói fiókja rendelkezik az új virtuális gép Azure-ba replikálásához szükséges [engedélyekkel](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

Most már engedélyezheti a replikációt a következők szerint:

1. Kattintson **2. lépés: Az alkalmazás replikálása** > **Forrás** elemre. A replikálás első alkalommal történő engedélyezését követően kattintson a tárolóban elérhető **+Replikálás** elemre a további gépek replikációjának engedélyezéséhez.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. A **Forrás** panelen válassza ki a VMM-kiszolgálót és a felhőt, amelyben a Hyper-V gazdagépek futnak. Ezután kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. A **Cél** résznél válassza ki az előfizetést, a feladatátvételt követő üzembe helyezési modellt, valamint a replikált adatok tárolására használni kívánt tárfiókot.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. Válassza ki a használni kívánt tárfiókot. Ha a rendelkezésre álló tárfiókok helyett egy másikat szeretne használni, [hozza létre](#set-up-an-azure-storage-account). Ha Prémium szintű Storage-fiókot használ a replikált adatok tárolására, a helyszíni adatokban bekövetkező változásokat rögzítő replikálási naplók tárolására be kell állítania egy Standard Storage-fiókot is. A Storage-fiók Resource Manager-alapú modellel történő létrehozásához kattintson az **Új létrehozása** elemre. Ha a klasszikus modellt használó tárfiókot szeretne létrehozni, azt az [Azure-portálon teheti meg](../storage/storage-create-storage-account-classic-portal.md). Ezután kattintson az **OK** gombra.
5. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre. Ha a rendelkezésre álló hálózatok helyett másikat szeretne használni, [hozza létre](#set-up-an-azure-network). A hálózat Resource Manager-modell használatával történő létrehozásához kattintson az **Új létrehozása** elemre. Ha a klasszikus modellt használó hálózatot szeretne létrehozni, azt az [Azure-portálon](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) teheti meg. Ha szükséges, válassza ki az alhálózatot. Ezután kattintson az **OK** gombra.
6. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben kattintással jelölje ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Végül kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication5.png)

7. A **Tulajdonságok** > **Tulajdonságok konfigurálása** menüben válassza ki a kijelölt virtuális gépek operációs rendszerét, valamint az operációsrendszer-lemezképet.

    - Ellenőrizze, hogy az Azure virtuális gép neve (a cél neve) megfelel-e az [Azure-beli virtuális gépekre vonatkozó követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).   
    - Alapértelmezés szerint a virtuális gép mindegyik lemeze ki van választva replikációra, de bármelyik lemezt törölheti, hogy kihagyja őket a replikálásból.

        - Célszerű egyes lemezeket kihagyni a replikálás sávszélességének csökkentése céljából. Például előfordulhat, hogy nem szeretné replikálni azokat a lemezeket, amelyek az ideiglenes adatokat vagy a gépek vagy alkalmazások minden egyes újraindításakor frissülő adatokat (például pagefile.sys vagy Microsoft SQL Server tempdb) tartalmazzák. Ha ki szeretne zárni egy lemezt a replikációból, törölje a kijelölését.
        - Csak az alaplemezek zárhatók ki. Operációsrendszer-lemezek nem zárhatók ki.
        - Azt javasoljuk, hogy ne zárja ki a dinamikus lemezeket. A Site Recovery nem tudja azonosítani, hogy egy virtuális merevlemez a vendég virtuális gépben alap- vagy dinamikus lemez-e. Ha nem zárja ki az összes függő dinamikus kötet lemezét, a védett dinamikus lemezek hibás lemezekként szerepelnek majd a virtuális gép feladatátadásakor, és a lemezen lévő adatok nem lesznek elérhetők.
        - A replikáció engedélyezése után már nem lehet ahhoz lemezeket hozzáadni, vagy lemezeket eltávolítani belőle. Lemez hozzáadásához vagy eltávolításához le kell tiltania, majd újra engedélyeznie kell a virtuális gép védelmét.
        - Az Azure-ban manuálisan létrehozott lemezek nem vesznek részt a feladatátvételben. Ha például végrehajtja három lemez feladatátvételét, kettőt pedig közvetlenül az Azure virtuális gépen hoz létre, csak a feladatátvételben részt vevő három lemezen lesz végrehajtva az Azure-ból a Hyper-V-re történő feladat-visszavétel. A manuálisan létrehozott lemezek nem vehetők fel a feladat-visszavételbe vagy a Hyper-V-ről az Azure-ba történő visszirányú replikálásba.
        - Ha kizár egy olyan lemezt, amely valamely alkalmazás működéséhez szükséges, az Azure-ba történő feladatátvétel esetén manuálisan létre kell majd hozni azt az Azure-ban, hogy a replikált alkalmazás futtatható legyen. Másik megoldásként integrálhatja az Azure Automationt egy helyreállítási tervbe, hogy a gép feladatátvétele során létrehozza a lemezt.

    Kattintson az **OK** gombra a módosítások mentéséhez. A további tulajdonságokat később is beállíthatja.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)

8. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** menüben válassza ki a védett virtuális gépekre alkalmazni kívánt replikációs beállításokat. Ezután kattintson az **OK** gombra. A replikációs szabályzatot a **Replikációs házirendek** > szabályzat neve > **Beállítások szerkesztése** menüpontban módosíthatja. Az itt megadott módosítások a már replikálás alatt álló, illetve az újonnan hozzáadott gépekre egyaránt érvényesek.

   ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication7.png)

A **Védelem engedélyezése** feladat előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

### <a name="view-and-manage-vm-properties"></a>A virtuális gépek tulajdonságainak megtekintése és kezelése

Javasoljuk, hogy ellenőrizze a forrásgépek tulajdonságait. Ne feledje, hogy az Azure virtuális gép nevének meg kell felelnie az [Azure virtuális gépekre vonatkozó követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. A **Védett elemek** résznél kattintson a **Replikált elemek** elemre, majd a részletek megtekintéséhez válassza ki a kívánt gépet.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. A **Számítás és hálózat** > **Számítási tulajdonságok** résznél adhatja meg az Azure virtuális gép nevét és a cél méretét. Ha szükséges, írja át úgy a nevet, hogy az megfeleljen az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). Ezenfelül itt a célhálózatra, az alhálózatra, valamint az Azure virtuális géphez rendelt IP-címre vonatkozó információk is megtekinthetők.
Vegye figyelembe:

   * A cél IP-címe beállítható. Ha nem ad meg címet, a gép, amelynek a feladatait átadja, a DHCP-t fogja használni. Ha olyan címet ad meg, amely nem használható feladatátadásra, a feladatátvételi művelet sikertelen lesz. A cél IP-címe feladatátvételi tesztre is használható, amennyiben a cím elérhető a feladatátvételi teszt hálózatában.
   * A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:

     * Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
     * Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot használja.
     * Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a célgép mérete csupán egy adaptert támogat, akkor a célgépen csak egy adapter fog működni.     
     * Ha a virtuális gépen több hálózati adapter működik, azok mindegyike ugyanahhoz a hálózathoz fog csatlakozni.

     ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/test-failover4.png)

4. A **Lemezek** résznél láthatja a replikálandó virtuális gépekhez tartozó operációsrendszer- és adatlemezeket.

#### <a name="managed-disks"></a>Felügyelt lemezek

A **Számítás és hálózat** > **Számítási tulajdonságok** részben válassza a „Felügyelt lemezek használata” területen az „Igen” lehetőséget a virtuális gépre vonatkozóan, ha szeretne felügyelt lemezeket csatolni a gépéhez az Azure-ba való migráláskor. A felügyelt lemezek leegyszerűsítik az Azure IaaS virtuális gépek lemezeinek kezelését a virtuális gép lemezeihez társított Storage-fiókok kezelésével. [További információk a felügyelt lemezekről](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview).

   - Felügyelt lemezek csak az Azure-ba történő feladatátvételkor jönnek létre és kapcsolódnak a virtuális géphez. Védelem engedélyezésekor a helyszíni gépek adatainak a Storage-fiókokba replikálása folytatódik.
   Felügyelt lemezeket csak a Resource Manager-alapú üzemi modellel üzembe helyezett virtuális gépeken lehet létrehozni.  

  > [!NOTE]
  > A felügyelt lemezekkel rendelkező gépek esetén jelenleg nem támogatott az Azure-ból a helyszíni Hyper-V-környezetbe történő feladat-visszavétel. Válassza a „Felügyelt lemezek használata” területen az „Igen” lehetőséget, ha az Azure-ba kívánja migrálni ezt a gépet.

   - Amikor a „Felügyelt lemezek használata” területen az „Igen” lehetőséget választja, az erőforráscsoportnak csak azok a rendelkezésre állási csoportjai lesznek elérhetőek a kiválasztásra, amelyeknél a „Felügyelt lemezek használata” „Igen” értékűre van állítva. Ennek az az oka, hogy a felügyelt lemezekkel rendelkező virtuális gépek csak olyan rendelkezésre állási csoportoknak lehetnek részei, amelyek esetén a „Felügyelt lemezek használata” tulajdonság „Igen” értékűre van állítva. Győződjön meg róla, hogy a rendelkezésre állási csoportokat olyan „Felügyelt lemezek használata” tulajdonságbeállítással hozza létre, ahogyan a feladatátvételkor a felügyelt lemezeket használni kívánja.  Hasonlóképpen, amikor a „Felügyelt lemezek használata” területen a „Nem” lehetőséget választja, az erőforráscsoportnak csak azok a rendelkezésre állási csoportjai lesznek elérhetőek kiválasztásra, amelyeknél a „Felügyelt lemezek használata” tulajdonság „Nem” értékűre van állítva. [További információk a felügyelt lemezekről és a rendelkezésre állási csoportokról](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).

  > [!NOTE]
  > Ha a replikálásra használt Storage-fiókot bármikor a Storage Service Encryptionnel titkosították, a felügyelt lemezek létrehozása sikertelen lesz a feladatátvételkor. Vagy állítsa a „Felügyelt lemezek használatát” „Nem” értékűre, és próbálja meg újra a feladatátvételt, vagy tiltsa le a virtuális gép védelmét, és védje azt egy olyan Storage-fiókkal, amely esetében soha nem volt engedélyezve a Storage Service Encryption.
  > [További információk a Storage Service Encryptionről és a felügyelt lemezekről](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

Az üzemelő példány kipróbálásához futtasson feladatátvételi tesztet egy virtuális gépen, vagy alkalmazzon egy vagy több virtuális gépet tartalmazó helyreállítási tervet.

### <a name="before-you-start"></a>Előkészületek

 - Ha a feladatátvételt követően RDP segítségével szeretne kapcsolódni az Azure-beli virtuális gépekhez, tudjon meg többet[a kapcsolódásra való előkészülésről](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - A teljes teszteléshez szüksége lesz az Active Directory és a DNS egy másolatára a tesztkörnyezetében. [További információk](site-recovery-active-directory.md#test-failover-considerations).

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. A **Replikált elemek** listáján szereplő egyik virtuális gép feladatainak átadásához kattintson a virtuális gépre, majd a **+Feladatátvételi teszt** elemre.
2. Helyreállítási terv feladatátadásához a **Helyreállítási tervek** menüben kattintson a jobb gombbal a kívánt tervre, majd válassza a **Feladatátvételi teszt** lehetőséget. Helyreállítási terv létrehozásához [kövesse ezeket az utasításokat](site-recovery-create-recovery-plans.md).
3. A **Feladatátvételi teszt** résznél válassza ki, hogy az Azure virtuális gépek melyik Azure-hálózathoz csatlakozzanak a feladatátvételt követően.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A folyamat előrehaladásának megtekintéséhez kattintson a virtuális gépre és nyissa meg a tulajdonságait, vagy a **Site Recovery-feladatok** menüben kísérje figyelemmel a **Feladatátvételi teszt** feladat állapotát.
5. A feladatátvétel befejezését követően a replika Azure-gépnek meg kell jelennie az Azure Portal > **Virtuális gépek** részében. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, és fut-e.
6. Ha elvégezte a feladatátvételt követő csatlakozáshoz szükséges előkészületeket, most tudnia kellene csatlakozni az Azure-beli virtuális géphez.
7. Miután elkészült, a helyreállítási terven kattintson a **Feladatátvételi teszt eltávolítása** elemre. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket. Ezzel törli a feladatátvételi teszt során létrehozott virtuális gépeket.

További részletekért tekintse meg az [Azure-ba irányuló feladatátvételi teszttel kapcsolatos](site-recovery-test-failover-to-azure.md) cikket.

## <a name="monitor-the-deployment"></a>A telepítés monitorozása

Az üzemelő Site Recovery-példány konfigurációs beállításainak és állapotának monitorozásához tegye a következőket:

1. Kattintson a tároló nevére az **Alapvető erőforrások** irányítópult megnyitásához. Itt megtalálja a Site Recovery-feladatokat, a replikációs állapotot, a replikálási terveket, a kiszolgáló állapotát, valamint az eseményeket.  Az **Alapvető erőforrások** irányítópultot testre szabva beállíthatja, hogy az Önnek legfontosabb csempék és elrendezések jelenjenek meg, így akár más Site Recovery- és Backup-tárolók állapotát is gyorsan megtekintheti.

    ![Alapvető erőforrások](./media/site-recovery-vmm-to-azure/essentials.png)
2. Az **Állapot** csempén nyomon követheti a helyszíni kiszolgálók (VMM- vagy konfigurációs kiszolgálók) problémáit, illetve megtekintheti a Site Recovery által az elmúlt 24 órában rögzített eseményeket.
3. A replikációt a **Replikált elemek**, a **Helyreállítási tervek** és a **Site Recovery-feladatok** csempéken felügyelheti és figyelheti. A feladatokat részletesen is megtekintheti a **Feladatok** > **Site Recovery-feladatok** menüpontban.

## <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Az Azure Site Recovery Provider telepítése a parancssorból

Az Azure Site Recovery Provider a parancssorból is telepíthető. A Provider ezzel a módszerrel a Server Core for Windows Server 2012 R2 kiadáson is telepíthető.

1. Töltse le egy mappába a Provider telepítőfájlját és a regisztrációs kulcsot. A mappa lehet például a következő: C:\ASR.
2. Rendszergazda jogú parancssorból futtassa az alábbi parancsokat a Provider telepítőjének kibontásához:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Az összetevők telepítéséhez futtassa ezt a parancsot:

            C:\ASR> setupdr.exe /i
4. Majd az alábbi parancsok futtatásával regisztrálja a kiszolgálót a tárolóban:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Az elemek magyarázata:

* **/Credentials**: kötelező paraméter, amely a regisztrációs kulcsfájl helyét határozza meg.  
* **/FriendlyName**: kötelező paraméter, amely a Hyper-V gazdakiszolgáló nevét tartalmazza úgy, ahogy az Azure Site Recovery portálon megjelenik.
* * **/EncryptionEnabled**: nem kötelező paraméter arra az esetre, ha VMM-felhőkben futó Hyper-V virtuális gépeket replikál az Azure-ba. Adja meg, hogy szeretné-e titkosítani a virtuális gépeket az Azure-ban (inaktív adatok titkosítása). A fájlnak **.pfx** kiterjesztésűnek kell lennie. A titkosítás alapértelmezés szerint ki van kapcsolva.
* **/proxyAddress**: nem kötelező paraméter, amely a proxykiszolgáló címét határozza meg.
* **/proxyport**: nem kötelező paraméter, amely a proxykiszolgáló portját határozza meg.
* **/proxyUsername**: nem kötelező paraméter, amely a proxy felhasználónevét határozza meg (ha a proxy hitelesítést igényel).
* **/proxyPassword**: nem kötelező paraméter, amely a proxykiszolgáló hitelesítéséhez szükséges jelszavát határozza meg (ha a proxy hitelesítést igényel).


### <a name="network-bandwidth-considerations"></a>Hálózati sávszélességgel kapcsolatos szempontok
A Capacity Planner segítségével a replikáláshoz (a kezdeti replikáláshoz, majd a változások szinkronizálásához) szükséges sávszélességet is kiszámíthatja. A replikáláshoz felhasznált sávszélesség mértékének szabályozására a következő lehetőségek állnak rendelkezésre:

* **Throttle bandwidth** (Sávszélesség szabályozása): a másodlagos helyre replikációt végző Hyper-V-forgalom egy konkrét Hyper-V-gazdagépen halad keresztül. A gazdakiszolgálón beállíthatja a sávszélesség szabályozását.
* **Tweak bandwidth** (Sávszélesség finomhangolása): beállításkulcsok segítségével is szabályozhatja a replikáláshoz használt sávszélességet.

#### <a name="throttle-bandwidth"></a>Sávszélesség szabályozása
1. Nyissa meg a Microsoft Azure Backup MMC beépülő modult a Hyper-V gazdakiszolgálón. Alapértelmezés szerint a Microsoft Azure Backup parancsikonja az asztalon vagy a következő mappában érhető el: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.
3. A **Szabályozás** lapon jelölje be **A biztonsági mentési műveletek internetes sávszélességének szabályozása** jelölőnégyzetet, majd állítsa be a munkaórákra és a munkaórákon kívüli időre érvényes korlátozást. 512 kB/s és 102 MB/s közötti értéket adhat meg.

    ![Sávszélesség szabályozása](./media/site-recovery-vmm-to-azure/throttle2.png)

A szabályozáshoz a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) parancsmag is használható. Íme egy példa:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

A **Set-OBMachineSetting -NoThrottle** beállítás azt jelenti, hogy nincs szükség szabályozásra.

#### <a name="influence-network-bandwidth"></a>A hálózati sávszélesség szabályozása
Az **UploadThreadsPerVM** beállításazonosító szabályozza a lemezen adatátvitelre (kezdeti vagy változásreplikálásra) használt szálak számát. Minél magasabb a beállítás értéke, annál nagyobb hálózati sávszélességet használ a rendszer a replikáláshoz. A **DownloadThreadsPerVM** beállításazonosító határozza meg, hogy a rendszer hány szálat használ az adatátvitelhez a feladatátvétel során.

1. Lépjen a beállításjegyzék következő helyére: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

   * Az **UploadThreadsPerVM** beállítás értékének módosításával (vagy ha nem létezik, létrehozásával) szabályozhatja a lemezek replikálásához használt szálak számát.
   * A **DownloadThreadsPerVM** beállítás értékének módosításával (vagy ha nem létezik, létrehozásával) szabályozhatja az Azure felől érkező feladatátvételi forgalomhoz használt szálak számát.
2. Az alapértelmezett érték a 4. A szükségesnél több erőforrással ellátott hálózatban érdemes módosítani a beállításazonosítók alapértelmezett értékét. A maximális érték a 32. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.

## <a name="next-steps"></a>Következő lépések

Miután a kezdeti replikálás befejeződött, és letesztelte az üzemelő példányt, szükség szerint indíthat feladatátvételeket. [További információk](site-recovery-failover.md) a feladatátvételek különféle típusaival és a futtatásukkal kapcsolatban.

