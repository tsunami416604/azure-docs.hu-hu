---
title: Az Azure virtuális gépek Azure Site Recovery használata vész-helyreállítási Azure ExpressRoute integrálása |} A Microsoft Docs
description: Ismerteti, hogyan lehet Azure-beli virtuális gépek Azure Site Recovery és az Azure ExpressRoute használatával vészhelyreállítás beállítása
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 03fac23ea17a6baa1b43e748a4390cf142661a19
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353541"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Vészhelyreállítás az Azure virtuális gépek Azure ExpressRoute integrálása


Ez a cikk azt ismerteti, hogyan integrálható az Azure ExpressRoute [Azure Site Recovery](site-recovery-overview.md), amikor Ön vészhelyreállítás beállítása az Azure virtuális gépek másodlagos Azure-régióba.

A Site Recovery lehetővé teszi, hogy Azure virtuális gépek vészhelyreállítása az Azure virtuális gép adatait az Azure-ba való replikálásával.

- Ha az Azure-beli virtuális gépek használni [Azure managed disks](../virtual-machines/windows/managed-disks-overview.md), virtuális gép adatait a rendszer replikálja egy replikált felügyelt lemez a másodlagos régióban.
- Azure virtuális gépek nem felügyelt lemezek használata, ha virtuális gép adatait a rendszer replikálja egy Azure storage-fiókot.
- Replikációs végpontok nyilvános, de az Azure virtuális gépek replikációs forgalom nem adatbázisközi az interneten.

Az ExpressRoute segítségével kiterjesztheti helyszíni hálózatait a Microsoft Azure-felhőben, amelyet egy kapcsolatszolgáltató biztosít, egy privát kapcsolaton keresztül. Ha az ExpressRoute konfigurálva van, együttműködik a Site Recovery a következő:

- **Az Azure-régiók közötti replikáció során**: replikációs forgalmát az Azure-beli virtuális gépek vészhelyreállításához csak az Azure-ban, és az ExpressRoute nem szükséges, vagy a replikációhoz használt. Azonban, amelyhez csatlakozik egy helyszíni helyről az Azure virtuális gépek az Azure elsődleges helyen, van-e olyan problémák érdemes figyelembe vennie, amikor beállítása vész-helyreállítási ezeket az Azure-beli virtuális gépek száma.
- **Az Azure-régiók közötti feladatátvétel**: Ha valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat az Azure virtuális gépek az elsődleges kiszolgálóról másodlagos Azure-régióba. Miután-ba irányuló feladatátvétel egy másodlagos régióba, a program több lépést ahhoz, hogy az ExpressRoute-tal tudjon másodlagos régióban az Azure virtuális gépek eléréséhez.


## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, győződjön meg arról, hogy megértette a következő fogalmak:

- Az ExpressRoute [Kapcsolatcsoportok](../expressroute/expressroute-circuit-peerings.md)
- Az ExpressRoute [útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
- Az ExpressRoute [helyek](../expressroute/expressroute-locations.md).
- Az Azure virtuális gép [replikáció architektúrája](azure-to-azure-architecture.md)
- Hogyan [-replikálás beállítása](azure-to-azure-tutorial-enable-replication.md) az Azure virtuális gépek.
- Hogyan [átadja a feladatokat](azure-to-azure-tutorial-failover-failback.md) Azure virtuális gépeken.


## <a name="general-recommendations"></a>Általános javaslatok

Az ajánlott eljárás, és hatékony Helyreállításiidő-célkitűzések (RTO) vész-helyreállítási biztosítása érdekében javasoljuk, hogy beállításakor a Site Recovery integrálható az expressroute-tal tegye a következőket:

- A feladatátvétel egy másodlagos régióba előtt hálózati összetevők üzembe helyezése:
    - Az Azure virtuális gépek a replikáció engedélyezése a Site Recovery is üzembe helyezhetők a hálózati erőforrások, például a hálózatok, alhálózatok és a forrás hálózati beállításai alapján a cél Azure-régióban, az átjárókat.
    - A Site Recovery automatikusan hálózati erőforrások, például virtuális hálózati átjárók nem tudja beállítani.
    - Azt javasoljuk, hogy ezek a feladatátvétel előtt további hálózati erőforrások kiépítése. Egy kis állásidő társítva ehhez a központi telepítéshez, és befolyásolhatja a teljes helyreállítási időt, ha az üzembe helyezés tervezése során ez a fiók nem.
- Rendszeres vészhelyreállítási próbákat:
    - A próba adatveszteség és állásidő nélkül ellenőrzi a replikációs stratégiáját, és nincs hatással az éles környezetre. Ez segít, hogy negatívan befolyásolhatja a RTO utolsó pillanatban konfigurációs problémák elkerülése érdekében.
    - A részletezés a feladatátvételi teszt futtatásakor azt javasoljuk, hogy egy külön Azure-beli Virtuálisgép-hálózatot, a replikáció engedélyezésekor beállított alapértelmezett hálózat helyett használja.
- Különböző IP-címterek akkor használhatja, ha egyetlen ExpressRoute-kapcsolatcsoportot.
    - Azt javasoljuk, hogy a cél virtuális hálózat egy másik IP-címteret használja. Problémák Ezzel elkerülhető a regionális üzemkimaradások utáni helyreállításon során kapcsolatok létesítéséhez.
    - Ha nem használhat egy külön címtér, mindenképp vész helyreállítási részletes feladatátvételi teszt futtatása a különböző IP-címek külön vizsgálat hálózat. Két virtuális hálózat nem átfedésben lévő IP-címtér ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz való csatlakozni.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Azure virtuális gépek replikálása az ExpressRoute használata során


Ha azt szeretné, az Azure virtuális gépek replikációjának beállításához egy elsődleges helyen, és csatlakozik a virtuális gépek a helyszíni helyről expressroute-on keresztül, a következő mit kell tennie:

1. [Engedélyezze a replikációt](azure-to-azure-tutorial-enable-replication.md) minden Azure virtuális géphez.
2. Szükség esetén lehetővé teszik a Site Recovery hálózatának beállítása:
    - Konfigurálásakor, és engedélyezze a replikációt, a Site Recovery állít be hálózatok, alhálózatok és átjáró-alhálózatokkal a cél Azure-régióban, hogy megegyezzenek a forrásrégióban. A Site Recovery is leképezi a forrás- és virtuális hálózatok között.
    - Ha nem szeretné automatikusan ehhez a Site Recovery, a céloldali hálózati erőforrások létrehozása, a replikáció engedélyezése előtt.
3. Hozzon létre más hálózati elemek:
    - A Site Recovery nem hoz létre útválasztási táblázatokat, virtuális hálózati átjárók, VNet-gateway-kapcsolatok, virtuális hálózatok közötti társviszony-létesítéshez, vagy más hálózati erőforrások és kapcsolatok a másodlagos régióban.
    - Ezek a kiegészítő hálózati elemek létrehozása a másodlagos régióban, az elsődleges régióból történő feladatátvétel futtatása előtt bármikor van szüksége.
    - Használhat [helyreállítási tervek](site-recovery-create-recovery-plans.md) és automatizálási szkriptek ezek kapcsolatot a hálózati erőforrások.
1. Ha egy hálózati virtuális készüléket (NVA) szabályozhatja a hálózati forgalom telepítve van, vegye figyelembe, hogy:
    - Az Azure alapértelmezett rendszer Azure virtuális gépek replikációjához út 0.0.0.0/0.
    - Jellemzően NVA központi telepítések is meghatározhat egy alapértelmezett útvonalat (0.0.0.0/0), amely kényszeríti a kimenő internetes forgalmat az nva-n keresztül. Az alapértelmezett útvonal szolgál, ha nincsenek más konkrét útválasztó-konfiguráció tekintheti meg.
    - Ez a helyzet, ha az nva-n túlterheltek, ha az összes replikációs forgalmat az nva-n keresztül továbbítja.
    - Az azonos korlátozás is vonatkozik, az alapértelmezett útvonalak használata esetén az összes Azure-beli Virtuálisgép-forgalmat a helyszíni üzemelő példányok az Útválasztás.
    - Ebben a forgatókönyvben javasoljuk, [hozzon létre egy hálózati szolgáltatásvégpontot](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) Microsoft.Storage szolgáltatás esetén a virtuális hálózaton, hogy a replikálás forgalma nem hagyja meg az Azure határt.

## <a name="replication-example"></a>Replikációs példa

Nagyvállalati környezetben általában a számítási feladatok elosztja a több Azure virtuális hálózat, a külső kapcsolatokat az interneten, és a helyszíni helyek központi kapcsolódási hubbal rendelkeznek. Egy küllős topológiában általában ExpressRoute együtt használatos.

![A-helyszíni – Azure ExpressRoute használatával a feladatátvétel előtt](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Régió**. Alkalmazások üzembe vannak helyezve a Kelet-Ázsia Azure-régióban.
- **Küllő virtuális hálózatok**. Alkalmazások üzembe vannak helyezve két küllő virtuális hálózatok:
    - **Forrás vNet1**: 10.1.0.0/24.
    - **Forrás vNet2**: 10.2.0.0/24.
    - Minden küllő virtuális hálózat csatlakozik **agyi virtuális hálózat**.
- **Agyi virtuális hálózat**. Van egy agyi virtuális hálózat **forrás agyi virtuális hálózat**: 10.10.10.0/24.
    - Az agyi virtuális hálózat a forgalomirányító szerepét.
    - Alhálózatok közötti minden kommunikáció próbálja ki a hub.
 - Hub Vnetet alhálózatokra **. Az agyi virtuális hálózat két alhálózattal rendelkezik:
     - **NVA-alhálózat**: 10.10.10.0/25. Ez az alhálózat egy nva-t (10.10.10.10) tartalmazza.
     - **Átjáró-alhálózat**: 10.10.10.128/25. Ezt az alhálózatot tartalmaz, amely a helyszíni hely egy privát társviszony-létesítési útválasztási tartomány segítségével irányítja egy ExpressRoute-kapcsolattal csatlakozik egy ExpressRoute-átjárót.
- A helyszíni adatközponthoz egy ExpressRoute-kapcsolatcsoport kapcsolaton keresztül egy partner edge Hongkong (KKT) rendelkezik.
- Minden útválasztás (UDR) Azure útválasztási táblázatban szabályozza.
- Virtuális hálózat között, vagy a helyszíni adatközpontba minden kimenő forgalom útválasztása az nva-n keresztül történik.

### <a name="hub-and-spoke-peering-settings"></a>Küllős topológiájú társviszony beállításai

#### <a name="spoke-to-hub"></a>A küllőt a központtal

**Direction (Irány)** | **Beállítás** | **állapot**
--- | --- | ---
A küllőt a központtal | Lehetővé teszi a virtuális hálózati cím | Engedélyezve
A küllőt a központtal | Továbbított forgalom engedélyezése | Engedélyezve
A küllőt a központtal | Átjárótranzit engedélyezése | Letiltva
A küllőt a központtal | Remove-átjárók használata | Engedélyezve

 ![Hub társviszony-létesítési konfigurációjának küllő](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>A központot a küllővel

**Direction (Irány)** | **Beállítás** | **állapot**
--- | --- | ---
A központot a küllővel | Lehetővé teszi a virtuális hálózati cím | Engedélyezve
A központot a küllővel | Továbbított forgalom engedélyezése | Engedélyezve
A központot a küllővel | Átjárótranzit engedélyezése | Engedélyezve
A központot a küllővel | Remove-átjárók használata | Letiltva

 ![A társviszony-létesítési konfigurációjának küllő hub](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Példa lépései

Ebben a példában a következő történjen, amikor engedélyezi az Azure-beli virtuális gépek replikációja a forrás hálózati:

1. Ön [engedélyezze a replikációt](azure-to-azure-tutorial-enable-replication.md) egy virtuális géphez.
2. A Site Recovery replika virtuális hálózatok, alhálózatok és átjáró-alhálózatot hoz létre a célrégióban.
3. Site Recovery létrehozza a forrás-hálózatok és a létrehozott replika célhálózatok közötti hozzárendelések.
4. Manuálisan létrehozott virtuális hálózati átjárók, virtuális hálózati átjárókapcsolatok, virtuális hálózatok közötti társviszony, vagy bármely más hálózati erőforrások vagy kapcsolatokat.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Azure virtuális gépek feladatainak átadása ExpressRoute használata során

Miután a feladatátvételt az Azure virtuális gépek a cél a Site Recovery használatával az Azure-régióban, elérheti azokat ExpressRoute-tal [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

- A cél virtuális hálózat egy új kapcsolat ExpressRoute csatlakozni kell. A meglévő ExpressRoute-kapcsolat nem át automatikusan.
- Amelyben állítsa be az ExpressRoute-kapcsolat a célként megadott virtuális hálózathoz módja attól függ, hogy az ExpressRoute-topológia.


### <a name="access-with-two-circuits"></a>Hozzáférés a két Kapcsolatcsoportok

#### <a name="two-circuits-with-two-peering-locations"></a>A két társviszony-létesítési helyszínek két Kapcsolatcsoportok

Ez a konfiguráció segít az ExpressRoute-Kapcsolatcsoportok regionális katasztrófa elleni védelmet biztosít. Ha az elsődleges társviszony-létesítési loation leáll, a kapcsolatok továbbra is a másik helyen.

- A kapcsolatcsoport csatlakozik az éles környezetben általában a az elsődleges. A másodlagos kapcsolatcsoportot általában rendelkezik alacsonyabb sávszélesség, ami növelhető, ha katasztrófa történik.
- A feladatátvételt követően a másodlagos ExpressRoute-kapcsolatcsoport a cél virtuális hálózatok közötti kapcsolatot is létrehozhat. Azt is megteheti hogy kapcsolatok beállítás és felkészülés vészhelyzet esetén a teljes helyreállítási idő csökkentése érdekében.
- Az egyidejű kapcsolatok mind az elsődleges és a cél virtuális hálózatok győződjön meg arról, hogy a helyszíni útválasztási csak használja a másodlagos kapcsolatcsoportot és a feladatátvétel után a kapcsolat.
- A forrás- és virtuális hálózatok kapni az új IP-címek, vagy tartsa ugyanazokat a portokat, a feladatátvételt követően. Mindkét esetben a másodlagos kapcsolatok hozhatók létre a feladatátvétel előtt.


#### <a name="two-circuits-with-single-peering-location"></a>A társviszony-létesítési helyszínen egyetlen két Kapcsolatcsoportok

Ez a konfiguráció segít az elsődleges ExpressRoute-kapcsolatcsoport meghibásodása elleni védelem, de nem az egyetlen ExpressRoute-társviszony-létesítési helyszínen leáll, ha a érintő mindkét Kapcsolatcsoportok.

- A helyszíni adatközpont származó egyidejű kapcsolatokat forrás virtuális hálózat és az elsődleges kapcsolatcsoportot, és a cél virtuális hálózat és a másodlagos kapcsolatcsoportot is rendelkezhet.
- Az egyidejű kapcsolatok az elsődleges és a cél győződjön meg arról, hogy a helyszíni útválasztási csak használja a másodlagos kapcsolatcsoportot és a feladatátvétel után a kapcsolat.
-   Kapcsolatcsoportok társviszony-létesítési ugyanarról a helyről létrehozásakor mindkét Kapcsolatcsoportok nem tud kapcsolódni az azonos virtuális hálózaton.

### <a name="access-with-a-single-circuit"></a>Hozzáférés az egyetlen kapcsolatcsoport

Ebben a konfigurációban nincs csak egy Expressroute-kapcsolatcsoportot. Bár a kapcsolatcsoport redundáns kapcsolattal rendelkezik, abban az esetben, ha egy leáll, egy egyetlen expressroute-kapcsolatcsoport nem nyújt rugalmasságot Ha leáll a társviszony-létesítési régióban. Vegye figyelembe:

- Azure virtuális gépek Azure-régiókba replikálhatja a [azonos földrajzi helyen](azure-to-azure-support-matrix.md#region-support). Ha a cél Azure-régió nem ugyanazon a helyen, mint a forrás-, kívánja engedélyezni az ExpressRoute Premium egy ExpressRoute-kapcsolatcsoport használata. Ismerje meg [ExpressRoute-helyek](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) és [ExpressRoute – díjszabás](https://azure.microsoft.com/pricing/details/expressroute/).
- Nem lehet csatlakoztatni forrás- és virtuális hálózatok egyszerre a kapcsolatcsoporthoz azonos IP-címterület használata a célként megadott régióban. Ebben a forgatókönyvben:    
    -  Szakítsa meg a forrás oldalon kapcsolatot, és ezután a cél ügyféloldali kapcsolat létesítésére. A Site Recovery helyreállítási terv részeként a kapcsolatot change parancsfájlalapú lehet. Vegye figyelembe:
        - A regionális meghibásodással Ha az elsődleges régióban nem érhető el, a leválasztás sikerült sikertelen. Ez hatással lehet a kapcsolat létrehozásakor, a célrégióban.
        - Ha létrehozta a kapcsolatot a célrégióban, és később helyreállítja az elsődleges régióban, csomag csepp tapasztalhatja, ha két egyidejű kapcsolatok próbál kapcsolódni az azonos címtartomány.
        - Ennek megelőzése érdekében azonnal leállítja az elsődleges kapcsolat.
        - Virtuális gép feladat-visszavétel az elsődleges régióba után az elsődleges újra lehet kapcsolódni, a másodlagos kapcsolati leválasztása után.
-   Ha egy másik címteret használja a cél virtuális hálózaton, egyidejűleg csatlakozhat a forrás- és virtuális hálózatok ugyanabban az ExpressRoute-kapcsolatcsoportból.


## <a name="failover-example"></a>Feladatátvételi példa

Ebben a példában a következő topológiát használunk:

- Két különböző ExpressRoute-kapcsolatcsoporttal a két különböző társviszony-létesítési helyszínek.
- Az Azure virtuális gépek magánhálózati IP-címek megőrzése feladatátvétel után.
- A helyreállítási Azure Délkelet-Ázsia.
- Egy másodlagos ExpressRoute-kapcsolatcsoport kapcsolaton keresztül egy fiókpartner peremhálózati a szingapúri jön létre.

Egy egyszerű topológiát használó egyetlen ExpressRoute-kapcsolatcsoport IP-címmel ugyanabban a feladatátvételt követően a [ebből a cikkből](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

### <a name="example-steps"></a>Példa lépései
Ebben a példában Itt a helyreállítás automatizálása, mit kell tennie:

1. Kövesse a lépéseket a [-replikálás beállítása](#azure-vm-replication-steps).
2. [Az Azure virtuális gépek feladatainak átadása](azure-to-azure-tutorial-failover-failback.md), további lépések során, és a feladatátvétel után.

    a. A célként megadott régióban agyi virtuális hálózat létrehozása az Azure ExpressRoute-átjárót. Ez az csatlakoznia kell a cél agyi virtuális hálózat ExpressRoute-kapcsolatcsoporthoz.

    b. Hozzon létre a kapcsolatot a cél agyi virtuális hálózat az ExpressRoute-kapcsolatcsoport cél.

    c. Állítsa be a virtuális hálózatok közötti társviszony-létesítéseket a célrégióban hub és a küllő virtuális hálózatok között. A társviszony-létesítés tulajdonságait a célként megadott régióban lesznek ugyanazok, mint a forrásrégióban található.

    d. Az udr-eket az agyi virtuális hálózat és a két küllő virtuális hálózatok beállítása.

    - A tulajdonságok a céloldalon az udr-EK esetén ugyanazok, mint a forrás oldalon az azonos IP-címekkel.
    - Másik céloldali IP-címmel az udr-EK megfelelően módosítani kell.


A fenti lépések részeként parancsfájlalapú lehet egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Attól függően, az alkalmazás kapcsolat és a helyreállítási időre vonatkozó követelmények a fenti lépéseket is is elvégezhető a feladatátvétel indítása előtt.

#### <a name="after-recovery"></a>A helyreállítás után

A virtuális gépek helyreállítása és a kapcsolat befejezése után a helyreállítási környezetet a következőképpen történik.

![A-helyszíni – Azure feladatátvétel után az ExpressRoute használatával](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>További lépések

További információ [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
