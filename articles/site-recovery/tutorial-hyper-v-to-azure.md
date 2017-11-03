---
title: "A helyszíni Hyper-V virtuális gépek Azure-bA az Azure Site Recovery vész-helyreállítási beállítása |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthatja be a vész-helyreállítási helyszíni Hyper-V virtuális gépek Azure-bA az Azure Site Recovery szolgáltatással."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 4d43fb03ce1c54a47315b8c3a5c83ec2082bcab9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Az Azure-bA helyszíni Hyper-V virtuális gépek vész-helyreállítási beállítása

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás vész-helyreállítási stratégiát infrastruktúrája azzal segíti a kezelése és koordinálása replikációjának, feladatátvételének és feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

Az oktatóanyag bemutatja, hogyan állíthat be az Azure-bA helyszíni Hyper-V virtuális gépek vész-helyreállítási. Az oktatóanyag a System Center Virtual Machine Manager (VMM) felhőkben felügyelt Hyper-V virtuális gépeket, és nem megfelelő. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be az Azure és a helyszíni Előfeltételek
> * A Site Recovery Recovery Services-tároló létrehozása 
> * Állítsa be a forrás és cél replikációs környezetekben 
> * A hálózatleképezéseket (Ha a System Center VMM által felügyelt Hyper-V)
> * Replikációs házirend létrehozása
> * A virtuális gépek replikálásának engedélyezése


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Győződjön meg arról, hogy tudomásul veszi a [forgatókönyv architektúrája és összetevői](concepts-hyper-v-to-azure-architecture.md).
- Tekintse át a [igényeinek támogatására](site-recovery-support-matrix-to-azure.md) lévő valamennyi összetevőnél.
- Győződjön meg arról, hogy replikálni kívánt virtuális gépeket ahhoz [Azure virtuális gép](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Állapítsa meg a kapacitástervezés:
    - Használja a [Hyper-V kapacitás Planner eszköz](http://aka.ms/asr-capacity-planner-excel)hátra a változási sebessége.
    - Használja a [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) elemzése a forráskörnyezettel, felbecsülni a sávszélesség, és a cél követelmények.
- Azure előkészítése. Azure-előfizetéssel, Azure virtuális hálózat és a storage-fiók szükséges.
- Készítse elő a helyszíni Hyper-V-gazdagépek és VMM-kiszolgáló számára, ha szükséges.





### <a name="set-up-an-azure-account"></a>Az Azure-fiók beállítása

Egy Microsoft beolvasása [Azure-fiók](http://azure.microsoft.com/).

- Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
- További tudnivalók [Site Recovery díjszabásáról](site-recovery-faq.md#pricing), és [díjszabása](https://azure.microsoft.com/pricing/details/site-recovery/).
- Annak megállapítása, amely [régiókban támogatott](https://azure.microsoft.com/pricing/details/site-recovery/) hely helyreállításához.

### <a name="verify-azure-account-permissions"></a>Azure-fiók szükséges engedélyek ellenőrzése

Ellenőrizze, hogy az Azure-fiókjával a virtuális gépek replikálása kell engedélyekkel rendelkezik.

- Tekintse át a [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) kell replikálni a gépeket az Azure-bA
- Ellenőrizze és módosítsa [szerepkörön alapuló hozzáférés](../active-directory/role-based-access-control-configure.md) engedélyek. 


### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Állítson be egy [Azure hálózati](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- Azure virtuális gépek kerülnek ehhez a hálózathoz, ha feladatátvételt követően jönnek létre.
- A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.


### <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

Állítson be egy [Azure storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- A Site Recovery a helyszíni gépeket replikál az Azure storage. Azure virtuális gépek jönnek létre a tárból, feladatátvételt követően.
- A tárfiók és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
- A tárfiók lehet standard vagy [prémium](../virtual-machines/windows/premium-storage.md).
- Prémium szintű fiók beállítása, ha szüksége egy további standard fiók naplóadatait.

### <a name="prepare-hyper-v-hosts"></a>Felkészülés a Hyper-V-gazdagépek

1. Győződjön meg arról, hogy a Hyper-V gazdagépek megfelelnek [igényeinek támogatására](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
2. Győződjön meg arról, hogy a gazdagépek hozzáférhet az URL-címek:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Bármely IP-címeken alapuló tűzfalszabályok szabályokat engedélyezni kell az Azure-kommunikációt.
    - Engedélyezze az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).
    - Az IP-címtartományok az előfizetés az Azure-régió, valamint a (hozzáférés-vezérlés és identitás kezelésre szolgáló) USA nyugati régiója engedélyezése.

### <a name="prepare-vmm-servers"></a>VMM-kiszolgáló előkészítése

Ha Hyper-V-gazdagépek a VMM kezeli, akkor készítse elő a helyszíni VMM-kiszolgálón. 

- Győződjön meg arról, hogy megfelel-e a VMM-kiszolgáló [igényeinek támogatására](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Győződjön meg arról, hogy a VMM-kiszolgáló legalább egy felhőben, egy vagy több gazdagépcsoportot van. A Hyper-V gazdagépet, amelyen a virtuális gépek futnak a felhőben található kell lennie.
- A VMM-kiszolgálót kell az internet-hozzáféréssel, aki hozzáféréssel rendelkezik a következő URL-címek:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Bármely IP-címeken alapuló tűzfalszabályok szabályokat engedélyezni kell az Azure-kommunikációt.
    - Engedélyezze az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).
    - Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).
- A hálózatleképezés előkészítése a VMM-kiszolgáló.


#### <a name="prepare-vmm-for-network-mapping"></a>A VMM hálózatleképezés előkészítése

Használata esetén a VMM-ben [hálózatleképezés](site-recovery-network-mapping.md) helyszíni VMM-Virtuálisgép-hálózatok és az Azure virtuális hálózatok közötti leképezéseket. Leképezési biztosítja az Azure virtuális gépek csatlakoznak a megfelelő hálózati, ha a feladatátvételt követően jönnek létre.

Készítse elő a VMM a hálózatleképezés az alábbiak szerint:

1. Győződjön meg arról, hogy egy [VMM logikai hálózata](https://docs.microsoft.com/system-center/vmm/network-logical) , amelyhez társítva a felhőben, amelyben a Hyper-V-gazdagépek találhatók.
2. Ellenőrizze, hogy egy [Virtuálisgép-hálózat](https://docs.microsoft.com/system-center/vmm/network-virtual) a logikai hálózathoz csatolva.
3. A virtuális gépek csatlakozni a Virtuálisgép-hálózatot.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>Válassza ki a védelmi cél

Kiválasztása replikálja, valamint replikálni úgy, hogy a helyét.

1. Kattintson a tárolóban lévő **Site Recovery** > **infrastruktúra előkészítése** > **védelmi cél**.
2. A **védelmi cél**, jelölje be **az Azure-bA**> **Igen, a Hyper-V-vel**. 
    - Ha a Hyper-V-gazdagépek a VMM nem felügyeli, válassza ki a **nem** annak ellenőrzéséhez, hogy a VMM nem használja.
    - Ha a VMM-felhőkben felügyelt gazdagépek, válassza ki a **Igen**.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezettel beállításakor az Azure Site Recovery Provider és az Azure Recovery Services Agent ügynök telepítése, és regisztrálja a helyszíni kiszolgálók a tárolóban. 

1. A **infrastruktúra előkészítése**, kattintson a **forrás**. 
    - Ha a VMM nem használja, kattintson a **+ Hyper-V hely**, és adjon meg egy helynevet. Kattintson a **+ Hyper-V Server**, és a helyhez hozzáadásra állomáshoz vagy fürthöz.
    - Ha használ a VMM-ben **forrás előkészítése**, kattintson a **+ VMM** hozzáadása a VMM-kiszolgáló. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **System Center VMM-kiszolgáló** megjelenik **kiszolgálótípus**.
2. Töltse le a Provider és agent összetevők, attól függően, hogy a környezetében.
    - A Hyper-V csak töltse le a Provider telepítőfájlját. A fájl a szolgáltató és a az ügynök telepítése minden egyes Hyper-V gazdagépen futtatja.
        
        ![Szolgáltató VMM nélkül](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - A Hyper-V, a VMM-mel töltse le a Provider és agent külön-külön. Futtassa a Provider telepítése a VMM-kiszolgálón. Futtassa az ügynök telepítése minden Hyper-V gazdagépen.
    
        ![Szolgáltató és a VMM-ügynök](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. Töltse le a tárolóregisztrációs kulcsot. Ez szükséges a szolgáltató telepítése futtatásakor. A kulcs a generálásától számított öt napig érvényes.

### <a name="install-components"></a>Összetevőinek telepítése

Az összetevők telepítése summmarized a táblában. 

**Összetevő** | **Részletek** | **A Hyper-V csak** | **A Hyper-V a VMM-mel**
--- | --- | --- | ---
**Az Azure Site Recovery Providert** | Koordinálja a replikálás az Azure-bA | Minden Hyper-V gazdagép telepítése | Telepítse a VMM-kiszolgálón
**Recovery Services Agent ügynök** | Kezeli az adatreplikáció | Minden Hyper-V gazdagép telepítése | Telepítse a Hyper-V gazdagépen


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>Telepítse a szolgáltatót a Hyper-V VMM nélkül

Telepítse a szolgáltató minden Hyper-V gazdagépen felvette a Hyper-V helyet. Hyper-V fürt telepítése, futtassa a telepítőt a fürt minden csomópontján. Telepítése és regisztrálása a Hyper-V fürt minden csomópontján biztosítja a virtuális gépek védelmének biztosításához, akkor is, ha az áttelepítés után csomópontjai között.

1. A **Microsoft Update** lapon kérheti a frissítések beszerzését, így a rendszer a Microsoft Update-szabályzatnak megfelelően telepíteni fogja a Providerhez kiadott frissítéseket.
2. A **Telepítés** lapon tetszés szerint fogadja el vagy módosítsa a Provider alapértelmezett telepítési helyét, majd kattintson a **Telepítés** gombra.
4. A **tároló beállításait**, kattintson a **Tallózás** jelölje be a tároló kulcsfájlját letöltött. Adja meg az Azure Site Recovery-előfizetést, a tároló neve és a Hyper-V hely, amelyhez a Hyper-V kiszolgáló tartozik.
5. A **proxybeállítások**, adja meg, hogyan csatlakozzon a Provider a VMM-kiszolgáló vagy Hyper-V gazdagépen futó Site Recovery az interneten keresztül.
    * Közvetlen kapcsolatot, válasszon **közvetlen kapcsolódás proxy nélkül Azure Site Recovery**.
    * Válassza a proxy, **csatlakozás az Azure Site Recovery proxykiszolgálóval**. Szükség esetén adja meg a proxykiszolgáló címét, a port és a hitelesítő adatait.
6. Miután a kiszolgáló regisztrálva van a tárolóban, kattintson **Befejezés**.

Azure Site Recovery lekéri a Hyper-V kiszolgálóról metaadatok, és a kiszolgáló megjelenik **Site Recovery-infrastruktúra** > **Hyper-V-gazdagépek**.


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>A Recovery Services agent telepítése a Hyper-V gazdagép VMM nélkül

A VMM a központi telepítés használata, futtassa a Recovery Services agent telepítő minden Hyper-V gazdagépen.

1. A telepítővarázsló > **szükséges előfeltételek ellenőrzése**, kattintson a **következő**. A rendszer automatikusan telepíti a hiányzó előfeltételeket.

    ![Recovery Services Agent, előfeltételek](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. A **Telepítési beállítások** részben fogadja el vagy módosítsa a telepítés, illetve a gyorsítótár helyét. A gyorsítótár-meghajtón kell legalább 5 GB tárhelyet. Azt javasoljuk, hogy olyan meghajtót, amelyen legalább 600 GB szabad terület. Ezt követően kattintson a **Telepítés** gombra.
4. A **telepítési**, a telepítés befejezését követően, kattintson a **Bezárás** a varázsló befejezéséhez.

##### <a name="set-up-internet-access-via-a-proxy"></a>Egy proxyn keresztül történő internet-hozzáférés beállítása

A Recovery Services Agent ügynököt a Hyper-V gazdagépen internet-hozzáférés az Azure virtuális gép replikációs szüksége van. Ha proxyn keresztül éri el az internetet, adja meg a következő beállításokat:

1. Nyissa meg a Microsoft Azure Backup MMC beépülő modult a Hyper-V gazdagépen. Alapértelmezés szerint a Microsoft Azure Backup parancsikonja áll rendelkezésre, az asztalon, vagy a C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.
3. Az a **proxykonfigurációt** lapra, adja meg a proxy adatait.

    ![A MARS Agent regisztrálása](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. Ellenőrizze, hogy az ügynök el lehet érni a [szükséges URL-címek](#prepare-hyper-v-hosts).

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>Telepítse a szolgáltatót a VMM-kiszolgálón (Hyper-V a VMM-mel)

1. A **Microsoft Update** lapon kérheti a frissítések beszerzését, így a rendszer a Microsoft Update-szabályzatnak megfelelően telepíteni fogja a Providerhez kiadott frissítéseket.
2. A **Telepítés** lapon tetszés szerint fogadja el vagy módosítsa a Provider alapértelmezett telepítési helyét, majd kattintson a **Telepítés** gombra. 
3. Most regisztrálja a VMM-kiszolgálót a tárolóban lévő állapottal. A **tároló beállításait**, kattintson a **Tallózás** jelölje be a tároló kulcsfájlját letöltött. Adja meg az Azure Site Recovery-előfizetést, és a tároló nevét.

    ![Kiszolgáló regisztrációja](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. A **proxybeállítások**, adja meg, hogyan csatlakozzon a Provider a VMM-kiszolgáló vagy Hyper-V gazdagépen futó Site Recovery az interneten keresztül.

    * Közvetlen kapcsolatot, válasszon **közvetlen kapcsolódás proxy nélkül Azure Site Recovery**.
    * Válassza a proxy, **csatlakozás az Azure Site Recovery proxykiszolgálóval**. Szükség esetén adja meg a proxykiszolgáló címét, a port és a hitelesítő adatait.
    - A VMM RunAs-fiókot (DRAProxyAccount) jön létre automatikusan a megadott proxy hitelesítő adatok használatával. Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A RunAs fiók beállításait módosíthatja a VMM-konzol > **beállítások** > **biztonsági** > **futtató fiókok**. Indítsa újra a VMM szolgáltatást módosításainak frissítésére.
5. A **adattitkosítás**, adja meg, hogy az Azure-bA küldött összes adat titkosítva. Ha ezt a beállítást a Site Recovery kiadják a tanúsítványokat. Ez a tanúsítvány vissza kell fejtenie az adatokat később lesz szüksége.
6. A **VMM-kiszolgáló**, adjon meg egy rövid nevet a tárolóban lévő VMM-kiszolgáló azonosítására szolgál. Fürtkonfiguráció használata esetén adja meg a VMM-fürtszerepkör nevét. A **szinkronizálás metaadatainak szinkronizálása a tárolóval**, adja meg, hogy a VMM-kiszolgálón futó összes felhő metaadatait szinkronizálni a tárolóval szeretné. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem kívánja az összes felhő szinkronizálásához, hagyja bejelölve ezt a beállítást, és szinkronizálja egyenként a felhő tulajdonságai a VMM-konzolon.

Regisztráció befejezése után az Azure Site Recovery lekéri a kiszolgálóról metaadatok, és a VMM-kiszolgáló megjelenik **Site Recovery-infrastruktúra**.






## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki, és ellenőrizze a tároló erőforrásait. 

1. Kattintson a **infrastruktúra előkészítése** > **cél**.
2. Válassza ki az előfizetés és az erőforráscsoport, amelyben az Azure virtuális gépek a feladatátvételt követően létrejön. Válassza ki a virtuális gépek Azure-ban használni kívánt telepítési modelljét.

3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="configure-network-mapping-with-vmm"></a>(A VMM-mel) hálózatleképezés konfigurálása

Ha VMM használata esetén állítsa be a hálózatra való leképezés.

1. A **Site Recovery-infrastruktúra** > **Hálózatleképezések** > **Hálózatleképezés** menüpontban kattintson a **+Hálózatleképezés** ikonra.
2. A **hálózatleképezés hozzáadása**, válassza ki a forrás VMM-kiszolgálón. Válassza ki **Azure** céljaként.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **Forráshálózat**, válassza ki a forrás helyi Virtuálisgép-hálózatot.
5. A **célhálózat**, válassza ki az Azure-hálózatot, mely replika Azure virtuális gépek kerülnek, ha a feladatátvételt követően jönnek létre. Ezután kattintson az **OK** gombra.

    ![Hálózatleképezés](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Kattintson a **infrastruktúra előkészítése** > **replikációs beállítások** > **+ létrehozás és társítás**.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét.
3. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).

    > [!NOTE]
    >  Prémium szintű tárterületre replikálás esetén nem támogatott a 30 másodperces gyakoriság. A korlátozás a Prémium szintű Storage által támogatott blobonkénti pillanatképek számától (100) függ. [További információk](../virtual-machines/windows/premium-storage.md#snapshots-and-copy-blob).

4. A **helyreállításipont-megőrzést**, adja meg, hogy mennyi ideig az adatmegőrzési időtartam lesznek (órákban) az egyes helyreállítási pontok. A védelemmel ellátott gépeket az időtartamon belüli bármelyik pontra visszaállíthatja.
5. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke 1 és 12 óra között változhat). Hyper-V két különböző használja:
    - **Standard pillanatkép**: lefedő növekményes pillanatképet a teljes virtuális gép.
    - **Alkalmazáskonzisztens pillanatkép**: pont időponthoz kötött pillanatképet készít a virtuális Gépen belül az alkalmazásadatok. Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a alkalmazások konzisztens állapotban legyenek, a pillanatkép készítésének időpontjában. Alkalmazáskonzisztens pillanatképeket engedélyezése hatással van a forrás virtuális gépek alkalmazás teljesítménye. Állítson be egy, amely kisebb, mint a további helyreállítási pontok száma.
6. A **Kezdeti replikáció kezdési ideje** a kezdeti replikáció kezdésének időpontját határozza meg. Replikáció az internetes sávszélességet, érdemes ütemezni, amikor a csúcsidőszakon kívül.
7. **Az Azure-on tárolt adatok titkosítása** beállításnál adhatja meg, hogy szeretné-e titkosítani az Azure-tárfiókban elhelyezett inaktív adatokat. Végül kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/tutorial-hyper-v-to-azure/replication-policy.png)


Ha egy új házirendet hoz létre, automatikusan társítja a VMM-felhő, vagy a Hyper-V helyet.

## <a name="enable-replication"></a>A replikáció engedélyezése


1. Kattintson a **alkalmazás replikálása** > **forrás**. 
2. A **forrás**, válassza ki a Hyper-V-hely/VMM-kiszolgáló/felhő. Ezután kattintson az **OK** gombra.
3. A **cél**, Azure ellenőrizze a cél, a tároló előfizetés és a feladatátvétel után az Azure-ban használni kívánt modell.
4. Válassza ki a tárfiókot, a replikált adatok és az Azure-hálózatot, amelyben Azure virtuális gépeken található feladatátvételt követően.
5. A **virtuális gépek** > **válasszon**, válassza ki a replikálni kívánt virtuális gépeket. Ezután kattintson az **OK** gombra.

 Előrehaladásának nyomon követheti a **Védelemengedélyezési** műveletét **feladatok** > **Site Recovery-feladatok**. Miután a **Védelemvéglegesítési** feladat befejeződik, a kezdeti replikálás befejeződik, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>Következő lépések
[Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
