---
title: ExpressRoute használata az Azure virtuális gép vészhelyreállítás |} Microsoft Docs
description: Azure ExpressRoute használata az Azure Site Recovery Azure virtuális gép katasztrófa utáni helyreállítás
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 44ecbcc51cb53f4d7b68f5c5e24e7d81c5a4208c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071587"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>ExpressRoute használata az Azure virtuális gép katasztrófa utáni helyreállítás

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Ez a cikk ismerteti, hogyan használhatja az ExpressRoute a Site Recovery vészhelyreállítás Azure virtuális gépek.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, tisztában kell lennie:
-   ExpressRoute [Kapcsolatcsoportok](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute [útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Azure virtuális gép [replikációs architektúrája](azure-to-azure-architecture.md)
-   [Replikáció](azure-to-azure-tutorial-enable-replication.md) az Azure virtuális gépek
-   [Feladatátadás](azure-to-azure-tutorial-failover-failback.md) Azure virtuális gépeken

## <a name="expressroute-and-azure-virtual-machine-replication"></a>Az ExpressRoute és az Azure virtuális gép replikációs

Amikor az Azure Site Recovery virtuális gépek védelme, replikációs adatok elküldi egy Azure Storage-fiók vagy a cél felügyelt lemezein a replika Azure-régió, attól függően, hogy az Azure virtuális gépek használata [Azure felügyelt lemezek](../virtual-machines/windows/managed-disks-overview.md). Annak ellenére, hogy a replikációs végpontok nyilvános, az Azure virtuális gép replikációs alapértelmezés szerint a replikációs forgalom nem haladnak át az interneten, függetlenül attól, amely az Azure-régió a forrás virtuális hálózat szerepel.

Azure virtuális gép vész-helyreállítási, a replikációs adatok ne hagyja el az Azure határ ExpressRoute nincs szükség a replikáció. Miután a virtuális gép nem keresztül a cél Azure-régió, akkor is elérhetőek használatával [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Azure-környezetekhez replikálása

Egy korábbi [cikk](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity), egy Azure-beli virtuális hálózat ügyfél olyan helyszíni adatközpontban ExpressRoute keresztül csatlakozik egy egyszerű beállítás leírását. Jellemző nagyvállalati környezetben e osztani több Azure virtuális hálózat munkaterhelésekkel rendelkeznek, és egy központi kapcsolat hub külső kapcsolatot, az internethez, és a helyszíni központi létrejön.

Ebben a példában leírja, egy küllős topológia, ami közös az olyan vállalati központi telepítéshez:
-   A telepítés van a **Azure Kelet-Ázsia** régió és a helyszíni adatközpontját a fiókpartner peremhálózati hongkongi keresztül ExpressRoute-kapcsolatcsoport kapcsolat van.
-   Alkalmazások vannak telepítve, két küllős virtuális hálózatok – közötti **forrás VNet1** a cím terület 10.1.0.0/24 és **forrás VNet2** a cím terület 10.2.0.0/24.
-   A központ virtuális hálózat **forrás Hub VNet**, a címterület 10.10.10.0/24 úgy működik, mint a forgalomirányító. A központ végighalad az alhálózatok közötti összes kommunikáció.
-   A központ virtuális hálózatnak két alhálózatból állnak – **NVA alhálózati** a cím terület 10.10.10.0/25 és **Átjáróalhálózatot** a cím terület 10.10.10.128/25.
-   A **NVA alhálózati** a hálózati virtuális készülék 10.10.10.10 IP-címmel rendelkezik.
-   A **Átjáróalhálózatot** ExpressRoute-átjáró csatlakozik-e egy ExpressRoute-kapcsolatot, amely továbbítja az ügyfél helyszíni adatközpontját a magánhálózati társviszony-létesítés útválasztási tartomány segítségével.
-   Minden egyes küllős virtuális hálózat csatlakozik-e a központ virtuális hálózat és a belül a hálózati topológia összes útválasztási Azure útvonal táblák (UDR) keresztül. Minden kimenő forgalom egy virtuális hálózatot a virtuális hálózatot, vagy a helyszíni adatközpontját a NVA keresztül történik.

![A-helyi-az-Azure-bA a feladatátvétel előtt ExpressRoute](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Hub és küllős társviszony-létesítés

A központ társviszony-létesítés küllős a következő konfigurációval rendelkezik:
-   Lehetővé teszi a virtuális hálózati cím: engedélyezve
-   Engedélyezi a továbbított forgalmat: engedélyezve
-   Átjáró átvitel engedélyezése: letiltva
-   Használjon eltávolítása átjárók: engedélyezve

 ![Hub társviszony-létesítési konfiguráció küllő](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

A társviszony-létesítés küllő hubot a következő konfigurációval rendelkezik:
-   Lehetővé teszi a virtuális hálózati cím: engedélyezve
-   Engedélyezi a továbbított forgalmat: engedélyezve
-   Átjáró átvitel engedélyezése: engedélyezve
-   Használjon eltávolítása átjárók: letiltva

 ![A társviszony-létesítési konfiguráció küllő hub](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>A központi telepítés replikáció engedélyezése

A fenti telepítő első [vész-helyreállítási](azure-to-azure-tutorial-enable-replication.md) tartozó minden virtuális gépet a Site Recovery használatával. A Site Recovery cél régió, virtuális hálózatok (például alhálózatok és átjáró alhálózatok) létrehozni a replikát, és hozzon létre a forrás és cél virtuális hálózatok közötti leképezések. Lehet előzetes létrehozása a cél ügyféloldali hálózatok és alhálózatok, és azonos használni a replikáció engedélyezése során.

A Site Recovery nem replikálódnak az útvonaltáblák, virtuális hálózati átjárók, virtuális hálózati átjáró kapcsolatok, virtuális hálózati társviszony-létesítést, vagy bármely más hálózati erőforrások vagy kapcsolatok. Ezeknek és más erőforrások, amelyek nem részei a [replikációs folyamat](azure-to-azure-architecture.md#replication-process) kell során vagy a feladatátvétel előtt létrehozott és a megfelelő erőforrásokhoz csatlakozik. Azure Site Recovery használhatja hatékony [helyreállítási tervek](site-recovery-create-recovery-plans.md) hoz létre és további források automatizálási parancsfájlok segítségével csatlakoztatja automatizálásához.

Alapértelmezés szerint a replikációs forgalom nem hagyja meg az Azure-határ. NVA központi telepítések általában is meghatározhat egy alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a kimenő internetforgalom az NVA áramlása érdekében. A készülék beolvasása halmozódni ebben az esetben, ha a replikációs forgalom átmegy az NVA. Ugyanez érvényes a alapértelmezett útvonalakat minden Azure virtuális gép forgalom irányításához a helyszíni központi használatakor is. Ajánlott [egy virtuális hálózati szolgáltatás-végpont létrehozása](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózatot "Tároló" úgy, hogy a replikálás forgalma nem hagynak Azure határ.

## <a name="failover-models-with-expressroute"></a>ExpressRoute feladatátvételi modellek

Az Azure virtuális gépek feladatátvétel történt egy másik régióban, ha a meglévő ExpressRoute-kapcsolatot a forrás virtuális hálózathoz nem automatikusan átkerül a cél virtuális hálózat helyreállítási régió. Új kapcsolat ExpressRoute a cél virtuális hálózathoz való kapcsolódáshoz szükséges.

Azure virtuális gépeken belül az azonos földrajzi fürt részletes bármely Azure régióban replikálhatja [Itt](azure-to-azure-support-matrix.md#region-support). Ha a választott cél Azure-régiót kívül esik a forrás-és geopolitikai ugyanabban a régióban, akkor engedélyeznie kell ExpressRoute prémium használata egy ExpressRoute-kapcsolatcsoportot a forrás és cél régió kapcsolatra. További részletekért ellenőrizze [helyek ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) és [ExpressRoute árképzési](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Két különböző ExpressRoute társviszony-létesítési helyeken két ExpressRoute-Kapcsolatcsoportok
-   Ez a konfiguráció akkor hasznos, ha sikertelen volt-e az elsődleges ExpressRoute-kapcsolatcsoport és nagy méretű regionális katasztrófák, sikerült is hatással lehet a társviszony-létesítési helyek ExpressRoute és megzavarhatják a elsődleges ExpressRoute-kapcsolatcsoportot elleni biztosítását szeretné.
-   A kapcsolatcsoport kapcsolódik az éles környezetben használt általában az elsődleges kapcsolat és a másodlagos kör egy failsafe, és általában alacsony sávszélességű. A sávszélesség a másodlagos katasztrófa esemény, ha a másodlagos kell vegye át az elsődleges növelhető.
-   Ezzel a konfigurációval hozható létre a másodlagos ExpressRoute-kapcsolatcsoportot a cél virtuális hálózathoz való csatlakozást feladatátvétel utáni, vagy a kapcsolat létrejötte és készen áll a katasztrófa nyilatkozatot, a teljes helyreállítási idő csökkentése. Az egyidejű kapcsolatok mind az elsődleges és a cél régió virtuális hálózatok, győződjön meg arról, hogy a helyi útválasztási használja-e a másodlagos kör és a kapcsolat csak a feladatátvételt követően.
-   A forrás és cél virtuális hálózatok, a Site Recovery védett virtuális gépek feladatátvétel / a követelmény lehet az ugyanazon vagy másik IP-címek. Mindkét esetben a másodlagos kapcsolatok hozhatók létre a feladatátvétel után már.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Az azonos ExpressRoute-társviszony-létesítési helye két ExpressRoute-Kapcsolatcsoportok
-   Ezzel a konfigurációval akkor is az elsődleges ExpressRoute-kapcsolatcsoport meghibásodása elleni biztosítását, de nem elleni nagyméretű regionális katasztrófák, amely hatással lehetett a társviszony-létesítési helyek ExpressRoute. Ez utóbbi az elsődleges és másodlagos kapcsolatok lekérése érintett.
-   A más feltételei IP-cím és a kapcsolatok továbbra is ugyanazok, mint a korábbi esetében. Akkor is a helyszíni adatközpontban származó egyidejű kapcsolatokat az elsődleges kapcsolat forrás virtuális hálózathoz, és a másodlagos kör a cél virtuális hálózathoz. Az egyidejű kapcsolatok mind az elsődleges és a cél régió virtuális hálózatok, győződjön meg arról, hogy a helyi útválasztási használja-e a másodlagos kör és a kapcsolat csak a feladatátvételt követően.
-   Kapcsolatok jönnek létre ugyanazon a társviszony-létesítési helyen mindkét kapcsolatok nem tud kapcsolódni az azonos virtuális hálózatban.

### <a name="single-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoportot
-   Ez a konfiguráció nem biztosítását nagyméretű regionális katasztrófa, amely jelentős hatással lehet az ExpressRoute-társviszony-létesítési helye alapján.
-   Egy ExpressRoute-kapcsolatcsoport nem csatlakozzon a forrás és cél virtuális hálózatok egyidejűleg áramkör cél régió, az azonos IP-címtér használata.
-   Az azonos IP-címtér használatakor a cél régió a kell megszakításra oldalon adatforrást, és a cél ügyféloldali kapcsolat ezt követően. A kapcsolat módosítása a helyreállítási terv részeként parancsfájlalapú lehet.
-   Regionális hibát Ha az elsődleges régióban nem érhető el, a leválasztás művelet meghiúsulhat. Nem tervezett kimaradás jelentős hatással lehet a kapcsolat létrehozásakor a cél régióban azonos IP-címtér használatakor a cél virtuális hálózat.
-   Ha elsődleges régió helyreállítja később cél sikeres kapcsolat létrehozásakor, is szembesülhetnek csomag elhagyta, ha a két egyidejű kapcsolat megpróbáljon csatlakozni a ugyanazt a címtartományt. Csomag elhagyta megelőzése érdekében az elsődleges kapcsolat azonnal meg kell szüntetni. A másodlagos kapcsolathoz a leválasztás után feladás egy vagy több feladat-visszavételt a virtuális gépek az elsődleges régióban, az elsődleges kapcsolat is újra létrehozni.
-   Eltérő a cél virtuális hálózat használatban van, ha ezután egyidejűleg csatlakozni a forrás és cél virtuális hálózatok az ExpressRoute-kapcsolatcsoportot.

## <a name="recovering-azure-deployments"></a>Azure-környezetekhez helyreállítása
Fontolja meg a feladatátvételi modell két különböző ExpressRoute-Kapcsolatcsoportok két különböző társviszony-létesítési helyen, és a védett Azure virtuális gépek magánhálózati IP-címek megőrzési. A cél helyreállítási régió Azure Délkelet-Ázsiában, és másodlagos ExpressRoute körön kapcsolatot létesít a fiókpartner peremhálózati szingapúri keresztül.

Helyreállítási mellett replikáló virtuális gépek és virtuális hálózatok, a teljes központi telepítésének automatizálásához más releváns hálózati erőforrások és a kapcsolatok is kell létrehozni. A korábbi küllős hálózati topológiájának a következő további lépéseket kell tenni, során vagy azt követően a [feladatátvételi](azure-to-azure-tutorial-failover-failback.md) műveletet:
1.  Hozzon létre az Azure ExpressRoute-átjárót a cél régió hub virtuális hálózat. A cél hub virtuális hálózathoz csatlakozni az ExpressRoute-kapcsolatcsoport az ExpressRoute-átjáró szükséges.
2.  A cél-központ virtuális hálózat a virtuális hálózati kapcsolat létrehozása a célként megadott ExpressRoute-kapcsolatcsoportot.
3.  Állítsa be a virtuális hálózat esetében a cél régió hub és küllős virtuális hálózatok között. A cél régió társviszony-létesítési tulajdonságok ugyanazok, mint a forrás régió lesz.
4.  A virtuális hálózat központban udr-EK és a két küllős Vnetek beállítása. A tulajdonságok a cél oldal udr-EK esetén ugyanazok, mint a forrás oldalon a azonos IP-címek használata. Másik cél IP-címek a udr-EK megfelelően módosítani kell.

Parancsfájlalapú lehet a fenti lépések részeként egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Attól függően, hogy az alkalmazás kapcsolódási és a helyreállítási idő követelményei a fenti lépéseket is is elvégezhető a feladatátvétel elkezdése előtt.

A helyreállítási virtuális gépet és a többi kapcsolódási lépés befejezése utáni, a helyreállítási környezet a következőképpen néz ki: ![a-helyi-az-Azure-bA a feladatátvételt követően ExpressRoute](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Egy egyszerű topológiát például egyetlen ExpressRoute-kapcsolatcsoportot, azonos IP-cél virtuális gépeken, a vész-helyreállítási Azure virtuális gép részletes [Itt](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Idő célkitűzés (RTO) kapcsolatos szempontok
Az üzembe helyezéshez a teljes helyreállítási idő csökkentése érdekében a üzembe helyezési és központi telepítése a további cél régió ajánlott [hálózati összetevők](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) például virtuális hálózati átjárók előre. Egy kis állásidő további erőforrásokat üzembe helyezi, és üzemszünet kedvezőtlen hatással lehet a teljes helyreállítási idejét, ha nem, vegye figyelembe a tervezés során.

Javasoljuk, hogy fut regular [vészhelyreállítások részletezésének](azure-to-azure-tutorial-dr-drill.md) a védett központi telepítéseket. A részletezés érvényesíti a replikációs stratégiájának adatvesztés vagy leállás nélkül, és nincs hatással az éles környezetben. A részletezés futtató megakadályozza, hogy egyszeri konfigurációs problémák, amelyek hátrányosan befolyásolhatja a helyreállítási idő célkitűzése. Azt javasoljuk, külön Azure Virtuálisgép-hálózatot a feladatátvételi teszthez, az alapértelmezett hálózat, amely be lett állítva, ha engedélyezte a replikációs helyett.

Egy ExpressRoute-kapcsolatcsoportot használata, azt javasoljuk, egy másik IP-címtér a cél virtuális hálózat regionális katasztrófák során kapcsolat kialakítása problémák elkerülése érdekében. Különböző IP-címek használata nem megvalósítható, a helyreállított éles környezetben, a vész helyreállítási részletezési feladatátvételi tesztet kell végezni a különböző IP-címekkel rendelkező vizsgálati elkülönített hálózat kapcsolódáskor két virtuális hálózat nem átfedő IP- az azonos ExpressRoute-kapcsolatcsoport-címterek.

## <a name="next-steps"></a>További lépések
- További információ [ExpressRoute-Kapcsolatcsoportok](../expressroute/expressroute-circuit-peerings.md).
- További információ [ExpressRoute útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- További információ [helyek ExpressRoute](../expressroute/expressroute-locations.md).
- További információ [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
