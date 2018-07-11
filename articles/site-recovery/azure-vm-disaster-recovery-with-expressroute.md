---
title: Az ExpressRoute használata Azure virtuális gépek vészhelyreállítása |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure ExpressRoute használata az Azure Site Recovery az Azure virtuális gépek vészhelyreállítása
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 73514b524f554affb9730ba63ccd608491497af2
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920470"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Az ExpressRoute használata Azure virtuális gépek vészhelyreállítása

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Ez a cikk bemutatja, hogyan használhatja az ExpressRoute a Site Recovery az Azure-beli virtuális gépek vészhelyreállítását.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, tisztában kell lennie:
-   Az ExpressRoute [Kapcsolatcsoportok](../expressroute/expressroute-circuit-peerings.md)
-   Az ExpressRoute [útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Az Azure virtuális gép [replikáció architektúrája](azure-to-azure-architecture.md)
-   [A replikáció](azure-to-azure-tutorial-enable-replication.md) Azure-beli virtuális gépek
-   [Feladatátvétele](azure-to-azure-tutorial-failover-failback.md) Azure-beli virtuális gépek

## <a name="expressroute-and-azure-virtual-machine-replication"></a>Az ExpressRoute és az Azure virtuális gépek replikálásának

Ha a Site Recovery-beli virtuális gépek, replikációs adatokat küld a egy Azure Storage-fiók vagy a felügyelt lemezt a cél replika attól függően, hogy az az Azure-beli virtuális gépek használata az Azure-régióban [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). Bár a replikációs végpontok nyilvános, az Azure virtuális gép replikációja, alapértelmezés szerint a replikációs forgalom sem halad át az interneten, függetlenül attól, amely az Azure-régióban a forrás virtuális hálózat szerepel.

Azure virtuális gép vész-helyreállítási, a replikációs adatok nem kerülnek az Azure határ az ExpressRoute nem kötelező replikálásra. Miután a virtuális gépek átadja a feladatokat a cél Azure-régióban, elérheti azokat használó [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Azure-környezetek replikálása

Egy korábbi [cikk](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity), egy ügyfél a helyszíni adatközpont expressroute-on keresztül csatlakozik az Azure virtual network egy egyszerű beállítás leírását. Jellemző nagyvállalati környezetben több Azure virtual networkök számítási feladatok és a egy központi kapcsolódási központ hoz létre a külső kapcsolatokat, az internethez, és a helyszíni üzemelő példányokhoz.

Ebben a példában ez az a vállalati telepítésekben közös egy küllős topológiában ismerteti:
-   Az üzembe helyezés nem található a **Azure Kelet-Ázsia** régió és a helyszíni adatközponthoz egy ExpressRoute-kapcsolatcsoport kapcsolaton keresztül egy fiókpartner peremhálózati hongkongi rendelkezik.
-   Két küllő virtuális hálózat – telepített alkalmazások **forrás VNet1** a cím terület 10.1.0.0/24 és **forrás VNet2** cím terület 10.2.0.0/24 az.
-   A központi virtuális hálózaton **forrás agyi virtuális hálózat**, a címtér 10.10.10.0/24 a forgalomirányító funkcionál. Alhálózatok közötti összes kommunikáció a hubon keresztül halad.
-   A központi virtuális hálózaton van két alhálózattal – **NVA-alhálózat** a cím terület 10.10.10.0/25 és **átjáró-alhálózat** a cím terület 10.10.10.128/25.
-   A **NVA-alhálózat** egy hálózati virtuális berendezésen 10.10.10.10 IP-címmel rendelkezik.
-   A **átjáró-alhálózat** egy ExpressRoute-átjáró csatlakozik-e az ExpressRoute-kapcsolatok, amely az ügyfél helyszíni adatközpont magánhálózati társviszony-létesítés útválasztási tartomány keresztül irányítja.
-   Minden küllő virtuális hálózat csatlakozik a központi virtuális hálózaton, és a hálózati topológia belüli összes Útválasztás szabályozott Azure útválasztási táblázatok (UDR) keresztül. Kimenő forgalom az egyik virtuális hálózatból a virtuális hálózathoz, vagy a helyszíni adatközpontba az nva-n keresztül van átirányítva.

![A-helyszíni – Azure feladatátvétel előtt az ExpressRoute használatával](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Eseményközpont és a küllő társviszony-létesítés

A hub társviszony-létesítési küllőkben a következő konfigurációval rendelkezik:
-   Lehetővé teszi a virtuális hálózati cím: engedélyezve
-   Továbbított forgalom engedélyezéséhez: engedélyezve
-   Átjárótranzit engedélyezése: letiltva
-   Használat távolítsa el az átjárók: engedélyezve

 ![Hub társviszony-létesítési konfigurációjának küllő](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

A társviszony-létesítés küllő hub a következő konfigurációval rendelkezik:
-   Lehetővé teszi a virtuális hálózati cím: engedélyezve
-   Továbbított forgalom engedélyezéséhez: engedélyezve
-   Átjárótranzit engedélyezése: engedélyezve
-   Használat távolítsa el az átjárók: letiltva

 ![A társviszony-létesítési konfigurációjának küllő hub](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Az üzembe helyezés engedélyezése

A fenti beállítás első [vészhelyreállítás beállítása](azure-to-azure-tutorial-enable-replication.md) minden virtuális gép Site Recovery használatával. A Site Recovery a célként megadott régióban lévő virtuális hálózatok (beleértve az alhálózatok és átjáró-alhálózatokkal) a replika létrehozásához és hozhat létre a szükséges a forrás- és virtuális hálózatok közötti leképezéseket. Hozzon előzetesen létre a cél ügyféloldali hálózatok és alhálózatok is, és használja azonos a replikáció engedélyezése során.

A Site Recovery nem replikálja útválasztási táblázatokat, virtuális hálózati átjárók, virtuális hálózati átjárókapcsolatok, virtuális hálózatok közötti társviszony, vagy bármely más hálózati erőforrások vagy kapcsolatokat. Ezen és más erőforrások, amelyek nem részei a [replikációs folyamat](azure-to-azure-architecture.md#replication-process) kell során vagy feladatátvétel előtt létrehozott és a kapcsolódó erőforrásokhoz csatlakozik. Használhatja az Azure Site Recovery hatékony [helyreállítási tervek](site-recovery-create-recovery-plans.md) létrehozása és csatlakozás a további erőforrások automatizálása szkriptekkel automatizálhatja.

Alapértelmezés szerint a replikálás forgalma nem hagyja el az Azure határain. Jellemzően NVA központi telepítések is meghatározhat egy alapértelmezett útvonalat (0.0.0.0/0), amely kényszeríti a kimenő internetes forgalmat az nva-n keresztül. Ebben az esetben a készülék előfordulhat, hogy leszabályozza a Ha a replikációs forgalmat az nva-n keresztül halad. Ugyanez vonatkozik a alapértelmezett útvonalak használata esetén az összes Azure-beli Virtuálisgép-forgalom útválasztási helyszíni üzemelő példányokhoz is. Javasoljuk, hogy [egy virtuális hálózati szolgáltatásvégpont létrehozása](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózatot "Tároló" úgy, hogy a replikálás forgalma nem hagyja el az Azure-határ.

## <a name="failover-models-with-expressroute"></a>Az expressroute-tal feladatátvételi modellek

Ha az Azure-beli virtuális gépek vannak a feladatátvétel egy másik régióba a meglévő ExpressRoute-kapcsolat a forrás virtuális hálózathoz nem kerül automatikusan át a célként megadott virtuális hálózathoz a helyreállítási régióban. Új kapcsolat ExpressRoute kapcsolódni a cél virtuális hálózat szükséges.

Az Azure virtual machines leírtaknak megfelelően földrajzi ugyanazon a fürtön belüli Azure-régiókba replikálhatja [Itt](azure-to-azure-support-matrix.md#region-support). Ha a kiválasztott cél Azure-régió nem forrásaként ugyanazon geopolitikai régión belül, az ExpressRoute Premium engedélyezéséhez, ha egyetlen ExpressRoute-kapcsolatcsoportot használ a forrás- és régióban történő kapcsolódáshoz szüksége. További részletekért ellenőrizze [ExpressRoute-helyek](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) és [ExpressRoute – díjszabás](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Két különböző helyen ExpressRoute társviszony-létesítés két ExpressRoute-Kapcsolatcsoportok
-   Ez a konfiguráció akkor hasznos, ha azt szeretné, annak érdekében, az elsődleges ExpressRoute-kapcsolatcsoport meghibásodása elleni és csapásoktól nagyméretű regionális, amely sikerült is hatással van az ExpressRoute-társviszony-létesítési helyszínek és akadályozza az elsődleges ExpressRoute-kapcsolatcsoport.
-   Általában a kapcsolatcsoportot, az éles környezet csatlakozik az elsődleges kapcsolatcsoportot, és a másodlagos kapcsolatcsoportot egy failsafe, és általában alacsony sávszélességű használt. A sávszélesség a másodlagos vész-helyreállítási esemény, ha a másodlagos kell átvenni az elsődleges növelhető.
-   Ez a konfiguráció létesíthet kapcsolatot a cél virtuális hálózattal a másodlagos ExpressRoute-kapcsolatcsoport feladatátvétel után, vagy a kapcsolat létrejötte és készen áll a vészhelyreállítási határozza meg, a teljes helyreállítási idő csökkentése. Az egyidejű kapcsolatok mind az elsődleges és a célrégió virtuális hálózatok, győződjön meg arról, hogy a helyi útválasztást használ-e a másodlagos kapcsolatcsoportot, és csak a feladatátvétel után a kapcsolat.
-   A forrás- és virtuális hálózatok, a Site Recovery által védett virtuális gépek lehetnek azonos vagy eltérő IP-címeket a követelmény egy feladatátvétel során. Mindkét esetben a másodlagos kapcsolatok hozhatók létre a feladatátvétel előtt.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Két, ugyanazon a helyen ExpressRoute társviszony-létesítési ExpressRoute-Kapcsolatcsoportok
-   Ezzel a konfigurációval, az elsődleges ExpressRoute-kapcsolatcsoport meghibásodása elleni is biztosítja, de nem nagyméretű regionális csapásoktól, ami hatással lehet az ExpressRoute-társviszony-létesítési helyszínek. Az utóbbi az elsődleges és másodlagos kapcsolatok lekérése negatív.
-   Az IP-címek és a kapcsolatokhoz a feltételek ugyanazok, mint a korábbi esetben továbbra is. Egyidejű kapcsolatok a helyszíni adatközpontból a forrás virtuális hálózatba a az elsődleges kapcsolatcsoportot, és a cél virtuális hálózatba a a másodlagos kapcsolatcsoportot is rendelkezhet. Az egyidejű kapcsolatok mind az elsődleges és a célrégió virtuális hálózatok, győződjön meg arról, hogy a helyi útválasztást használ-e a másodlagos kapcsolatcsoportot, és csak a feladatátvétel után a kapcsolat.
-   Kapcsolatcsoportok társviszony-létesítési ugyanarról a helyről létrehozásakor mindkét Kapcsolatcsoportok nem tud kapcsolódni az azonos virtuális hálózatban.

### <a name="single-expressroute-circuit"></a>Egyetlen ExpressRoute-kapcsolatcsoport
-   Ez a konfiguráció nem biztosítja a nagy méretű regionális katasztrófa, ami hatással lehet az ExpressRoute-társviszony-létesítési helyszínen ellen.
-   Egyetlen ExpressRoute-kapcsolatcsoport lehet csatlakozni a forrás és cél virtuális hálózatok egyidejűleg kapcsolatcsoport a ugyanazon IP-címterület használata a célrégióban a.
-   A címtér a célrégióban a használata esetén a forrás ügyféloldali kapcsolatot meg bontja, és a cél ügyféloldali kapcsolatot létrehozni, azt követően. A helyreállítási terv részeként a kapcsolatot change parancsfájlalapú lehet.
-   A regionális meghibásodással Ha az elsődleges régióban nem érhető el, a leválasztás sikerült sikertelen. A cél virtuális hálózattal azonos IP-címtér használatakor-beli szolgáltatáskimaradás hatással lehet a célrégióban a kapcsolat létrehozása.
-   Ha a kapcsolat létrehozás sikeresen befejeződik, a célon, és később helyreállítja az elsődleges régióban, ha két egyidejű kapcsolatok próbál kapcsolódni az azonos címtartomány csomag csepp is között. Csomag csepp megelőzése érdekében az elsődleges kapcsolati azonnal meg kell szüntetni. POST feladat-visszavételt a virtuális gépek az elsődleges régió újra az elsődleges kapcsolat is létesíthető a másodlagos kapcsolati kapcsolatának bontása után.
-   Eltérő a cél virtuális hálózat használata esetén majd is egyszerre csatlakozzon a forrás és cél virtuális hálózatok ugyanabban az ExpressRoute-kapcsolatcsoportból.

## <a name="recovering-azure-deployments"></a>Azure-környezetek helyreállítása
Fontolja meg a két különböző ExpressRoute-Kapcsolatcsoportok két másik társviszony-létesítési helyszínek, valamint a védett Azure-beli virtuális gépek magánhálózati IP-címek megőrzési feladatátvételi modell. A helyreállítási célrégióban Azure Délkelet-Ázsia, és egy ExpressRoute-kapcsolatcsoport másodlagos kapcsolati létrejön egy fiókpartner peremhálózati a szingapúri keresztül.

Helyreállítási replikáló virtuális gépek és virtuális hálózatok mellett a teljes üzembe helyezés automatizálásához más releváns hálózati erőforrások és a kapcsolatokat is létre kell hozni. A korábbi küllős hálózati topológia a következő további lépéseket kell elvégezni, közben vagy után a [feladatátvételi](azure-to-azure-tutorial-failover-failback.md) műveletet:
1.  A célként megadott régióban hub virtuális hálózat létrehozása az Azure ExpressRoute-átjárót. Az ExpressRoute-átjárót a cél-központ virtuális hálózat csatlakoztatása az ExpressRoute-kapcsolatcsoport szükséges.
2.  Hozzon létre a virtuális hálózati kapcsolat a célként megadott központi virtuális hálózaton a cél ExpressRoute-kapcsolatcsoport között.
3.  Állítsa be a virtuális hálózatok közötti társviszony-létesítéseket a célrégióban hub és a küllő virtuális hálózatok között. A társviszony-létesítés tulajdonságait a célként megadott régióban lesznek ugyanazok, mint a forrásrégióban található.
4.  Az udr-eket az agyi virtuális hálózat és a két küllő virtuális hálózatok beállítása. A tulajdonságok a céloldalon az udr-EK esetén ugyanazok, mint a forrás oldalon az azonos IP-címekkel. Másik céloldali IP-címmel az udr-EK megfelelően módosítani kell.

A fenti lépések részeként parancsfájlalapú lehet egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Attól függően, az alkalmazás kapcsolat és a helyreállítási időre vonatkozó követelmények a fenti lépéseket is is elvégezhető a feladatátvétel indítása előtt.

A virtuális gépek helyreállítását és a többi kapcsolat lépés befejezése után, a helyreállítási környezetet a következőképpen néz ki: ![a-helyszíni – Azure feladatátvétel után az ExpressRoute használatával](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Egy egyszerű topológiát példában egyetlen ExpressRoute-kapcsolatcsoport a cél virtuális gépek, azonos IP-címmel rendelkező Azure virtuális gép vész-helyreállítási részletes [Itt](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>A helyreállítási időre vonatkozó célkitűzés (RTO) kapcsolatos szempontok
Az üzembe helyezéshez a teljes helyreállítási idő csökkentése érdekében javasoljuk, hogy kiépítés és üzembe helyezése a további célrégióban [hálózati összetevők](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) például a virtuális hálózati átjárók előre. Egy kis állásidő társított további erőforrások üzembe helyezése, és az üzemszünet hatással lehet a teljes helyreállítási idő, ha nem, vegye figyelembe a tervezés során.

Javasoljuk, hogy rendszeresen futó [vészhelyreállítási próbák](azure-to-azure-tutorial-dr-drill.md) a védett központi telepítések. A próba ellenőrzi a replikációs stratégiáját, adatveszteség és állásidő nélkül, és nem érinti az éles környezetben. Utolsó pillanatban konfigurációs problémákat, amelyek hátrányosan befolyásolhatja a helyreállítási időre vonatkozó célkitűzést is fut a próba elkerülhető. A teszt feladatátvételhez, amely be lett állítva, ha engedélyezte a replikációt az alapértelmezett hálózat helyett egy külön Azure Virtuálisgép-hálózat használatát javasoljuk.

Egyetlen ExpressRoute-kapcsolatcsoportot használ, ha regionális katasztrófa során kapcsolat kialakítása problémák elkerülése érdekében a cél virtuális hálózat egy másik IP-címtér használatát javasoljuk. Ha másik IP-címek használata nem megvalósítható az helyreállított éles környezetben, a különböző IP-címek külön vizsgálat hálózat a katasztrófa utáni helyreállítás részletes feladatátvételi tesztet kell elvégezni, mivel a két virtuális hálózat összekapcsolása nem átfedő IP címtér ExpressRoute-kapcsolatcsoporthoz.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [ExpressRoute-Kapcsolatcsoportok](../expressroute/expressroute-circuit-peerings.md).
- Tudjon meg többet [ExpressRoute útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Tudjon meg többet [ExpressRoute-helyek](../expressroute/expressroute-locations.md).
- Tudjon meg többet [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
