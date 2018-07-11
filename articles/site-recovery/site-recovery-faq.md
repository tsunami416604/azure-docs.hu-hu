---
title: 'Az Azure Site Recovery: Gyakori kérdések |} A Microsoft Docs'
description: Ez a cikk ismerteti a gyakori kérdések az Azure Site Recoveryvel kapcsolatos.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 41fa742571b94505588172b94ea91f196e212a4c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920963"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Az Azure Site Recovery: gyakori kérdések (GYIK)
Ez a cikk az Azure Site Recovery – gyakori kérdések tartalmazza. Ha kérdése van a cikk elolvasása után, el őket az a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Általános kérdések
### <a name="what-does-site-recovery-do"></a>Mire való a Site Recovery?
A Site Recovery megvalósításában az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia, replikálásának munkafolyamatát levezényli és automatizálja az Azure virtuális gépek közötti régiók, a helyszíni virtuális gépek és fizikai kiszolgálók Azure-ba, és a helyszíni gépek egy másodlagos adatközpontba. [További információk](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Mi a Site Recovery védheti?
* **Az Azure virtuális gépek**: Site Recovery a támogatott Azure virtuális gépen futó bármilyen számítási feladat képes replikálni.
* **A Hyper-V virtuális gépek**: Site Recovery szolgáltatás védi a Hyper-V virtuális gépen futó bármilyen számítási feladat.
* **Fizikai kiszolgálók**: Site Recovery szolgáltatás védi a Windows vagy Linux rendszerű fizikai kiszolgálókat.
* **VMware virtuális gépek**: Site Recovery szolgáltatás védi a VMware virtuális gépeken futó bármilyen számítási feladat.



### <a name="can-i-replicate-azure-vms"></a>Azure virtuális gépeket lehet replikálni?
Igen, támogatott Azure virtuális gépeket replikálhatja az Azure-régiók között. [További információk](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Mit kell a Hyper-V Site Recovery-vel való replikáció vezényléséhez?
A szükséges Hyper-V gazdakiszolgálóra vonatkozó feltételek a telepítési forgatókönyvtől függenek. A Hyper-V-vel kapcsolatos előfeltételekről az alábbi cikkekben olvashat:

* [Azure-ba történő Hyper-V virtuális gépek (VMM nélkül)](site-recovery-hyper-v-site-to-azure.md)
* [Azure-ba történő Hyper-V virtuális gépek (VMM-mel)](site-recovery-vmm-to-azure.md)
* [Hyper-V virtuális gépek replikálása másodlagos adatközpontba](site-recovery-vmm-to-vmm.md)
* Ha replikál egy másodlagos adatközpontba, olvassa el [támogatott vendég operációs rendszerként a Hyper-V virtuális gépek](https://technet.microsoft.com/library/mt126277.aspx).
* Ha az Azure-bA replikál, a Site Recovery támogatja az összes a vendég operációs rendszerek, amelyek [Azure által támogatott](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Képes virtuális gépek védelme, ha Hyper-V egy ügyfél operációs rendszeren fut?
Nem. A virtuális gépeknek egy támogatott Windows kiszolgáló gépen futó Hyper-V gazdakiszolgálón kell lenniük. Ha egy ügyfélszámítógép védelmére van szüksége, tudta replikálni, mint a fizikai gépek [Azure](site-recovery-vmware-to-azure.md) vagy egy [másodlagos adatközpontba](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Milyen számítási feladatokat tud védeni a Site Recovery?
A Site Recovery segítségével a legtöbb, a támogatott virtuális Gépen vagy fizikai kiszolgálón futó munkaterhelések védelmére. A Site Recovery támogatja az alkalmazásbarát replikációt, így az alkalmazások működőképes állapotban állíthatók helyre. Integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory, és szorosan együttműködik az olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>A Hyper-V-gazdagépeken szükséges VMM-felhőkben kell?
Ha replikálni szeretne egy másodlagos adatközpontba, akkor a Hyper-V virtuális gépek kell lennie a Hyper-V gazdagépek a VMM-felhőben található kiszolgálók. Ha az Azure-bA replikálni kívánt, majd virtuális gépeket replikálhatja vagy a VMM-felhőkben nélkül. [További információ](tutorial-hyper-v-to-azure.md) kapcsolatos Hyper-V-replikáció az Azure-bA.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Üzembe helyezhetem VMM-mel a Site Recovery-t, ha csak egy VMM-kiszolgálóm van?

Igen. Replikálhat a virtuális gépek a Hyper-V kiszolgálók a VMM-felhőben az Azure-ba, vagy ugyanazon a kiszolgálón található VMM-felhők között. A helyszíni és a helyszíni replikálásra azt javasoljuk, hogy rendelkezik-e a VMM-kiszolgáló az elsődleges és másodlagos helyeken.  

### <a name="what-physical-servers-can-i-protect"></a>Milyen fizikai kiszolgálókat lehet védeni?
Windows és Linux rendszerű, Azure-bA vagy egy másodlagos helyre fizikai kiszolgálókat replikálhat. Ismerje meg a követelményeknek [az Azure-bA](vmware-physical-azure-support-matrix.md#replicated-machines), és [egy másodlagos helyre replikációt](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


Vegye figyelembe, hogy fizikai kiszolgálók virtuális gépként fognak futni az Azure-ban, ha a helyszíni kiszolgáló leáll. Feladat-Visszavételhez helyszíni fizikai kiszolgálóra jelenleg nem támogatott. A gép, fizikai védelemmel csak a feladat-visszavétel VMware virtuális gépeket is.

### <a name="what-vmware-vms-can-i-protect"></a>Milyen VMware virtuális gépek védhetők?

VMware virtuális gépek védelméhez vSphere hipervizorra van szükség, a virtuális gépeknek pedig VMware-eszközökkel kell rendelkezniük. A hipervizorok kezeléséhez a VMware vCenter használatát javasoljuk. További tudnivalók a követelményei [az Azure-bA](vmware-physical-azure-support-matrix.md#replicated-machines), vagy [egy másodlagos helyre replikációt](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Elérhető a vészhelyreállítás a fiókirodák számára a Site Recoveryvel?
Igen. A Site Recovery használatával a fiókirodákban replikációjának és feladatátvételének megszervezése, amikor egy egységes vezénylésével és a nézet az összes a fiókiroda számítási feladatait kap egy központi helyen. A központi telephelyről könnyen intézheti a feladatátvételt és a vészhelyreállítást az összes fiókiroda tekintetében anélkül, hogy el kellene látogatnia a fiókirodák telephelyeire.

## <a name="pricing"></a>Díjszabás
A díjszabással kapcsolatos kérdésekre, tekintse meg a gyakori Kérdéseket, [Azure Site Recovery díjszabásáról](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Biztonság
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, a Site Recovery nem replikált adatok intercept, de nem rendelkezik a virtuális gépek vagy fizikai kiszolgálókon futó bármilyen információ.
A replikációs adatcsere a helyszíni Hyper-V gazdagépek, a VMware hipervizorok vagy fizikai kiszolgálók és az Azure tárolási szolgáltatás között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

Site Recovery szolgáltatás ISO 27001:2013, 27018, a HIPAA, DPA hitelesített, és SOC2 és FedRAMP JAB folyamatban van.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Megfelelőségi okokból akár a helyi metaadatok ugyanabban a földrajzi régióban kell maradniuk. A Site Recovery segítségével velünk a kapcsolatot?
Igen. Site Recovery-tároló létrehozásakor egy régióban biztosítható, hogy minden metaadat kell lehetővé tételéhez és levezényléséhez replikációs és feladatátvételi adott régión belül marad, a földrajzi határ.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
A virtuális gépek és fizikai kiszolgálók a helyszíni helyek – az átvitel közbeni titkosítás között zajlik a replikálás használata támogatott. A virtuális gépek és fizikai kiszolgálók replikációja az Azure-ba, mindkét – az átvitel közbeni titkosítás és [titkosítás inaktív (az Azure-ban)](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.

## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Replikálhatok, az Azure-bA a site-to-site VPN-kapcsolaton keresztül?
Az Azure Site Recovery replikálja az adatokat az Azure storage-fiókba, egy nyilvános végpontot keresztül. Replikáció a site-to-site VPN-kapcsolaton keresztül nem. Létrehozhat egy helyek közötti VPN-, az Azure-beli virtuális hálózathoz. Ez nem zavarja a Site Recovery replikációjára.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Az ExpressRoute használatával virtuális gépek replikálása az Azure-bA?
Igen, [ExpressRoute is használható](concepts-expressroute-with-site-recovery.md) a helyszíni virtuális gépek replikálása az Azure-bA. Az Azure Site Recovery replikálja az adatokat egy Azure Storage-fiókot, egy nyilvános végpontot keresztül. Be kell állítania [nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) vagy [Microsoft társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) ExpressRoute használata a Site Recovery replikációjára. Microsoft társviszony-létesítés a replikáció ajánlott útválasztási tartományhoz. Miután a virtuális gépek feladatátadása megtörtént az Azure virtual Networkhöz elérheti azokat használatával a [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) beállítása az Azure virtuális hálózattal. Replikáció nem támogatott a privát társviszony-létesítésen keresztül.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Van valamilyen előfeltétele a virtuális gépek Azure-ba való replikációjának?
[VMware virtuális gépek](vmware-physical-azure-support-matrix.md#replicated-machines) és [Hyper-V virtuális gépek](hyper-v-azure-support-matrix.md#replicated-vms) a replikálni kívánt Azure meg kell felelniük az Azure követelményeinek.

Az Azure felhasználói fióknak rendelkeznie kell bizonyos [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) engedélyezni a replikációt egy új virtuális gép az Azure-bA.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Replikálható Hyper-V 2. generációs virtuális gép az Azure-ba?
Igen. A Site Recovery a 2. generációs konvertálja az 1. a feladatátvétel során. Feladat-visszavételt, a gép vissza a 2. generációs alakítja át. [További információk](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Ha Azure-ba replikálok, hogyan kell fizetni az Azure virtuális gépekért?
Szokásos replikáció esetén rendszer replikálja az adatokat georedundáns Azure storage-ba, és, nem kell díjat fizetnie Azure IaaS virtuális gép díjakat nyújtó egyik jelentős előnye. Amikor feladatátvételt indít az Azure-ba, a Site Recovery automatikusan létrehoz virtuális gépeket az Azure IaaS-ben, ezt követően pedig az Azure-ban felhasznált számítási erőforrások díját fogjuk számlázni.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálhatja a Site Recovery-forgatókönyvek az SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. Jelenleg támogatott forgatókönyveket, a PowerShell használatával a Site Recovery üzembe helyezéséhez:

* [VMMs-felhőben lévő Hyper-V-Virtuálisgépek replikálása az Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [A Hyper-V virtuális gépek VMM nélkül replikálása az Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Az Azure PowerShell Resource Manager-bA replikálni a VMware](vmware-azure-disaster-recovery-powershell.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Milyen típusú tárfiókra van szükségem ahhoz, hogy az Azure-ba replikálhassak?
Az LRS vagy GRS tárfiókra van szükség. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. A Premium storage a VMware virtuális gépek, Hyper-V virtuális és fizikai kiszolgálók replikálásához, akkor támogatott, ha a Site Recovery üzembe helyezése az Azure Portalon.

### <a name="how-often-can-i-replicate-data"></a>Milyen gyakran replikálhatom az adatokat?
* **A Hyper-V:** Hyper-V virtuális gépek (kivéve a premium storage) 30 másodperc, 5 percenként vagy 15 perc lehet replikálni. Ha SAN-replikálás beállítása replikációs szolgáltatása szinkron.
* **VMware és fizikai kiszolgálók:** a replikáció gyakoriságának ezeknél nincs jelentősége. A replikálás folyamatos történik.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kiterjesztheti a replikáció már létező helyreállítási helyről egy másik harmadlagos helyhez?
A kiterjesztett vagy láncolt replikáció nem támogatott. Ennek a funkciónak a kérelem [Visszajelzési fórum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Végezhetek offline replikációt, amikor első alkalommal replikálok Azure-ba?
Ez a funkció nem támogatott. Ennek a funkciónak a kérelem a [Visszajelzési fórum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kizárhatok a replikációból bizonyos lemezeket?
Ha VMware virtuális gépek és a Hyper-V virtuális gépek Azure-ban, az Azure portal használatával replikál támogatják.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Replikálhatok dinamikus lemezekkel rendelkező virtuális gépeket?
A dinamikus lemezek Hyper-V virtuális gépek replikálása esetén támogatottak. Ezek is támogatottak, ha VMware virtuális gépek és fizikai gépek replikálása Azure-bA. Az operációsrendszer-lemez alaplemeznek kell lennie.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Egy meglévő replikációs csoporthoz is hozzáadhatok egy új gépet?
Új gépek hozzáadása meglévő replikációs csoportok használata támogatott. Ehhez válassza ki a replikációs csoporthoz ("Replikált elemek" panelről) és a helyi menüjében kattintson a jobb gombbal és válassza ki a replikációs csoportot, és válassza ki a megfelelő lehetőséget.

![Replikációs csoport hozzáadása](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Szabályozhatom a Hyper-V replikációs forgalom sávszélességét?
Igen. További információ a központi telepítési cikkekben sávszélesség szabályozása:

* [Kapacitás megtervezése a VMware virtuális gépek és fizikai kiszolgálók replikálása](site-recovery-plan-capacity-vmware.md)
* [Kapacitás megtervezése a Hyper-V virtuális gépek replikálása Azure-bA](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Feladatátvétel
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Ha e vagyok átvitelét az Azure-ba, hogyan férhetek hozzá az Azure-beli virtuális gépek a feladatátvételt követően?
Az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en keresztül, vagy Azure ExpressRoute segítségével érheti el. Készítse elő a számos dolgot, hogy csatlakozni kell. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Lehet feladatátvételt az Azure hogyan Azure győződjön meg arról, ha az adatok hibatűréséről?
Az Azure-t hibatűrőnek terveztük. Szükség esetén a Site Recovery már kategóriában a feladatátvétel egy másodlagos Azure adatközpontba, az Azure SLA. Ha ez történik, biztosítjuk, hogy a metaadatokat, és a tárolók maradjanak ugyanabban a földrajzi régióban, a tároló számára is választott.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ha két adatközpont között végzek, mi történik, ha az elsődleges adatközpont esetleges váratlan leállásakor?
Elindíthat egy nem tervezett feladatátvételt a másodlagos helyről. A Site Recovery nem követeli meg a feladatátvétel végrehajtásához, hogy az elsődleges helyről kapcsolódjon.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
A feladatátvétel nem automatikus. A portálon egyetlen kattintással feladatátvételt, vagy használhat [Site Recovery PowerShell](/powershell/module/azurerm.siterecovery) feladatátvétel indításához. A Site Recovery portálon egy egyszerű művelet visszavétel.

Automatizálhatja, sikerült helyi Orchestrator vagy az Operations Manager használata virtuális gép hibájának észlelésére, és majd elindíthatja a feladatátvételt az SDK-val.

* [További információ](site-recovery-create-recovery-plans.md) helyreállítási tervek ismertetése.
* [További információ](site-recovery-failover.md) feladatátvételi kapcsolatban.
* [További információ](site-recovery-failback-azure-to-vmware.md) biztonsági másolatot feladat-visszavétel VMware virtuális gépek és fizikai kiszolgálók

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Ha a helyi gazdagép nem válaszol vagy összeomlott, lehetőségeket vissza egy másik gazdagépre a feladatátvétel?
Igen, a másodlagos hely helyreállítása használhatók egy másik gazdagépre feladat-visszavétel az Azure-ból. További információ a beállításokat az alábbi hivatkozások a VMware és Hyper-V virtuális gépek.

* [A VMware virtuális gépek](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V virtuális gépek](hyper-v-azure-failback.md#perform-failback)

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
Nem, az adatok replikációja az Azure storage-fiókba az előfizetésében. Ha feladatátvételi tesztet (vészhelyreállítási gyakorlatot) vagy tényleges feladatátvételt végez, a Site Recovery automatikusan létrehozza a virtuális gépeket az előfizetéséhez.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Elérhető bérlő szintű elkülönítés az Azure-ba való replikációnál?
Igen.

### <a name="what-platforms-do-you-currently-support"></a>Jelenleg milyen platformok támogatottak?
Támogatott az Azure-csomag, a Cloud Platform System, és a System Center (2012 vagy újabb). [További](https://technet.microsoft.com/library/dn850370.aspx) Azure Pack és a Site Recovery integrációjával kapcsolatban.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Támogatott az egyetlen Azure Pack-re és az egyetlen VMM-kiszolgálóra alapuló üzembe helyezési modell?
Igen, Hyper-V virtuális gépeket replikálhatja az Azure-ba, vagy a szolgáltatói helyek között.  Vegye figyelembe, hogy replikálhat szolgáltatói helyek között, ha Azure-forgatókönyvek integrációja nem érhető el.

## <a name="next-steps"></a>További lépések
* Olvassa el a [Site Recovery áttekintését](site-recovery-overview.md)
* Információk a [Site Recovery architektúrájáról](site-recovery-components.md)  
