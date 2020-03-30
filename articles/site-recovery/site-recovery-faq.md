---
title: Általános kérdések az Azure Site Recovery szolgáltatással kapcsolatban
description: Ez a cikk az Azure Site Recovery általános kérdéseit ismerteti.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257679"
---
# <a name="general-questions-about-azure-site-recovery"></a>Általános kérdések az Azure Site Recovery szolgáltatással kapcsolatban

Ez a cikk összefoglalja az Azure Site Recovery szolgáltatással kapcsolatos gyakori kérdéseket. A konkrét esetekben tekintse át ezeket a cikkeket

- [Kérdések az Azure virtuális gép azure-beli vész-helyreállítási](azure-to-azure-common-questions.md)
- [Kérdések a VMware VM vész-helyreállítási azure-ba](vmware-azure-common-questions.md)
- [Kérdések a Hyper-V virtuális gép azure-beli vész-helyreállítási](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Általános kérdések

### <a name="what-does-site-recovery-do"></a>Mire való a Site Recovery?
A Site Recovery hozzájárul az üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiához azáltal, hogy az Azure virtuális gépek régiók közötti replikációját, a helyszíni virtuális gépeket és a fizikai kiszolgálókat az Azure-ba, valamint a helyszíni gépeket egy másodlagos adatközpontba. [További információ](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Megvédhetem a Docker-lemezzel rendelkező virtuális gépet?

Nem, ez egy nem támogatott forgatókönyv.

## <a name="service-providers"></a>Szolgáltatók

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Szolgáltató vagyok. Működik a Site Recovery dedikált és megosztott infrastruktúra-modellek esetén?
Igen, a Site Recovery támogatja mind a dedikált, mind a megosztott infrastruktúra-modelleket.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Egy szolgáltató esetében a bérlőm identitása meg van osztva a Site Recovery szolgáltatással?
Nem. A bérlőidentitás névtelen marad. A bérlőnek nincs szüksége a Site Recovery-portál elérésére. Csak a szolgáltatást nyújtó rendszergazdája kommunikál a portállal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>A bérlői alkalmazás adatai valaha is az Azure-ba kerülnek?
Ha a replikáció a szolgáltató által birtokolt helyek között történik, az alkalmazásadatok soha nem kerülnek az Azure-ba. Az adatok átvitel közben titkosítva vannak, és közvetlenül replikálódnak a szolgáltatói helyek között.

Ha az Azure-ba replikál, az alkalmazásadatok Azure-tárterületre kerülnek, a Site Recovery szolgáltatáshoz azonban nem jutnak el. Az adatok átvitel közben titkosítva vannak, és az Azure-ban is titkosítva maradnak.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Kapnak a bérlőim számlát valamilyen Azure-szolgáltatásról?
Nem. Az Azure közvetlenül a szolgáltatóval áll számlázási kapcsolatban. A bérlők felé történő számlázásért a szolgáltató felel.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Ha az Azure-ba replikálok, szükséges a virtuális gépeket folyamatosan az Azure-ban futtatni?
Nem, az adatok replikálódnak az Azure storage-ba az előfizetésben. Ha feladatátvételi tesztet (vészhelyreállítási gyakorlatot) vagy tényleges feladatátvételt végez, a Site Recovery automatikusan létrehozza a virtuális gépeket az előfizetéséhez.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Elérhető bérlő szintű elkülönítés az Azure-ba való replikációnál?
Igen.

### <a name="what-platforms-do-you-currently-support"></a>Jelenleg milyen platformok támogatottak?
Támogatjuk az Azure Pack, a Cloud Platform System és a System Center alapú (2012-es és újabb) telepítéseket. [További információ](https://technet.microsoft.com/library/dn850370.aspx) az Azure Pack és a Site Recovery integrációjáról.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Támogatott az egyetlen Azure Pack-re és az egyetlen VMM-kiszolgálóra alapuló üzembe helyezési modell?
Igen, a Hyper-V virtuális gépek replikálhatók az Azure-ba vagy a szolgáltatóhelyek között.  Vegye figyelembe, hogy ha replikálja a szolgáltató helyek között, az Azure Runbook-integráció nem érhető el.

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-pricing-information"></a>Hol találhatok árinformációkat?
Tekintse át [a Webhely-helyreállítási árképzési részleteket.](https://azure.microsoft.com/pricing/details/site-recovery/)


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hogyan számíthatom ki a hozzávetőleges díjakat a Site Recovery használata során?

A [díjkalkulátor](https://aka.ms/asr_pricing_calculator) segítségével megbecsülheti a költségeket a Site Recovery használata közben.

A költségek részletes becsléséhez futtassa a [VMware](https://aka.ms/siterecovery_deployment_planner) vagy a [Hyper-V](https://aka.ms/asr-deployment-planner)üzembe helyezéstervező eszközét, és használja a [költségbecslési jelentést.](https://aka.ms/asr_DP_costreport)


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>A felügyelt lemezek mostantól vmware virtuális gépek és fizikai kiszolgálók replikálására szolgálnak. További díjakmerülnek fel a felügyelt lemezekkel rendelkező gyorsítótár-tárfiókért?

Nem, a gyorsítótárért nem kell további díjat fizetni. A szabványos tárfiókra replikáláskor ez a gyorsítótár-tár ugyanannak a céltárfióknak a része.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Több mint egy hónapja azure site recovery felhasználó vagyok. Továbbra is ingyenesen kapom az első 31 napot minden védett példányesetében?

Igen. Minden védett példány nem jár Azure Site Recovery díjak az első 31 nap. Ha például az elmúlt 6 hónapban 10 példányt védett, és egy 11. Az első 10 példány továbbra is azure site recovery díjakat, mivel már több mint 31 napig védett.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Az első 31 napban felmerülnek-e egyéb Azure-díjak?

Igen, annak ellenére, hogy a Site Recovery ingyenes a védett példány első 31 napjában, díjat felkérhet az Azure Storage- és tárolási tranzakciókért és az adatátvitelért. A helyreállított virtuális gép azure-számítási díjakat is felszámíthat.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Van-e költség a vész-helyreállítási gyakorlatok végrehajtásához/a feladatátvételhez?

A VÉSZ-fúrónak nincs külön költsége. A tesztfeladat-átvétel után a virtuális gép létrehozása után számítási díjak lesznek.



## <a name="security"></a>Biztonság

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, a Site Recovery nem fogja el a replikált adatokat, és nem rendelkezik semmilyen információt arról, hogy mi fut a virtuális gépeken vagy a fizikai kiszolgálókon.
A replikációs adatcsere a helyszíni Hyper-V gazdagépek, a VMware hipervizorok vagy fizikai kiszolgálók és az Azure tárolási szolgáltatás között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

Site Recovery ISO 27001:2013, 27018, HIPAA, DPA tanúsítvánnyal rendelkezik, és folyamatban van az SOC2 és A FedRAMP JAB értékelések.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Megfelelőségi okokból még a helyszíni metaadatainknak is ugyanabban a földrajzi régióban kell maradniuk. Segíthet nekünk a Site Recovery?
Igen. Amikor létrehoz egy hely-helyreállítási tárolót egy régióban, biztosítjuk, hogy a replikáció engedélyezéséhez és vezényléséhez szükséges összes metaadat az adott régió földrajzi határán belül maradjon.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Virtuális gépek és fizikai kiszolgálók esetén a helyszíni helyek közötti replikálás támogatott. Az Azure-ba replikáló virtuális gépek és fizikai kiszolgálók esetében mind a titkosítás átvitel e közbeni, mind [az inaktív titkosítás (az Azure-ban)](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>Hogyan kényszeríthetem a TLS 1.2-t az Azure Site Recovery összes összetevőjére?
A replikált elemekre telepített mobilitási ügynökök csak a TLS 1.2-es rendszeren kommunikálnak a Process Server kiszolgálóval. A Configuration Server és az Azure közötti kommunikáció, valamint a Process Server és az Azure közötti kommunikáció azonban a TLS 1.1-es vagy 1.0-s kapcsolata lehet. Kérjük, [guidance](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) kövesse a TLS 1.2 érvényesítéséhez az Ön által beállított összes konfigurációs kiszolgálón és folyamatkiszolgálón.


## <a name="disaster-recovery"></a>Vészhelyreállítás

### <a name="what-can-site-recovery-protect"></a>Mit védhet a Site Recovery?
* **Azure virtuális gépek:** A Site Recovery replikálhatja a támogatott Azure virtuális gépen futó munkaterhelést
* **Hyper-V virtuális gépek:** Site Recovery képes megvédeni a Hyper-V virtuális gépen futó számítási feladatok.
* **Fizikai kiszolgálók**: A Site Recovery képes megvédeni a Windows vagy Linux rendszert futtató fizikai kiszolgálókat.
* **VMware virtuális gépek**: Site Recovery képes megvédeni a vmware virtuális gépen futó munkaterhelés.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Milyen számítási feladatokat tud védeni a Site Recovery?
A Site Recovery segítségével megvédheti a támogatott virtuális gépen vagy fizikai kiszolgálón futó legtöbb számítási feladatot. A Site Recovery támogatja az alkalmazásbarát replikációt, így az alkalmazások intelligens állapotba állíthatók. Integrálható a Microsoft alkalmazásaival, például a SharePointtal, az Exchange-szel, a Dynamics-szal, az SQL Serverrel és az Active Directoryval, és szorosan együttműködik a vezető gyártókkal, köztük az Oracle-lel, az SAP-val, az IBM-mel és a Red Hattal. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Elérhető a vészhelyreállítás a fiókirodák számára a Site Recoveryvel?
Igen. Ha a Site Recovery használatával vezényli a replikációt és a feladatátvételt a fiókirodákban, egységes vezénylést kap, és egy központi helyen tekinti meg az összes fiókirodai munkaterhelést. A központi telephelyről könnyen intézheti a feladatátvételt és a vészhelyreállítást az összes fiókiroda tekintetében anélkül, hogy el kellene látogatnia a fiókirodák telephelyeire.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>A vész-helyreállítási támogatja az Azure virtuális gépek?

Igen, a Site Recovery támogatja az Azure-beli virtuális gépek katasztrófáját az Azure-régiók között. Tekintse át az Azure-beli vész-helyreállítási [kapcsolatos gyakori kérdéseket.](azure-to-azure-common-questions.md)

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Támogatja a vészhelyreállítást a VMware virtuális gépek?

Igen, a Site Recovery támogatja a helyszíni VMware virtuális gépek vészhelyreállítását. [Tekintse át](vmware-azure-common-questions.md) a VMware virtuális gépek vész-helyreállítási gyakori kérdéseit.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>A vész-helyreállítási támogatott hyper-V virtuális gépek?
Igen, a Site Recovery támogatja a helyszíni Hyper-V virtuális gépek vészhelyreállítását. [Tekintse át](hyper-v-azure-common-questions.md) a Hyper-V virtuális gépek vész-helyreállítási gyakori kérdéseit.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>A vészhelyreállítás támogatott a fizikai kiszolgálók?
Igen, a Site Recovery támogatja a Windows és Linux rendszert futtató helyszíni fizikai kiszolgálók vészhelyreállítását az Azure-ba vagy egy másodlagos helyre. Ismerje meg az [Azure-ba](vmware-physical-azure-support-matrix.md#replicated-machines)és egy másodlagos helyre irányuló vész-helyreállítási[követelményeket.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
Vegye figyelembe, hogy a fizikai kiszolgálók virtuális gépként fognak futni az Azure-ban a feladatátvétel után. Az Azure-ból egy helyszíni fizikai kiszolgálóra történő feladat-visszavétel jelenleg nem támogatott. Csak a VMware virtuális gépre lehet visszaadni.





## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Replikálható a helyek közötti VPN-en keresztül az Azure-ba?
Az Azure Site Recovery replikálja az adatokat egy Azure-tárfiókba vagy felügyelt lemezekre, nyilvános végponton keresztül. A replikáció nem egy helyek közötti VPN felett van. 

### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem tudok vpn-en keresztül replikálni?

Amikor replikálja az Azure-ba, replikációs forgalom eléri a nyilvános végpontok egy Azure Storage. Így csak a nyilvános interneten vagy az ExpressRoute-on keresztül replikálható (Microsoft társviszony-létesítés vagy meglévő nyilvános társviszony-létesítés).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Használhatom a Riverbed SteelHeads-et replikációra?

Partnerünk, a Riverbed részletes útmutatást nyújt az Azure Site Recovery használatával kapcsolatos munkához. Tekintse át [a megoldásútmutatót.](https://community.riverbed.com/s/article/DOC-4627)

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Használhatom az ExpressRoute-ot virtuális gépek replikálására az Azure-ba?
Igen, [expressroute használható](concepts-expressroute-with-site-recovery.md) replikálni a helyszíni virtuális gépek az Azure-ba.

- Az Azure Site Recovery replikálja az adatokat egy Azure Storage-ba egy nyilvános végponton keresztül. Be kell állítania a [Microsoft társviszony-létesítését,](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) vagy egy meglévő [nyilvános társviszony-létesítést](../expressroute/about-public-peering.md) (új áramkörök esetében elavult) kell használnia az ExpressRoute webhely-helyreállítási replikációhoz való használatához.
- A Microsoft társviszony-létesítésa a replikáció ajánlott útválasztási tartománya.
- A replikáció nem támogatott a privát társviszony-létesítés során.
- Ha vmware-gépeket vagy fizikai gépeket véd, győződjön meg arról, hogy a [konfigurációs](vmware-azure-configuration-server-requirements.md#network-requirements) kiszolgáló hálózati követelményei is teljesülnek. A Configuration Server nek szüksége van az adott URL-címekhez való kapcsolódásra a Site Recovery replikációvoniójának vezényléséhez. Az ExpressRoute nem használható ehhez a kapcsolathoz.
- Miután a virtuális gépek feladatátvételt egy Azure virtuális hálózathoz, elérheti őket az Azure virtuális hálózattal rendelkező [privát társviszony-létesítési](../expressroute/expressroute-circuit-peerings.md#privatepeering) beállítások használatával.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Ha replikálok az Azure-ba, milyen tárfiókra vagy felügyelt lemezre van szükségem?

LRS vagy GRS tárolóra van szüksége. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. Prémium szintű tárolási támogatja a VMware VM, Hyper-VV, és a fizikai kiszolgáló replikáció, amikor telepíti site recovery az Azure Portalon. A felügyelt lemezek csak az LRS-t támogatják.

### <a name="how-often-can-i-replicate-data"></a>Milyen gyakran replikálhatom az adatokat?
* **Hiper-V:** A Hyper-V virtuális gépek 30 másodpercenként replikálhatók (kivéve a prémium szintű tárhelyet), öt perc vagy 15 perc.
* **Azure virtuális gépek, VMware virtuális gépek, fizikai kiszolgálók:** A replikáció gyakorisága itt nem releváns. A replikáció folyamatos.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kiterjeszthetem a replikációt a meglévő helyreállítási helyről egy másik harmadlagos helyre?
A kiterjesztett vagy láncolt replikáció nem támogatott. Kérje ezt a funkciót [a visszajelzés fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Végezhetek offline replikációt, amikor első alkalommal replikálok Azure-ba?
Ez a funkció nem támogatott. Kérje ezt a funkciót a [visszajelzésfórumban](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kizárhatok a replikációból bizonyos lemezeket?
Ez akkor támogatott, ha vmware virtuális gépeket és hyper-vm-eket replikál az Azure-ba az Azure Portalhasználatával.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Replikálható a dinamikus lemezekkel rendelkező virtuális gépek?
A dinamikus lemezek támogatottak a Hyper-V virtuális gépek replikálásakor, valamint a VMware virtuális gépek és a fizikai gépek Azure-ba történő replikálásakor. Az operációs rendszer lemezének alaplemeznek kell lennie.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Szabályozható sávszélesség a replikációs forgalomszámára?
Igen. A sávszélesség szabályozásáról a következő cikkekben olvashat bővebben:

* [Kapacitástervezés vmware virtuális gépek és fizikai kiszolgálók replikálásához](site-recovery-plan-capacity-vmware.md)
* [Kapacitástervezés a Hyper-V virtuális gépek Azure-ba történő replikálására](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Feladatátvétel
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Ha én vagyok a feladatátvétel az Azure-ba, hogyan érheti el az Azure-beli virtuális gépek feladatátvétel után?

Az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en keresztül, vagy Azure ExpressRoute segítségével érheti el. Be kell készíteni egy sor dolgot annak érdekében, hogy csatlakozni. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Ha átteszem a feladatát az Azure-ba, hogyan győződjön meg arról, hogy az adataim rugalmasak?
Az Azure-t hibatűrőnek terveztük. A Site Recovery már tervezték feladatátvétel egy másodlagos Azure-adatközpontba, az Azure SLA-nak megfelelően. Ha ez történik, gondoskodunk arról, hogy a metaadatok és a tárolók ugyanabban a földrajzi régióban maradjanak, amelyet a tárolóhoz választott.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ha két adatközpont között replikálok, mi történik, ha az elsődleges adatközpont váratlan kimaradást tapasztal?
Elindíthat egy nem tervezett feladatátvételt a másodlagos helyről. A Site Recovery nem követeli meg a feladatátvétel végrehajtásához, hogy az elsődleges helyről kapcsolódjon.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
A feladatátvétel nem automatikus. Feladatátvételt kezdeményez egyetlen kattintással a portálon, vagy a [Site Recovery PowerShell](/powershell/module/az.recoveryservices) használatával elindíthatja a feladatátvételt. A visszalépések egyszerű műveleta a Webhely-helyreállítási portálon.

Automatizálni lehet a helyszíni Orchestrator vagy operations manager virtuális gép meghibásodásának észlelésére, majd indítsa el a feladatátvételt az SDK használatával.

* [További információ](site-recovery-create-recovery-plans.md) a helyreállítási tervekről.
* [További információ](site-recovery-failover.md) a feladatátvételről.
* [További információ](site-recovery-failback-azure-to-vmware.md) a VMware virtuális gépek és a fizikai kiszolgálók visszahibáiról

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Ha a helyszíni gazdagép nem válaszol, vagy összeomlott, visszavehetem a feladatát egy másik gazdagépnek?
Igen, használhatja a másik helyen helyreállítási feladat-visszavétel egy másik állomás az Azure-tól.

* [VMware virtuális gépekhez](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V virtuális gépekhez](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálhatom a hely-helyreállítási forgatókönyveket SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. Jelenleg támogatott forgatókönyvek a Site Recovery PowerShell használatával történő üzembe helyezéséhez:

* [Hyper-V virtuális gépek replikálása a virtuális gépgyártók felhőiben az Azure PowerShell-erőforrás-kezelőben](hyper-v-vmm-powershell-resource-manager.md)
* [A Virtuálisgép nélküli Hyper-V virtuális gépek replikálása az Azure PowerShell-erőforrás-kezelőbe](hyper-v-azure-powershell-resource-manager.md)
* [VMware replikálása az Azure-ba a PowerShell Erőforrás-kezelővel](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Összetevő/szolgáltató frissítése

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Hol találom a Site Recovery frissítéseinek kiadási/összesítő információit?

[További információ](site-recovery-whats-new.md) az új frissítésekről, és [az összesítő információk beolvasása](service-updates-how-to.md).

## <a name="next-steps"></a>További lépések
* Olvassa el a [Site Recovery áttekintését](site-recovery-overview.md)

