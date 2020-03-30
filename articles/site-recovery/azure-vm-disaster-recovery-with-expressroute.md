---
title: Az Azure ExpressRoute Azure virtuális gép vészhelyreállítási szolgáltatásának integrálása az Azure Site Recovery szolgáltatással
description: A vészhelyreállítás beállítása az Azure Site Recovery és az Azure ExpressRoute használatával
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bf12a5b7850a56d945e1082be6c522c31738669c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954084"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Az ExpressRoute integrálása vészhelyreállítással az Azure virtuális gépekhez


Ez a cikk bemutatja, hogyan integrálhatja az Azure ExpressRoute-ot az [Azure Site Recovery](site-recovery-overview.md)szolgáltatással, amikor vészhelyreállítást állít be az Azure-beli virtuális gépekhez egy másodlagos Azure-régióba.

A Site Recovery lehetővé teszi az Azure virtuális gépek vészutáni helyreállítását az Azure virtuális gép adatainak az Azure-ba történő replikálásával.

- Ha az Azure virtuális gépek [Azure felügyelt lemezeket](../virtual-machines/windows/managed-disks-overview.md)használnak, a virtuálisgép-adatok replikálódnak egy replikált felügyelt lemezre a másodlagos régióban.
- Ha az Azure virtuális gépek nem használnak felügyelt lemezeket, a virtuálisgép-adatok replikálása egy Azure-tárfiókba.
- A replikációs végpontok nyilvánosak, de az Azure virtuális gépek replikációs forgalma nem lépi át az internetet.

Az ExpressRoute lehetővé teszi, hogy a helyszíni hálózatokat privát kapcsolaton keresztül bővítse ki a Microsoft Azure-felhőbe, amelyet egy kapcsolódási szolgáltató is elősegít. Ha konfigurálta az ExpressRoute-ot, az a következőképpen integrálható a Site Recovery alkalmazással:

- **Az Azure-régiók közötti replikáció során:** Az Azure virtuális gép vész-helyreállítási replikációs forgalma csak az Azure-on belül van, és az ExpressRoute nem szükséges, és replikációra nem használható. Ha azonban egy helyszíni helyről csatlakozik az Azure-beli virtuális gépekhez az elsődleges Azure-helyen, számos olyan probléma merül fel, amelyeket figyelembe kell venni, amikor az Azure-beli virtuális gépek vész-helyreállítási beállításakor.
- **Feladatátvétel az Azure-régiók között:** Kimaradások esetén az elsődleges és a másodlagos Azure-régióbeli Azure-virtuális gépek feladatátvételt. Miután egy másodlagos régió, számos lépést kell tennie annak érdekében, hogy az ExpressRoute használatával az Azure-beli virtuális gépek a másodlagos régióban.


## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, győződjön meg róla, hogy megértette a következő fogalmakat:

- [ExpressRoute-áramkörök](../expressroute/expressroute-circuit-peerings.md)
- [ExpressRoute-útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- [ExpressRoute-helyek](../expressroute/expressroute-locations.md).
- Az Azure virtuális gép [replikációs architektúrája](azure-to-azure-architecture.md)
- Az Azure-beli virtuális gépek [replikációjának beállítása.](azure-to-azure-tutorial-enable-replication.md)
- Az Azure-beli virtuális gépek [feladatátvétele.](azure-to-azure-tutorial-failover-failback.md)


## <a name="general-recommendations"></a>Általános ajánlások

Az ajánlott eljárás érdekében, valamint a vész-helyreállítási hatékony helyreállítási idő célok (RtOs) biztosítása érdekében javasoljuk, hogy a Site Recovery beállításakor az ExpressRoute-ba integrálva tegye a következőket:

- Hálózati összetevők kiépítése a feladatátvétel előtt egy másodlagos régióba:
    - Ha engedélyezi az Azure virtuális gépek replikációját, a Site Recovery automatikusan telepítheti a hálózati erőforrásokat, például a hálózatokat, az alhálózatokat és az átjárókat a cél Azure-régióban a forráshálózati beállítások alapján.
    - A Site Recovery nem tudja automatikusan beállítani a hálózati erőforrásokat, például a virtuális hálózatok átjáróit.
    - Azt javasoljuk, hogy ezeket a további hálózati erőforrásokat a feladatátvétel előtt. Egy kis állásidő van társítva ehhez a központi telepítéshez, és hatással lehet a teljes helyreállítási idő, ha nem veszi figyelembe a központi telepítés tervezése során.
- Rendszeres vész-helyreállítási gyakorlatok futtatása:
    - A próba adatveszteség és állásidő nélkül ellenőrzi a replikációs stratégiáját, és nincs hatással az éles környezetre. Ez segít elkerülni az utolsó pillanatban konfigurációs problémák, amelyek hátrányosan befolyásolhatják rto.
    - Amikor egy teszt feladatátvételt futtat a fúróhoz, azt javasoljuk, hogy a replikáció engedélyezésekor beállított alapértelmezett hálózat helyett használjon külön Azure-beli virtuálisgép-hálózatot.
- Használjon különböző IP-címtereket, ha egyetlen ExpressRoute-kapcsolaton van.
    - Azt javasoljuk, hogy a cél virtuális hálózathoz más IP-címterületet használjon. Ezzel elkerülhetők a problémák, amikor a regionális kimaradások során kapcsolatokat hoz létre.
    - Ha nem tud külön címterületet használni, győződjön meg arról, hogy a vész-helyreállítási teszt feladatátvételt egy külön teszthálózaton futtatja, különböző IP-címekkel. Nem csatlakoztathat két, egymást átfedő IP-címtérrel rendelkező virtuális hálózatot ugyanahhoz az ExpressRoute-kapcsolathoz.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Azure-virtuális gépek replikálása az ExpressRoute használatakor


Ha egy elsődleges helyen szeretné beállítani az Azure virtuális gépek replikációját, és ezeket a virtuális gépeket a helyszíni helyről csatlakozik expressroute-on keresztül, a következőket kell tennie:

1. [Engedélyezze](azure-to-azure-tutorial-enable-replication.md) a replikációt az egyes Azure virtuális gépekhez.
2. Szükség esetén a Site Recovery beállítja a hálózatot:
    - A replikáció konfigurálásakor és engedélyezésekor a Site Recovery hálózatokat, alhálózatokat és átjáró-alhálózatokat állít be a cél Azure-régióban, hogy megfeleljenek a forrásrégióban lévőknek. A Site Recovery a forrás és a cél virtuális hálózatok között is leképezi.
    - Ha nem szeretné, hogy a Site Recovery ezt automatikusan elvégezze, a replikáció engedélyezése előtt hozza létre a céloldali hálózati erőforrásokat.
3. Hozzon létre egyéb hálózati elemeket:
    - A Site Recovery nem hoz létre útvonaltáblákat, virtuális hálózati átjárókat, virtuális hálózati átjárókapcsolatokat, virtuális hálózati társviszony-létesítést vagy más hálózati erőforrásokat és kapcsolatokat a másodlagos régióban.
    - Ezeket a további hálózati elemeket a másodlagos régióban kell létrehoznia, bármikor, mielőtt feladatátvételt futtatna az elsődleges régióból.
    - A [helyreállítási tervek](site-recovery-create-recovery-plans.md) és az automatizálási parancsfájlok segítségével beállíthatja és csatlakoztathatja ezeket a hálózati erőforrásokat.
1. Ha egy hálózati virtuális berendezés (NVA) telepítve van a hálózati forgalom áramlásának szabályozására, vegye figyelembe a következőket:
    - Az Azure alapértelmezett rendszerútvonala az Azure virtuális gép replikációjához 0.0.0.0/0.
    - Az NVA-telepítések általában egy alapértelmezett útvonalat (0.0.0.0/0) is meghatároznak, amely a kimenő internetes forgalmat az NVA-n keresztül kényszeríti. Az alapértelmezett útvonal akkor használatos, ha nem található más konkrét útvonalkonfiguráció.
    - Ebben az esetben az NVA túlterhelt lehet, ha az összes replikációs forgalom áthalad az NVA-n.
    - Ugyanez a korlátozás vonatkozik az alapértelmezett útvonalak használata esetén az összes Azure virtuális gép forgalmának a helyszíni üzembe helyezésekhez történő útválasztásához.
    - Ebben a forgatókönyvben azt javasoljuk, hogy [hozzon létre egy hálózati szolgáltatás végpontot](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózatban a Microsoft.Storage szolgáltatás, hogy a replikációs forgalom nem hagyja el az Azure-határt.

## <a name="replication-example"></a>Példa replikációs példa

Általában a vállalati telepítések számítási feladatok több Azure virtuális hálózaton között vannak felosztva, és egy központi kapcsolódási központtal rendelkeznek az internethez és a helyszíni helyekhez való külső kapcsolathoz. A hub ot és a küllős topológiát általában az ExpressRoute-tal együtt használják.

![Helyszíni Azure-beli és Azure-beli feladatátvétel előtt](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Régió .** Az alkalmazások az Azure Kelet-Ázsia régióban vannak telepítve.
- **Küllős vNets**. Az alkalmazások két küllős virtuális hálózatban vannak telepítve:
    - **Forrás vNet1**: 10.1.0.0/24.
    - **Forrás vNet2**: 10.2.0.0/24.
    - Minden küllős virtuális hálózat csatlakozik a **Hub vNet.Each**spoke virtual network is connected to Hub vNet .
- **Hub virtuális hálózat**. Van egy központi virtuális hálózat **forrásközpont virtuális hálózat:** 10.10.10.0/24.
  - Ez a központi virtuális hálózat forgalomirányítóként működik.
  - Az alhálózatok közötti összes kommunikáció ezen a hubon keresztül megy keresztül.
    - **Hub virtuális hálózatok.** A központi virtuális hálózat két alhálózattal rendelkezik:
    - **NVA alhálózat:** 10.10.10.0/25. Ez az alhálózat NVA-t tartalmaz (10.10.10.10).
    - **Átjáró alhálózat:** 10.10.10.128/25. Ez az alhálózat egy ExpressRoute-átjárót tartalmaz, amely egy ExpressRoute-kapcsolathoz csatlakozik, amely egy privát társviszony-létesítési útválasztási tartományon keresztül a helyszíni helyhez vezet.
- A helyszíni adatközpont egy ExpressRoute-kapcsolat egy partner peremhálózaton keresztül Hong Kongban rendelkezik.
- Minden útválasztás az Azure-útvonaltáblák (UDR) által vezérelt.
- A virtuális hálózatok közötti összes kimenő forgalom, vagy a helyszíni adatközpont az NVA-n keresztül.

### <a name="hub-and-spoke-peering-settings"></a>Központi és küllős társviszony-létesítési beállítások

#### <a name="spoke-to-hub"></a>A küllőt a központtal

**Irányba** | **Beállítás** | **Állapot**
--- | --- | ---
A küllőt a központtal | Virtuális hálózati cím engedélyezése | Engedélyezve
A küllőt a központtal | Továbbított forgalom engedélyezése | Engedélyezve
A küllőt a központtal | Átjáró-átvitel engedélyezése | Letiltva
A küllőt a központtal | Átjárók eltávolítása | Engedélyezve

 ![A központi társviszony-létesítési konfigurációval beszélget](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>A központot a küllővel

**Irányba** | **Beállítás** | **Állapot**
--- | --- | ---
A központot a küllővel | Virtuális hálózati cím engedélyezése | Engedélyezve
A központot a küllővel | Továbbított forgalom engedélyezése | Engedélyezve
A központot a küllővel | Átjáró-átvitel engedélyezése | Engedélyezve
A központot a küllővel | Átjárók eltávolítása | Letiltva

 ![Hub a küllős társviszony-létesítési konfigurációhoz](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Példa lépések

A példában a következő kell történnie, amikor engedélyezi a replikációt az Azure virtuális gépek a forráshálózatban:

1. Engedélyezi a [virtuális gép replikációját.](azure-to-azure-tutorial-enable-replication.md)
2. A Site Recovery replikálási virtuális hálózatokat, alhálózatokat és átjáró-alhálózatokat hoz létre a célrégióban.
3. A Site Recovery leképezéseket hoz létre a forráshálózatok és az általa létrehozott replikacélhálózatok között.
4. Manuálisan hozhat létre virtuális hálózati átjárókat, virtuális hálózati átjárókapcsolatokat, virtuális hálózati társviszony-létesítést vagy bármely más hálózati erőforrást vagy kapcsolatot.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Az ExpressRoute használata esetén az Azure-beli virtuális gépek feletti feladatátvétel

Miután a Site Recovery használatával meghibásodott az Azure-beli virtuális gépek a cél-Azure-régióban, az ExpressRoute [privát társviszony-létesítésével érheti](../expressroute/expressroute-circuit-peerings.md#privatepeering)el őket.

- Az ExpressRoute-ot egy új kapcsolattal kell csatlakoztatnia a cél virtuális hálózathoz. A meglévő ExpressRoute-kapcsolat nem kerül automatikusan átvitelre.
- Az ExpressRoute-kapcsolat beállítása a cél virtuális hálózathoz az ExpressRoute-topológiától függ.


### <a name="access-with-two-circuits"></a>Hozzáférés két áramkörrel

#### <a name="two-circuits-with-two-peering-locations"></a>Két áramkör két társviszony-létesítési helynel

Ez a konfiguráció segít megvédeni az ExpressRoute-áramköröket a regionális katasztrófa ellen. Ha az elsődleges társviszony-létesítési hely leáll, a kapcsolatok a másik helyről is folytathatók.

- Az éles környezethez csatlakoztatott áramkör általában az elsődleges. A másodlagos áramkör általában kisebb sávszélességgel rendelkezik, amely katasztrófa esetén növelhető.
- Feladatátvétel után a másodlagos ExpressRoute-kapcsolat a cél virtuális hálózathoz. Azt is megteheti, hogy a kapcsolatokat beállítja, és katasztrófa esetén készen áll, hogy csökkentse a teljes helyreállítási időt.
- Az elsődleges és a cél virtuális hálózatok egyidejű kapcsolata, győződjön meg arról, hogy a helyszíni útválasztási csak a másodlagos kapcsolat és a kapcsolat feladatátvétel után.
- A forrás- és a célvirtuális hálózatok a feladatátvétel után új IP-címeket kaphatnak, vagy megtarthatja ugyanazokat. Mindkét esetben a másodlagos kapcsolatok a feladatátvétel előtt is létrehozhatók.


#### <a name="two-circuits-with-single-peering-location"></a>Két áramkör egyetlen társviszony-létesítési helynel

Ez a konfiguráció segít az elsődleges ExpressRoute-kapcsolat meghibásodása ellen, de nem akkor, ha az egyetlen ExpressRoute-társviszony-létesítési hely leáll, és mindkét kapcsolatra hatással van.

- A helyszíni adatközpontból egyidejű kapcsolatokkal rendelkezhet a vNEt elsődleges áramkörrel és a másodlagos kapcsolattal rendelkező cél virtuális hálózat forrásához.
- Az elsődleges és a cél egyidejű kapcsolata esetén győződjön meg arról, hogy a helyszíni útválasztás csak a másodlagos kapcsolatot és a feladatátvétel t követő kapcsolatot használja.
-   Nem csatlakoztathatja mindkét kapcsolatkapcsolatot ugyanahhoz a virtuális hálózathoz, ha az áramkörök ugyanazon a társviszony-létesítési helyen jönnek létre.

### <a name="access-with-a-single-circuit"></a>Hozzáférés egyetlen áramkörrel

Ebben a konfigurációban csak egy Expressroute-kapcsolat van. Bár az áramkör redundáns kapcsolattal rendelkezik, ha az egyik leáll, egyetlen útvonal-kapcsolat nem biztosít rugalmasságot, ha a társviszony-létesítési régió leáll. Vegye figyelembe:

- Az Azure-beli virtuális gépekreplikálhatók bármely Azure-régióban ugyanazon a [földrajzi helyen.](azure-to-azure-support-matrix.md#region-support) Ha a cél-Azure-régió nem ugyanazon a helyen, mint a forrás, engedélyeznie kell expressroute Premium, ha egyetlen ExpressRoute-kapcsolaton keresztül. További információ az [ExpressRoute-helyekről](../expressroute/expressroute-locations.md) és [az ExpressRoute-díjszabásról.](https://azure.microsoft.com/pricing/details/expressroute/)
- Nem csatlakoztathat forrás- és célvirtuális hálózatokat egyszerre a kapcsolatcsoporthoz, ha ugyanazt az IP-címterületet használja a célrégióban. Ebben a forgatókönyvben:    
    -  Válassza le a forrásoldali kapcsolatot, majd hozza létre a céloldali kapcsolatot. Ez a kapcsolatmódosítás a Hely-helyreállítási helyreállítási terv részeként is parancsfájlálható. Vegye figyelembe:
        - Ha egy regionális hiba esetén az elsődleges régió nem érhető el, a kapcsolatbontási művelet sikertelen lehet. Ez hatással lehet a kapcsolat létrehozásához a célrégióhoz.
        - Ha a kapcsolatot a célrégióban hozta létre, és az elsődleges régió később helyreáll, csomagleejtést tapasztalhat, ha két egyidejű kapcsolat próbál csatlakozni ugyanahhoz a címterülethez.
        - Ennek elkerülése érdekében azonnal szakítsa meg az elsődleges kapcsolatot.
        - Miután a virtuális gép feladat-visszavétel az elsődleges régióba, az elsődleges kapcsolat újra létrejöhet, miután leválasztotta a másodlagos kapcsolatot.
-   Ha a cél virtuális hálózaton más címtereket használ, egyidejűleg csatlakozhat a forrás- és a célvirtuális hálózatokhoz ugyanabból az ExpressRoute-kapcsolati kapcsolatból.


## <a name="failover-example"></a>Például feladatátvételi példa

Példánkban a következő topológiát használjuk:

- Két különböző ExpressRoute-kapcsolati kapcsolati kapcsolati kapcsolati hely.
- A feladatátvétel után az Azure-beli virtuális gépek privát IP-címeinek megőrzése.
- A cél helyreállítási régió az Azure Délkelet-Ázsia.
- A másodlagos ExpressRoute-kapcsolat szingapúri partnerperiómen keresztül jön létre.

Egyetlen ExpressRoute-áramkört használó egyszerű topológia esetén, amelynek ip-címe a feladatátvétel után azonos, [tekintse át ezt a cikket.](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)

### <a name="example-steps"></a>Példa lépések
A helyreállítás automatizálásához ebben a példában a következőket kell tennie:

1. A replikáció beállításához kövesse a lépéseket.
2. [Feladatátvétel az Azure virtuális gépek](azure-to-azure-tutorial-failover-failback.md), ezekkel a további lépésekkel a feladatátvétel során vagy után.

    a. Hozza létre az Azure ExpressRoute-átjárót a célrégió beli virtuális hálózatban. Ehhez csatlakoztatnikell a célközpont virtuális hálózatát az ExpressRoute-kapcsolattal.

    b. Hozza létre a kapcsolatot a célközpont virtuális hálózatáról a cél ExpressRoute-kapcsolat.

    c. Állítsa be a virtuális hálózat társviszony-létesítések között a célrégió hub és a küllővirtuális hálózatok. A célrégió társviszony-létesítési tulajdonságai megegyeznek a forrásrégió tulajdonságaival.

    d. Állítsa be az UDRs a központi virtuális hálózat, és a két küllővirtuális ok.

    - A céloldal UDRs tulajdonságai megegyeznek a forrásoldalon lévőtulajdonságokkal, ha ugyanazokat az IP-címeket használják.
    - A különböző cél IP-címek esetén az UdRs-t ennek megfelelően módosítani kell.


A fenti lépések a [helyreállítási terv](site-recovery-create-recovery-plans.md)részeként írhatók. Az alkalmazás kapcsolatától és a helyreállítási idő követelményeitől függően a fenti lépések a feladatátvétel megkezdése előtt is elvégezhetők.

#### <a name="after-recovery"></a>A helyreállítás után

A virtuális gépek helyreállítása és a kapcsolat befejezése után a helyreállítási környezet a következő.

![Helyszíni Azure-beli és Azure-beli az ExpressRoute-tal feladatátvétel után](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>További lépések

További információ a [helyreállítási tervek](site-recovery-create-recovery-plans.md) használatáról az alkalmazásfeladat-átvétel automatizálásához.
