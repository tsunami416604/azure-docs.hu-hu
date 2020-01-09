---
title: Általános kérdések a Azure Site Recovery szolgáltatással kapcsolatban
description: Ez a cikk a Azure Site Recoveryekkel kapcsolatos népszerű általános kérdéseket tárgyalja.
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: f64b885e82d2f790d7d146e16bb6ccb44e207465
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497533"
---
# <a name="general-questions-about-azure-site-recovery"></a>Általános kérdések a Azure Site Recovery

Ez a cikk a Azure Site Recoveryokkal kapcsolatos gyakori kérdéseket foglalja össze. Adott forgatókönyvek esetében tekintse át ezeket a cikkeket

- [Kérdések az Azure-beli virtuális gépek vész-helyreállításáról az Azure-ba](azure-to-azure-common-questions.md)
- [A VMware virtuális gép vész-helyreállításával kapcsolatos kérdések az Azure-ba](vmware-azure-common-questions.md)
- [Kérdések a Hyper-V virtuális gép vész-helyreállításáról az Azure-ba](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Általános

### <a name="what-does-site-recovery-do"></a>Mire való a Site Recovery?
Site Recovery hozzájárul az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához az Azure-beli virtuális gépek régiók, helyszíni virtuális gépek és fizikai kiszolgálók közötti, az Azure-ba, illetve a helyszíni gépek közötti replikációjának összehangolásával és automatizálásával másodlagos adatközpont. [További információk](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Biztosítható a Docker-lemezzel rendelkező virtuális gépek elleni védelem?

Nem, ez egy nem támogatott forgatókönyv.

## <a name="service-providers"></a>Szolgáltatók

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Szolgáltató vagyok. Működik Site Recovery a dedikált és közös infrastruktúra-modellekhez?
Igen, a Site Recovery támogatja mind a dedikált, mind a megosztott infrastruktúra-modelleket.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>A szolgáltatónál a bérlő identitása a Site Recovery szolgáltatással közösen van megosztva?
Nem. A bérlői identitás névtelen marad. A bérlőnek nincs szüksége a Site Recovery-portál elérésére. Csak a szolgáltatást nyújtó rendszergazdája kommunikál a portállal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>A bérlői alkalmazásadatok még mindig az Azure-ba kerülnek?
Ha a replikáció a szolgáltató által birtokolt helyek között történik, az alkalmazásadatok soha nem kerülnek az Azure-ba. Az adatforgalom átvitel alatt áll, és közvetlenül replikálódik a szolgáltatói helyek között.

Ha az Azure-ba replikál, az alkalmazásadatok Azure-tárterületre kerülnek, a Site Recovery szolgáltatáshoz azonban nem jutnak el. Az adatforgalom átvitel alatt áll, és az Azure-ban is titkosítva marad.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Kapnak a bérlőim számlát valamilyen Azure-szolgáltatásról?
Nem. Az Azure közvetlenül a szolgáltatóval áll számlázási kapcsolatban. A bérlők felé történő számlázásért a szolgáltató felel.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Ha az Azure-ba replikálok, szükséges a virtuális gépeket folyamatosan az Azure-ban futtatni?
Nem, az Azure Storage-ba replikálja az adatait az előfizetésében. Ha feladatátvételi tesztet (vészhelyreállítási gyakorlatot) vagy tényleges feladatátvételt végez, a Site Recovery automatikusan létrehozza a virtuális gépeket az előfizetéséhez.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Elérhető bérlő szintű elkülönítés az Azure-ba való replikációnál?
Igen.

### <a name="what-platforms-do-you-currently-support"></a>Jelenleg milyen platformok támogatottak?
Támogatjuk az Azure Pack, a Cloud platform System és a System Center-alapú (2012-es és újabb) üzembe helyezést. [További](https://technet.microsoft.com/library/dn850370.aspx) információ az Azure Pack és a site Recovery integrációról.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Támogatott az egyetlen Azure Pack-re és az egyetlen VMM-kiszolgálóra alapuló üzembe helyezési modell?
Igen, a Hyper-V virtuális gépek replikálása az Azure-ba vagy a szolgáltatói helyek között végezhető el.  Vegye figyelembe, hogy ha a szolgáltatói helyek között replikál, az Azure runbook-integráció nem érhető el.

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-pricing-information"></a>Hol találhatok díjszabási információkat?
Tekintse át [site Recovery díjszabásának](https://azure.microsoft.com/pricing/details/site-recovery/) részleteit.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hogyan számítható ki a becsült díjak a Site Recovery használata során?

A [díjszabási számológép](https://aka.ms/asr_pricing_calculator) használatával megbecsülheti a költségeket site Recovery használata közben.

A költségek részletes becsléséhez futtassa a Deployment Planner eszközt a [VMware](https://aka.ms/siterecovery_deployment_planner) -hez vagy a [Hyper-V-](https://aka.ms/asr-deployment-planner)hez, és használja a [Cost becslése jelentést](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>A felügyelt lemezek mostantól a VMware virtuális gépek és a fizikai kiszolgálók replikálására szolgálnak. Felmerülhetek-e további díjak a gyorsítótárbeli Storage-fiókhoz a felügyelt lemezekkel?

Nem, a gyorsítótárra vonatkozóan nem számítunk fel további díjakat. A standard Storage-fiókba való replikáláskor a gyorsítótár tárolója ugyanahhoz a célként megadott Storage-fiókhoz tartozik.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Már több mint egy hónapja Azure Site Recovery-felhasználó vagyok. Továbbra is minden egyes védett példány esetében vonatkozik rám a díjmentes első 31 nap?

Igen. Az első 31 napra egyetlen védett példány tekintetében sem számítunk fel Azure Site Recovery-díjat. Ha például az elmúlt 6 hónap során 10 példányt védett, és egy 11. példányt hoz Azure Site Recoveryhoz, akkor a 11. példányra az első 31 nap során nem számítunk fel díjat. Az első 10 példány továbbra is Azure Site Recovery díjat von maga után, mert több mint 31 napja volt védetté tenni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Az első 31 nap során kell valamilyen más Azure-díjat fizetni?

Igen, annak ellenére, hogy Site Recovery a védett példányok első 31 napján ingyenes, díjköteles lehet az Azure Storage, a tárolási tranzakciók és az adatátvitelek esetében. Továbbá a helyreállított virtuális gépekre is vonatkozhatnak Azure-díjak a számítási idő alapján.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Van olyan díja, amely vész-helyreállítási részletezést vagy feladatátvételi tesztet hajt végre?

A DR működéshez nincs külön díj. A virtuális gép a feladatátvételi teszt után létrejött után számítunk fel díjat.



## <a name="security"></a>Biztonság

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, Site Recovery nem metszi a replikált adatokat, és nem rendelkezik információval arról, hogy mi fut a virtuális gépeken vagy a fizikai kiszolgálókon.
A replikációs adatcsere a helyszíni Hyper-V gazdagépek, a VMware hipervizorok vagy fizikai kiszolgálók és az Azure tárolási szolgáltatás között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

A Site Recovery ISO 27001:2013, 27018, HIPAA, DPA tanúsítvánnyal rendelkezik, és a SOC2 és a FedRAMP ÜSS értékelésének folyamata folyamatban van.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>A megfelelőségi okokból még a helyszíni metaadatoknak is ugyanabban a földrajzi régióban kell maradniuk. Segíthet Site Recovery?
Igen. Amikor létrehoz egy Site Recovery tárolót egy régióban, biztosítjuk, hogy a replikáció és a feladatátvétel engedélyezéséhez és előkészítéséhez szükséges metaadatok a régió földrajzi határain belül maradnak.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
A virtuális gépek és a fizikai kiszolgálók esetében a replikálása a helyszíni helyek között a titkosítás-átvitel során támogatott. Az Azure-ba replikált virtuális gépek és fizikai kiszolgálók esetében a titkosítást és a [titkosítást](https://docs.microsoft.com/azure/storage/storage-service-encryption) is támogatja az Azure-ban.




## <a name="disaster-recovery"></a>Vészhelyreállítás

### <a name="what-can-site-recovery-protect"></a>Mit lehet Site Recovery védelemmel ellátni?
* **Azure-beli virtuális gépek**: site Recovery a támogatott Azure-beli virtuális gépen futó bármilyen számítási feladatot replikálhat
* **Hyper-v virtuális gépek**: a site Recovery képes a Hyper-v virtuális gépen futó bármilyen munkaterhelés elleni védelemre.
* **Fizikai kiszolgálók**: site Recovery a Windows vagy Linux rendszerű fizikai kiszolgálókat is képes védelemmel ellátni.
* **VMware Virtual Machines**: a site Recovery a VMWare virtuális gépeken futó munkaterhelések elleni védelemre is alkalmas.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Milyen számítási feladatokat tud védeni a Site Recovery?
A Site Recovery használatával biztosíthatja a legtöbb, támogatott virtuális gépen vagy fizikai kiszolgálón futó számítási feladatot. Site Recovery támogatja az Application-Aware replikálást, így az alkalmazások intelligens állapotba állíthatók helyre. Integrálható a Microsoft-alkalmazásokkal, például a SharePoint, az Exchange, a Dynamics, a SQL Server és a Active Directoryval, és szorosan együttműködik a vezető szállítókkal, például az Oracle, az SAP, az IBM és a Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Elérhető a vészhelyreállítás a fiókirodák számára a Site Recoveryvel?
Igen. Ha a fiókirodákban a replikáció és a feladatátvétel összekapcsolásához Site Recovery használ, az összes fiókirodai munkaterhelést egy központi helyen fogja megtekinteni. A központi telephelyről könnyen intézheti a feladatátvételt és a vészhelyreállítást az összes fiókiroda tekintetében anélkül, hogy el kellene látogatnia a fiókirodák telephelyeire.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Az Azure-beli virtuális gépek esetében támogatott a vész-helyreállítási szolgáltatás?

Igen, Site Recovery támogatja az Azure-beli virtuális gépek Azure-régiók közötti katasztrófáját. [Tekintse át](azure-to-azure-common-questions.md) az Azure virtuális gépek vész-helyreállítási szolgáltatásával kapcsolatos gyakori kérdéseket.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Támogatott-e a vész-helyreállítási szolgáltatás a VMware virtuális gépeken?

Igen, Site Recovery támogatja a helyszíni VMware virtuális gépek vész-helyreállítását. [Tekintse át](vmware-azure-common-questions.md) a VMWare virtuális gépek vész-helyreállításával kapcsolatos gyakori kérdéseket.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Támogatott-e a vész-helyreállítási szolgáltatás a Hyper-V virtuális gépeken?
Igen, Site Recovery támogatja a helyszíni Hyper-V virtuális gépek vész-helyreállítását. [Tekintse át](hyper-v-azure-common-questions.md) a Hyper-V virtuális gépek vész-helyreállításával kapcsolatos gyakori kérdéseket.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>A vész-helyreállítási támogatott a fizikai kiszolgálókon?
Igen, Site Recovery támogatja a Windows és Linux rendszerű helyszíni fizikai kiszolgálók vész-helyreállítását az Azure-ba vagy egy másodlagos helyre. Ismerje meg az [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)-ba irányuló vész-helyreállítási követelményeket és[a másodlagos helyet](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Vegye figyelembe, hogy a fizikai kiszolgálók az Azure-beli virtuális gépekként fognak futni a feladatátvétel után. Az Azure-ból a helyszíni fizikai kiszolgálóra történő feladat-visszavétel jelenleg nem támogatott. Csak egy VMware virtuális géphez lehet visszaadni.





## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Replikálható helyek közötti VPN-ről az Azure-ba?
Azure Site Recovery replikálja az Azure Storage-fiókba vagy a felügyelt lemezekre egy nyilvános végponton keresztül. A replikáció nem haladja meg a helyek közötti VPN-t. 

### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem lehet replikálni VPN-en keresztül?

Az Azure-ba való replikáláskor a replikálási forgalom eléri az Azure Storage nyilvános végpontját. Így csak a nyilvános interneten keresztül replikálhat ExpressRoute (Microsoft-társ vagy meglévő nyilvános társ), és a VPN nem működik.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Használhatom a Riverbed-SteelHeads a replikáláshoz?

Partnerünk, Riverbed, részletes útmutatást nyújt a Azure Site Recovery használatáról. Tekintse át a [megoldási útmutatót](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Használhatom a ExpressRoute a virtuális gépek Azure-ba történő replikálásához?
Igen, a [ExpressRoute](concepts-expressroute-with-site-recovery.md) használatával replikálhatja a helyszíni virtuális gépeket az Azure-ba.

- Azure Site Recovery replikálja az Azure Storage-ba egy nyilvános végponton keresztül. Be kell állítania a [Microsoft-társat](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) , vagy egy meglévő [nyilvános](../expressroute/about-public-peering.md) (új áramköröknél elavult) kapcsolatot kell használnia a ExpressRoute site Recovery replikációhoz való használatához.
- A replikációhoz a Microsoft-társ a javasolt útválasztási tartomány.
- A replikáció nem támogatott privát társak esetén.
- Ha a VMware-gépeket vagy fizikai gépeket védi, győződjön meg arról, hogy a konfigurációs kiszolgáló [hálózati követelményei](vmware-azure-configuration-server-requirements.md#network-requirements) is teljesülnek. A konfigurációs kiszolgáló a Site Recovery replikáció összehangolása érdekében meghatározott URL-címekhez való kapcsolódást igényel. A ExpressRoute nem használható ehhez a kapcsolathoz.
- Miután a virtuális gépeket átadta egy Azure-beli virtuális hálózatnak, elérheti őket az Azure Virtual Network szolgáltatással elérhető [privát](../expressroute/expressroute-circuit-peerings.md#privatepeering) társítási beállítással.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Ha az Azure-ba replikálok, milyen típusú Storage-fiókra vagy felügyelt lemezre van szükségem?

Szüksége van egy LRS vagy egy GRS-tárolóra. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. A Premium Storage szolgáltatás a VMware virtuális gépek, a Hyper-V virtuális gépek és a fizikai kiszolgálók replikálásához használható, ha a Site Recoveryt a Azure Portal telepíti. A felügyelt lemezek csak a LRS támogatják.

### <a name="how-often-can-i-replicate-data"></a>Milyen gyakran replikálhatom az adatokat?
* **Hyper-V:** A Hyper-V virtuális gépek 30 másodpercenként replikálhatók (kivéve a Premium Storage esetében), öt perc vagy 15 perc.
* **Azure-beli virtuális gépek, VMWare virtuális gépek, fizikai kiszolgálók:** A replikálási gyakoriság itt nem releváns. A replikáció folyamatos.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Ki lehet terjeszteni a replikálást a meglévő helyreállítási helyről egy másik harmadlagos helyre?
A kiterjesztett vagy láncolt replikáció nem támogatott. Kérje ezt a funkciót a [visszajelzési fórumokban](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Végezhetek offline replikációt, amikor első alkalommal replikálok Azure-ba?
Ez a funkció nem támogatott. Kérje ezt a szolgáltatást a [visszajelzési fórumba](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kizárhatok a replikációból bizonyos lemezeket?
Ez akkor támogatott, ha VMware virtuális gépeket és Hyper-V virtuális gépeket replikál az Azure-ba a Azure Portal használatával.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Replikálható a virtuális gépek dinamikus lemezek használatával?
A dinamikus lemezek a Hyper-V rendszerű virtuális gépek replikálásakor és a VMware virtuális gépek és fizikai gépek Azure-ba történő replikálásakor támogatottak. Az operációs rendszer lemezének alapszintű lemeznek kell lennie.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Szabályozható a replikálási forgalom számára kiosztott sávszélesség?
Igen. A sávszélesség szabályozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [A VMware virtuális gépek és fizikai kiszolgálók replikálásának kapacitásának megtervezése](site-recovery-plan-capacity-vmware.md)
* [A Hyper-V virtuális gépek Azure-ba történő replikálásának kapacitásának megtervezése](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Feladatátvétel
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Ha az Azure-ban nem végeztem el az Azure-t, hogyan férhetnek hozzá az Azure-beli virtuális gépekhez a feladatátvétel után?

Az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en keresztül, vagy Azure ExpressRoute segítségével érheti el. A csatlakozáshoz több dolgot is elő kell készíteni. [További információk](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Ha az Azure-t átadja az Azure-nak, hogyan gondoskodik róla, hogy az adataim rugalmasak legyenek?
Az Azure-t hibatűrőnek terveztük. A Site Recovery már a másodlagos Azure-adatközpontba történő feladatátvételre lett tervezve, az Azure SLA-val összhangban. Ha ez történik, győződjön meg róla, hogy a metaadatok és a tárolók ugyanabban a földrajzi régióban maradnak, amelyet a tárolóhoz választott.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ha két adatközpont között replikálok, mi történik, ha az elsődleges adatközpont váratlan kimaradást tapasztal?
Elindíthat egy nem tervezett feladatátvételt a másodlagos helyről. A Site Recovery nem követeli meg a feladatátvétel végrehajtásához, hogy az elsődleges helyről kapcsolódjon.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
A feladatátvétel nem automatikus. A feladatátvételt egyetlen kattintással kezdeményezheti a portálon, vagy a [site Recovery PowerShell](/powershell/module/az.recoveryservices) használatával is elindíthat feladatátvételt. A feladat-visszavétel egy egyszerű művelet a Site Recovery-portálon.

A helyszíni Orchestrator vagy Operations Manager segítségével automatizálhatja a virtuális gép hibáit, majd a feladatátvételt az SDK használatával aktiválhatja.

* [További](site-recovery-create-recovery-plans.md) információ a helyreállítási tervekről.
* [További](site-recovery-failover.md) információ a feladatátvételről.
* [További](site-recovery-failback-azure-to-vmware.md) információ a VMWare virtuális gépek és a fizikai kiszolgálók meghibásodásáról

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Ha a helyszíni gazdagép nem válaszol vagy összeomlott, Visszatérhetek egy másik gazdagépre?
Igen, a másik helyre történő helyreállítást használhatja a feladat-visszavételhez egy másik gazdagépre az Azure-ból.

* [VMware virtuális gépekhez](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V rendszerű virtuális gépek esetén](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálható Site Recovery forgatókönyvek SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. Jelenleg támogatott forgatókönyvek Site Recovery üzembe helyezéséhez a PowerShell használatával:

* [VMMs-felhőkben lévő Hyper-V virtuális gépek replikálása a Resource Manager Azure PowerShell](hyper-v-vmm-powershell-resource-manager.md)
* [Hyper-V virtuális gépek replikálása VMM nélkül Azure PowerShell Resource Managerrel](hyper-v-azure-powershell-resource-manager.md)
* [VMware replikálása az Azure-ba a PowerShell Resource Managerrel](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Összetevő/szolgáltató frissítése

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Hol találhatók a Site Recovery frissítéseinek kibocsátási megjegyzései/kumulatív frissítései

[Ismerje meg](site-recovery-whats-new.md) az új frissítéseket, és szerezze be a [kumulatív információkat](service-updates-how-to.md).

## <a name="next-steps"></a>Következő lépések
* Olvassa el a [Site Recovery áttekintését](site-recovery-overview.md)

