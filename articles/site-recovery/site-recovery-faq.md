---
title: 'Azure Site Recovery: Gyakori kérdések |} A Microsoft Docs'
description: Ez a cikk ismerteti a gyakori kérdések az Azure Site Recoveryvel kapcsolatos.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/08/2019
ms.author: raynew
ms.openlocfilehash: 824782e54f2cd989f9ab13857d9b894b215fc550
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361363"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Az Azure Site Recovery: gyakori kérdések (GYIK)
Ez a cikk összefoglalja az Azure Site Recovery – gyakori kérdések. 

## <a name="general"></a>Általános kérdések

### <a name="what-does-site-recovery-do"></a>Mire való a Site Recovery?
A Site Recovery megvalósításában az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia, replikálásának munkafolyamatát levezényli és automatizálja az Azure virtuális gépek közötti régiók, a helyszíni virtuális gépek és fizikai kiszolgálók Azure-ba, és a helyszíni gépek egy másodlagos adatközpontba. [További információk](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Egy virtuális gépet, amely egy Docker-lemez lehet védeni?

Nem, ez a nem támogatott forgatókönyv.

## <a name="service-providers"></a>Szolgáltatók

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>A szolgáltató vagyok. Működik a Site Recovery dedikált és megosztott infrastruktúra-modellekkel?
Igen, a Site Recovery támogatja mind a dedikált, mind a megosztott infrastruktúra-modelleket.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Szolgáltató az a bérlőhöz, a Site Recovery szolgáltatással folytatott közös identitásának?
Nem. Bérlői azonosító névtelen marad. A bérlőnek nincs szüksége a Site Recovery-portál elérésére. Csak a szolgáltatást nyújtó rendszergazdája kommunikál a portállal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Fog bérlő alkalmazásainak adatai eljutnak az Azure-bA?
Ha a replikáció a szolgáltató által birtokolt helyek között történik, az alkalmazásadatok soha nem kerülnek az Azure-ba. Az átvitel során, és közvetlenül a szolgáltatói helyek között replikált adatokat is titkosítja.

Ha az Azure-ba replikál, az alkalmazásadatok Azure-tárterületre kerülnek, a Site Recovery szolgáltatáshoz azonban nem jutnak el. Adatok titkosított átvitel, és az Azure-ban is titkosítva maradnak.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Kapnak a bérlőim számlát valamilyen Azure-szolgáltatásról?
Nem. Az Azure közvetlenül a szolgáltatóval áll számlázási kapcsolatban. A bérlők felé történő számlázásért a szolgáltató felel.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Ha az Azure-ba replikálok, szükséges a virtuális gépeket folyamatosan az Azure-ban futtatni?
Nem, az adatok replikációja az Azure storage az előfizetésében. Ha feladatátvételi tesztet (vészhelyreállítási gyakorlatot) vagy tényleges feladatátvételt végez, a Site Recovery automatikusan létrehozza a virtuális gépeket az előfizetéséhez.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Elérhető bérlő szintű elkülönítés az Azure-ba való replikációnál?
Igen.

### <a name="what-platforms-do-you-currently-support"></a>Jelenleg milyen platformok támogatottak?
Támogatott az Azure-csomag, a Cloud Platform System, és a System Center (2012 vagy újabb). [További](https://technet.microsoft.com/library/dn850370.aspx) Azure Pack és a Site Recovery integrációjával kapcsolatban.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Támogatott az egyetlen Azure Pack-re és az egyetlen VMM-kiszolgálóra alapuló üzembe helyezési modell?
Igen, Hyper-V virtuális gépeket replikálhatja az Azure-ba, vagy a szolgáltatói helyek között.  Vegye figyelembe, hogy replikálhat szolgáltatói helyek között, ha Azure-forgatókönyvek integrációja nem érhető el.

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-pricing-information"></a>Hol található a díjszabásról?
Felülvizsgálat [Site Recovery díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/) részleteit.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hogyan lehet becsült költségek kiszámítása a Site Recovery használata során?

Használhatja a [díjkalkulátor](https://aka.ms/asr_pricing_calculator) költségeket megbecsülheti a Site Recovery használata közben.

Részletes becsült költségek, futtassa a deployment planner eszköz a [VMware](https://aka.ms/siterecovery_deployment_planner) vagy [Hyper-V](https://aka.ms/asr-deployment-planner), és használja a [költségbecslési jelentés költség](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Felügyelt lemezeket a VMware virtuális gépek és fizikai kiszolgálók replikálása most már használhatók. A gyorsítótárfiók a felügyelt lemezekkel rendelkező vonatkozik külön díj fel?

Nem, nincsenek gyorsítótár nem vonatkozik külön díj. Standard szintű tárfiókot a replikált, amikor a gyorsítótár az azonos céloldali tárfiók része.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Már több mint egy hónapja Azure Site Recovery-felhasználó vagyok. Továbbra is minden egyes védett példány esetében vonatkozik rám a díjmentes első 31 nap?

Igen. Az első 31 napra egyetlen védett példány tekintetében sem számítunk fel Azure Site Recovery-díjat. Például ha Ön 10 példányt védett az elmúlt 6 hónap során, és csatlakoztatja egy 11. példányt is az Azure Site Recovery, nem számítunk fel díjat a 11. példányra az első 31 nap. Az első 10 példányra továbbra is az Azure Site Recovery költségek, mivel azok már védelemmel 31 napnál hosszabb.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Az első 31 nap során kell valamilyen más Azure-díjat fizetni?

Igen, annak ellenére, hogy a Site Recovery szolgáltatás ingyenes a védett példányok az első 31 nap során, díjkötelesek lehetnek Azure Storage, a tárelérési tranzakciók és az adatforgalom. Továbbá a helyreállított virtuális gépekre is vonatkozhatnak Azure-díjak a számítási idő alapján.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Van egy katasztrófa utáni helyreállítás gyakorlatokat és tesztelési feladatátvétel végrehajtásához kapcsolódó költségek?

Nincs a Vészhelyreállítás részletezési külön költség nélkül. Kell díjat fizetni a tesztelési feladatátvétel után a virtuális gép létrehozása után.



## <a name="security"></a>Biztonság

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, a Site Recovery nem replikált adatok intercept, de nem rendelkezik a virtuális gépek vagy fizikai kiszolgálókon futó bármilyen információ.
A replikációs adatcsere a helyszíni Hyper-V gazdagépek, a VMware hipervizorok vagy fizikai kiszolgálók és az Azure tárolási szolgáltatás között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

Site Recovery szolgáltatás ISO 27001:2013, 27018, a HIPAA, DPA hitelesített, és SOC2 és FedRAMP JAB folyamatban van.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Megfelelőségi okokból akár a helyi metaadatok ugyanabban a földrajzi régióban kell maradniuk. A Site Recovery segítségével velünk a kapcsolatot?
Igen. Site Recovery-tároló létrehozásakor egy régióban biztosítható, hogy minden metaadat kell lehetővé tételéhez és levezényléséhez replikációs és feladatátvételi adott régión belül marad, a földrajzi határ.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
A virtuális gépek és fizikai kiszolgálók a helyszíni helyek – az átvitel közbeni titkosítás között zajlik a replikálás használata támogatott. A virtuális gépek és fizikai kiszolgálók replikációja az Azure-ba, mindkét – az átvitel közbeni titkosítás és [titkosítás inaktív (az Azure-ban)](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.




## <a name="disaster-recovery"></a>Vészhelyreállítás

### <a name="what-can-site-recovery-protect"></a>Mi a Site Recovery védheti?
* **Az Azure virtuális gépek**: Site Recovery a támogatott Azure virtuális gépen futó bármilyen számítási feladat képes replikálni.
* **A Hyper-V virtuális gépek**: Site Recovery szolgáltatás védi a Hyper-V virtuális gépen futó bármilyen számítási feladat.
* **Fizikai kiszolgálók**: Site Recovery szolgáltatás védi a Windows vagy Linux rendszerű fizikai kiszolgálókat.
* **VMware virtuális gépek**: Site Recovery szolgáltatás védi a VMware virtuális gépeken futó bármilyen számítási feladat.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Milyen számítási feladatokat tud védeni a Site Recovery?
A Site Recovery segítségével a legtöbb, a támogatott virtuális Gépen vagy fizikai kiszolgálón futó munkaterhelések védelmére. A Site Recovery támogatja az alkalmazásbarát replikációt, így az alkalmazások működőképes állapotban állíthatók helyre. Integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory, és szorosan együttműködik az olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Elérhető a vészhelyreállítás a fiókirodák számára a Site Recoveryvel?
Igen. A Site Recovery használatával a fiókirodákban replikációjának és feladatátvételének megszervezése, amikor egy egységes vezénylésével és a nézet az összes a fiókiroda számítási feladatait kap egy központi helyen. A központi telephelyről könnyen intézheti a feladatátvételt és a vészhelyreállítást az összes fiókiroda tekintetében anélkül, hogy el kellene látogatnia a fiókirodák telephelyeire.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Az Azure virtuális gépek támogatott vész-helyreállítási?

Igen, a Site Recovery támogatja a vészhelyreállítás az Azure virtuális gépek Azure-régiók között. [Tekintse át a gyakori kérdéseket](azure-to-azure-common-questions.md) kapcsolatos Azure-beli virtuális gépek vészhelyreállításához.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Vész-helyreállítási támogatja a VMware virtuális gépekhez?

Igen, a Site Recovery a helyszíni VMware virtuális gépek vészhelyreállítása támogatja. [Tekintse át a gyakori kérdéseket](vmware-azure-common-questions.md) vész-helyreállítási VMware virtuális gépek.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>A Hyper-V virtuális gépek támogatott vész-helyreállítási?
Igen, a Site Recovery a helyszíni Hyper-V virtuális gépek vészhelyreállítása támogatja. [Tekintse át a gyakori kérdéseket](hyper-v-azure-common-questions.md) vész-helyreállítási Hyper-V virtuális gépek.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>A fizikai kiszolgálók számára támogatott vész-helyreállítási?
Igen, a Site Recovery támogatja a Windows és Linux rendszerű, Azure-bA vagy egy másodlagos helyre a helyszíni fizikai kiszolgálók vészhelyreállítását. A vész-helyreállítási követelményeivel kapcsolatos további [Azure](vmware-physical-azure-support-matrix.md#replicated-machines), és a[egy másodlagos hely](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Vegye figyelembe, hogy fizikai kiszolgálók virtuális gépként fognak futni az Azure-ban a feladatátvételt követően. Feladat-visszavétel az Azure-ból a helyszíni fizikai kiszolgálóra jelenleg nem támogatott. Vissza a VMware virtuális gépeket csak sikertelen.





## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Replikálhatok, az Azure-bA a site-to-site VPN-kapcsolaton keresztül?
Az Azure Site Recovery replikálja az adatokat egy Azure storage-fiók vagy a felügyelt lemezek, egy nyilvános végpontot keresztül. Replikáció a site-to-site VPN-kapcsolaton keresztül nem. 

### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem tudja replikálni VPN-kapcsolaton keresztül?

Ha Azure-bA replikálni replikációs forgalom eléri a nyilvános végpontokat az Azure Storage-tárolók. Így csak replikálhatja az expressroute-tal (nyilvános társviszony-létesítés) a nyilvános interneten keresztül, és a VPN nem működik.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Használhatok Riverbed SteelHeads replikáció?

A partner Riverbed, részletes útmutatást nyújt az Azure Site Recovery használata. Tekintse át a [megoldási útmutató](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Az ExpressRoute használatával virtuális gépek replikálása az Azure-bA?
Igen, [ExpressRoute is használható](concepts-expressroute-with-site-recovery.md) a helyszíni virtuális gépek replikálása az Azure-bA.

- Az Azure Site Recovery replikálja az adatokat egy Azure Storage egy nyilvános végpontot keresztül. Be kell állítania [nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#publicpeering) vagy [Microsoft társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ExpressRoute használata a Site Recovery replikációjára.
- Microsoft társviszony-létesítés a replikáció ajánlott útválasztási tartományhoz.
- Miután a virtuális gépek feladatátadása megtörtént az Azure virtual Networkhöz elérheti azokat használatával a [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#privatepeering) beállítása az Azure virtuális hálózattal.
- Replikáció nem támogatott a privát társviszony-létesítésen keresztül.
- Ha VMware-alapú gépek vagy fizikai gépek védi, győződjön meg arról, hogy a konfigurációs kiszolgáló megfelel-e az [hálózati követelményeiben](vmware-azure-configuration-server-requirements.md#network-requirements) replikálásra. 



### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Ha az Azure-bA replikálok, milyen típusú storage-fiók vagy a felügyelt lemez van szükségem?

Szüksége lesz egy LRS vagy GRS-tárolót. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. A Premium storage a VMware virtuális gépek, Hyper-V virtuális és fizikai kiszolgálók replikálásához, akkor támogatott, ha a Site Recovery üzembe helyezése az Azure Portalon. A felügyelt lemezek csak az LRS támogatják.

### <a name="how-often-can-i-replicate-data"></a>Milyen gyakran replikálhatom az adatokat?
* **Hyper-V:** A Hyper-V virtuális gépek replikálhatók át 5 percenként, o 30 másodperc (kivéve a premium storage)
* **Az Azure virtuális gépek, VMware virtuális gépeket, fizikai kiszolgálók:** A replikáció gyakoriságának ezeknél nincs jelentősége. A replikálás folyamatos történik.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kiterjesztheti a replikáció már létező helyreállítási helyről egy másik harmadlagos helyhez?
A kiterjesztett vagy láncolt replikáció nem támogatott. Ennek a funkciónak a kérelem [Visszajelzési fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Végezhetek offline replikációt, amikor első alkalommal replikálok Azure-ba?
Ez a funkció nem támogatott. Ennek a funkciónak a kérelem a [Visszajelzési fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kizárhatok a replikációból bizonyos lemezeket?
Ha VMware virtuális gépek és a Hyper-V virtuális gépek Azure-ban, az Azure portal használatával replikál támogatják.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Replikálhatok dinamikus lemezekkel rendelkező virtuális gépeket?
Ha Hyper-V virtuális gépek replikálásához, illetve ha VMware virtuális gépek és fizikai gépek replikálása Azure-bA támogatja a dinamikus lemezeket. Az operációsrendszer-lemez alaplemeznek kell lennie.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Szabályozhatja a replikációs forgalom kiosztott sávszélesség?
Igen. További információ ezekben a cikkekben sávszélesség szabályozása:

* [Kapacitás megtervezése a VMware virtuális gépek és fizikai kiszolgálók replikálása](site-recovery-plan-capacity-vmware.md)
* [Kapacitás megtervezése a Hyper-V virtuális gépek replikálása Azure-bA](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Feladatátvétel
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Ha e vagyok átvitelét az Azure-ba, hogyan férhetek hozzá az Azure virtuális gépek a feladatátvételt követően?

Az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en keresztül, vagy Azure ExpressRoute segítségével érheti el. Készítse elő a számos dolgot, hogy csatlakozni kell. [További információk](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Lehet feladatátvételt az Azure hogyan Azure győződjön meg arról, ha az adatok hibatűréséről?
Az Azure-t hibatűrőnek terveztük. A Site Recovery már dolgait a feladatátvétel egy másodlagos Azure adatközpontba, az Azure SLA. Ha ez történik, biztosítjuk, hogy a metaadatokat, és a tárolók maradjanak ugyanabban a földrajzi régióban, a tároló számára is választott.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ha két adatközpont között végzek, mi történik, ha az elsődleges adatközpont esetleges váratlan leállásakor?
Elindíthat egy nem tervezett feladatátvételt a másodlagos helyről. A Site Recovery nem követeli meg a feladatátvétel végrehajtásához, hogy az elsődleges helyről kapcsolódjon.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
A feladatátvétel nem automatikus. A portálon egyetlen kattintással feladatátvételt, vagy használhat [Site Recovery PowerShell](/powershell/module/az.recoveryservices) feladatátvétel indításához. A Site Recovery portálon egy egyszerű művelet visszavétel.

Automatizálhatja, sikerült helyi Orchestrator vagy az Operations Manager használata virtuális gép hibájának észlelésére, és majd elindíthatja a feladatátvételt az SDK-val.

* [További információ](site-recovery-create-recovery-plans.md) helyreállítási tervek ismertetése.
* [További információ](site-recovery-failover.md) feladatátvételi kapcsolatban.
* [További információ](site-recovery-failback-azure-to-vmware.md) biztonsági másolatot feladat-visszavétel VMware virtuális gépek és fizikai kiszolgálók

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Ha a helyi gazdagép nem válaszol vagy összeomlott, e visszaadhatja egy másik gazdagépre?
Igen, a másodlagos hely helyreállítása használhatók egy másik gazdagépre feladat-visszavétel az Azure-ból.

* [A VMware virtuális gépek](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V virtuális gépek](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálhatja a Site Recovery-forgatókönyvek az SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. Jelenleg támogatott forgatókönyveket, a PowerShell használatával a Site Recovery üzembe helyezéséhez:

* [VMMs-felhőben lévő Hyper-V-Virtuálisgépek replikálása az Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [A Hyper-V virtuális gépek VMM nélkül replikálása az Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Az Azure PowerShell Resource Manager-bA replikálni a VMware](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Összetevő/szolgáltató frissítése

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Hol található a kibocsátási megjegyzések és kumulatív frissítések a Site Recovery frissítési

[Ismerje meg,](site-recovery-whats-new.md) új frissítésekről, és [összesített adatok lekérése](service-updates-how-to.md).

## <a name="next-steps"></a>További lépések
* Olvassa el a [Site Recovery áttekintését](site-recovery-overview.md)

