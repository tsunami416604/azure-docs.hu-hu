---
title: "Az Azure Site Recovery: Gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk ismerteti, amelyek a népszerű kérdések Azure Site Recoveryvel kapcsolatos."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: raynew
ms.openlocfilehash: ad6f70cf9c2f420e887031c8b240d2f831e6c359
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: gyakori kérdések (GYIK)
A cikk az Azure Site Recovery kapcsolatos gyakran ismételt kérdések tartalmaz. Ha kérdése van a cikk elolvasása után, az fel őket a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Általános kérdések
### <a name="what-does-site-recovery-do"></a>Mire való a Site Recovery?
A Site Recovery funkciója hozzájárul az üzletmenet folytonosságát és a vész-helyreállítási (BCDR) stratégia, a levezényli és automatizálja a replikáció Azure virtuális gépek közötti régiókban, a helyszíni virtuális gépek és fizikai kiszolgálók Azure-ba, és a helyszíni gépeket egy másodlagos adatközpont. [További információk](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Mi is védeni a Site Recovery?
* **Azure virtuális gépek**: a Site Recovery bármilyen a támogatott Azure virtuális gép számítási feladatot képes replikálni.
* **Hyper-V virtuális gépek**: tudja védeni a Site Recovery bármilyen munkaterhelést egy Hyper-V virtuális gépen.
* **Fizikai kiszolgálók**: a Site Recovery windowsos vagy Linuxos fizikai kiszolgálókat védhet.
* **VMware virtuális gépek**: tudja védeni a Site Recovery bármilyen VMware virtuális gép futó munkaterhelések.



### <a name="can-i-replicate-azure-vms"></a>Azure virtuális gépek képes replikálni?
Igen, replikálhatja támogatott Azure virtuális gépek Azure-régiók között. [További információk](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Mit kell a Hyper-V replikációra a Site Recoveryvel?
A szükséges Hyper-V gazdakiszolgálóra vonatkozó feltételek a telepítési forgatókönyvtől függenek. A Hyper-V-vel kapcsolatos előfeltételekről az alábbi cikkekben olvashat:

* [(VMM nélkül) a Hyper-V virtuális gépek replikálása Azure-bA](site-recovery-hyper-v-site-to-azure.md)
* [(A VMM-mel) a Hyper-V virtuális gépek replikálása Azure-bA](site-recovery-vmm-to-azure.md)
* [Hyper-V virtuális gépek replikálása másodlagos adatközpontba](site-recovery-vmm-to-vmm.md)
* Ha replikál, olvassa el a másodlagos adatközpontba [Hyper-V virtuális gépek esetén a vendég operációs rendszerek támogatott](https://technet.microsoft.com/library/mt126277.aspx).
* Ha az Azure-bA replikál, a Site Recovery támogatja-e minden a vendég operációs rendszerek, amelyek [használható az Azure-](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Képes a virtuális gépek védelme Ha Hyper-V ügyfél operációs rendszeren fut?
Nem. A virtuális gépeknek egy támogatott Windows kiszolgáló gépen futó Hyper-V gazdakiszolgálón kell lenniük. Ha egy ügyfélszámítógép védeni kíván sikerült replikálja, mint egy fizikai gép [Azure](site-recovery-vmware-to-azure.md) vagy egy [másodlagos adatközpontba](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Milyen számítási feladatokat tud védeni a Site Recovery?
A Site Recovery segítségével a legtöbb, a támogatott virtuális vagy fizikai kiszolgálón futó munkaterhelések védelmére. A Site Recovery támogatja az alkalmazástudatos replikációt, így az alkalmazások működőképes állapotban állíthatók helyre. Integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory, és szorosan együttműködik olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V-gazdagépek a VMM-felhőkben kell igényelnek?
Ha replikálása másodlagos adatközpontba, akkor a Hyper-V virtuális gépek kell lennie a Hyper-V gazdagépek a VMM-felhőben lévő kiszolgálók. Ha az Azure-bA replikálni kívánt, majd replikálhatja virtuális gépek vagy a VMM-felhő nélkül. [További](tutorial-hyper-v-to-azure.md) Azure Hyper-V replikációs kapcsolatban.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Üzembe helyezhetem VMM-mel a Site Recovery-t, ha csak egy VMM-kiszolgálóm van?

Igen. Replikálhat virtuális gépeket a Hyper-V kiszolgálók, a VMM-felhőben az Azure-ba, vagy ugyanazon a kiszolgálón található VMM-felhők között. A helyszíni replikáció javasoljuk, hogy rendelkezik-e a VMM-kiszolgáló az elsődleges és másodlagos helyeken.  

### <a name="what-physical-servers-can-i-protect"></a>Milyen fizikai kiszolgálókat lehet védeni?
Fizikai kiszolgálók Azure-bA vagy másodlagos helyhez fut a Windows és Linux replikálhatja. [További tudnivalók](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) operációs rendszerre vonatkozó követelményeket.  Ugyanazok a követelmények érvényesek, hogy a fizikai kiszolgálók Azure-bA vagy másodlagos helyre replikál.


Vegye figyelembe, hogy fizikai kiszolgálók virtuális gépként fognak futni az Azure-ban, ha a helyszíni kiszolgáló leáll. A feladat-visszavétel egy helyszíni fizikai kiszolgáló jelenleg nem támogatott. Védett, fizikai gépek esetén csak a feladat-visszavétel a VMware virtuális gépeket is.

### <a name="what-vmware-vms-can-i-protect"></a>Milyen VMware virtuális gépek védhetők?

VMware virtuális gépek védelméhez vSphere hipervizorra van szükség, a virtuális gépeknek pedig VMware-eszközökkel kell rendelkezniük. A hipervizorok kezeléséhez a VMware vCenter használatát javasoljuk. [További](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) replikálásához a VMware-kiszolgálók és virtuális gépek Azure-bA vagy másodlagos helyhez részletes követelményeiről.


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Elérhető a vészhelyreállítás a fiókirodák számára a Site Recoveryvel?
Igen. A Site Recovery használatával replikációs és feladatátvételi levezényelni a fiókirodában használt, egy központi helyen egy egységes vezénylési és a nézet az összes a fiókiroda számítási feladatait fog kapni. A központi telephelyről könnyen intézheti a feladatátvételt és a vészhelyreállítást az összes fiókiroda tekintetében anélkül, hogy el kellene látogatnia a fiókirodák telephelyeire.

## <a name="pricing"></a>Díjszabás
Az árazással kapcsolatos kérdésekre, tekintse meg a következő gyakori kérdések [Azure Site Recovery díjszabásáról](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Biztonság
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, a Site Recovery nem gyűjt replikációs adatokat, és nem rendelkezik semmilyen információval, hogy a virtuális gépek és fizikai kiszolgálókon futó tartalomról.
A replikációs adatcsere a helyszíni Hyper-V gazdagépek, a VMware hipervizorok vagy fizikai kiszolgálók és az Azure tárolási szolgáltatás között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

A Site Recovery ISO 27001:2013, 27018, HIPAA, DPA hitelesített, de SOC2 és FedRAMP JAB minősítés folyamatban van.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Megfelelőségi okokból még akkor is a helyi metaadatok ugyanazon a földrajzi területen belül kell maradnia. A Site Recovery segíthet?
Igen. A Site Recovery-tárolóban egy régióban létrehozásakor gondoskodunk róla, hogy minden metaadatot, amely igazolnia kell a lehetővé tételéhez és levezényléséhez replikációs és feladatátvételi adott régión belül marad a földrajzi határ.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
A virtuális gépek és fizikai kiszolgálók a helyszíni helyek titkosítási az átvitel közötti replikálása esetén támogatott. A virtuális gépek és fizikai kiszolgálók replikálása Azure-ba, mindkét titkosítási az átvitel és [titkosítási nyugalmi (az Azure-ban)](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption) támogatottak.

## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Replikálhatok, pont-pont VPN-Azure-bA?
Az Azure Site Recovery replikálja az adatokat egy Azure storage-fiókot, egy nyilvános végpontot keresztül. Replikációs nem található a pont-pont VPN-kapcsolaton keresztül. A telephelyek közötti VPN, létrehozhat egy Azure virtuális hálózatra. Ez nem zavarja a Site Recovery replikációs.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>ExpressRoute használatával virtuális gépek replikálása Azure-bA?
Igen, ExpressRoute segítségével virtuális gépek replikálása Azure-bA. Az Azure Site Recovery replikálja az adatokat egy Azure Storage-fiók egy nyilvános végpontot keresztül. Állítson be kell [nyilvános társviszony](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) Site Recovery replikációs ExpressRoute használandó. Miután a virtuális gép keresztül lett hibába ütközött egy Azure virtuális hálózatra elérheti őket használatával a [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) beállítása az Azure virtuális hálózathoz.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Van valamilyen előfeltétele a virtuális gépek Azure-ba való replikációjának?
Az Azure-bA replikálni kívánt virtuális gépek meg kell felelnie [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

Egyes rendelkeznie kell Azure felhasználói fiókja [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) engedélyezni szeretné egy új virtuális gép az Azure-bA replikálását.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Replikálható Hyper-V 2. generációs virtuális gép az Azure-ba?
Igen. A Site Recovery 1. generációs feladatátvétel során a 2. generációs alakítja. A feladat-visszavétel a gép alakítja vissza a 2. generációs. [További információk](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Ha Azure-ba replikálok, hogyan kell fizetni az Azure virtuális gépekért?
A szokásos replikáció esetén adatokat georedundáns Azure Storage replikálódik, és azt nem kell díjat fizetnie Azure infrastruktúra-szolgáltatási virtuális gép díjakért jelentős előnyt biztosít. Amikor feladatátvételt indít az Azure-ba, a Site Recovery automatikusan létrehoz virtuális gépeket az Azure IaaS-ben, ezt követően pedig az Azure-ban felhasznált számítási erőforrások díját fogjuk számlázni.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálható a Site Recovery forgatókönyvek az SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. A PowerShell használatával a Site Recovery üzembe helyezésekor jelenleg támogatott esetek:

* [VMMs-felhőkben Hyper-V virtuális gépek replikálása az Azure PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [VMM nélkül a Hyper-V virtuális gépek replikálása az Azure PowerShell erőforrás-kezelő](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Milyen típusú tárfiókra van szükségem ahhoz, hogy az Azure-ba replikálhassak?
Az LRS- vagy GRS-tárfiók van szüksége. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. Prémium szintű storage esetén támogatott VMware virtuális gép, a Hyper-V virtuális gép és a fizikai kiszolgáló replikációs, amikor telepíti a Site Recovery az Azure portálon.

### <a name="how-often-can-i-replicate-data"></a>Milyen gyakran replikálhatom az adatokat?
* **Hyper-V:** Hyper-V virtuális gépek (kivéve a prémium szintű storage) 30 másodperces, 5 percenként vagy 15 percenként lehet replikálni. Ha beállítása SAN-replikálás akkor szinkron replikálása.
* **VMware és fizikai kiszolgálók:** a replikáció gyakoriságának ezeknél nincs jelentősége. Replikáció folyamatos.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kiterjesztheti a replikálást a már létező helyreállítási helyről egy másik harmadlagos helyre?
A kiterjesztett vagy láncolt replikáció nem támogatott. Ez a szolgáltatás kérése [visszajelzési fórumon](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Végezhetek offline replikációt, amikor első alkalommal replikálok Azure-ba?
Ez a funkció nem támogatott. Ez a szolgáltatás kérése a [visszajelzési fórumon](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kizárhatok a replikációból bizonyos lemezeket?
Ez akkor támogatott, ha Ön [VMware virtuális gépek és a Hyper-V virtuális gépek replikálása](site-recovery-exclude-disk.md) az Azure-ba, az Azure portál használatával.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Replikálhatok dinamikus lemezzel rendelkező virtuális gépek?
A dinamikus lemezek Hyper-V virtuális gépek replikálása esetén támogatottak. Ezek is támogatottak, ha VMware virtuális gépek és fizikai gépek replikálása Azure-bA. Az operációsrendszer-lemez alaplemeznek kell lennie.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Új gépek hozzáadható egy meglévő replikációs csoporthoz?
Új gépek felvétele a meglévő replikációs csoportok esetén támogatott. Ehhez az szükséges, válassza ki a replikációs csoporthoz ("Replikált elemek" paneljéről) és a replikációs csoportban kattintson a jobb gombbal/kiválasztani helyi menü, és jelölje ki a kívánt beállítást.

![Felvétele replikációs csoportba](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Szabályozhatom a Hyper-V replikációs forgalom sávszélességét?
Igen. További információ a központi telepítés cikkekben sávszélesség szabályozása:

* [VMware virtuális gépek és fizikai kiszolgálók replikálása kapacitástervezését](site-recovery-plan-capacity-vmware.md)
* [A Hyper-V virtuális gépek replikálása Azure-bA kapacitástervezése](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Feladatátvétel
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>I vagyok feladatátadás az Azure-ba, ha hogyan érhetem el az Azure virtuális gépek a feladatátvételt követően?
Az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en keresztül, vagy Azure ExpressRoute segítségével érheti el. Készítse elő a több csatlakozás érdekében minden lesz szüksége. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Ha I feladatátadást hogyan Azure győződjön meg arról, hogy az adatok is lehetséges?
Az Azure-t hibatűrőnek terveztük. Szükség esetén a Site Recovery már visszafejtett a feladatátvétel egy másodlagos Azure adatközpontba, az Azure garantált szolgáltatási szintje. Ha ez történik, biztosítjuk, hogy a metaadatok, és a tároló számára kiválasztott azonos földrajzi régióban maradjanak tárolók.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ha két adatközpont között végzek, mi történik, ha az elsődleges adatközpont esetleges váratlan leállásakor?
Elindíthat egy nem tervezett feladatátvételt a másodlagos helyről. A Site Recovery nem követeli meg a feladatátvétel végrehajtásához, hogy az elsődleges helyről kapcsolódjon.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
A feladatátvétel nem automatikus. Feladatátvételt egyetlen kattintással a portálon, vagy használhat [Site Recovery PowerShell](/powershell/module/azurerm.siterecovery) elindítása a feladatátvételt. Sikertelen vissza nem egy egyszerű műveletek a Site Recovery portálon.

Automatizálható, képes használni a helyszíni Orchestratort vagy az Operations Manager virtuális gép hibájának észlelésére, és majd elindíthatja a feladatátvételt az SDK használatával.

* [További](site-recovery-create-recovery-plans.md) helyreállítási tervek.
* [További](site-recovery-failover.md) feladatátvétellel kapcsolatos.
* [További](site-recovery-failback-azure-to-vmware.md) hibás kapcsolatos biztonsági másolatot a VMware virtuális gépek és fizikai kiszolgálók

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Ha a helyi gazdagép nem válaszol vagy lefagyott, telepíthetek vissza egy másik gazdagépet a feladatátvétel?
Igen, a másik helyre történő helyreállítást használható feladat-visszavételi egy másik gazdagépen az Azure-ból. További információk a beállítások az alábbi hivatkozások VMware és a Hyper-v virtuális gépek.

* [A VMware virtuális gépek](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [A Hyper-v virtuális gépek](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>Szolgáltatók
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Szolgáltató vagyok. Működik a Site Recovery dedikált és megosztott infrastruktúra-modellekkel?
Igen, a Site Recovery támogatja mind a dedikált, mind a megosztott infrastruktúra-modelleket.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>A szolgáltató az hozzáfér a bérlők identitásadataihoz a Site Recovery szolgáltatásban identitásának?
Nem. Bérlői azonosító névtelen marad. A bérlőnek nincs szüksége a Site Recovery-portál elérésére. Csak a szolgáltatást nyújtó rendszergazdája kommunikál a portállal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Rendszer bérlő alkalmazásainak adatai eljutnak az Azure-bA?
Ha a replikáció a szolgáltató által birtokolt helyek között történik, az alkalmazásadatok soha nem kerülnek az Azure-ba. Az átvitel során, és közvetlenül a szolgáltatást nyújtó helyei között replikált adatok titkosítása.

Ha az Azure-ba replikál, az alkalmazásadatok Azure-tárterületre kerülnek, a Site Recovery szolgáltatáshoz azonban nem jutnak el. Adatok titkosított az átvitel, és az Azure-ban is titkosítva maradnak.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Kapnak a bérlőim számlát valamilyen Azure-szolgáltatásról?
Nem. Az Azure közvetlenül a szolgáltatóval áll számlázási kapcsolatban. A bérlők felé történő számlázásért a szolgáltató felel.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Ha az Azure-ba replikálok, szükséges a virtuális gépeket folyamatosan az Azure-ban futtatni?
Azure-tárfiók az előfizetésben nem, adatok replikálódnak. Ha feladatátvételi tesztet (vészhelyreállítási gyakorlatot) vagy tényleges feladatátvételt végez, a Site Recovery automatikusan létrehozza a virtuális gépeket az előfizetéséhez.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Elérhető bérlő szintű elkülönítés az Azure-ba való replikációnál?
Igen.

### <a name="what-platforms-do-you-currently-support"></a>Jelenleg milyen platformok támogatottak?
Azure-csomag, a Cloud Platform System támogatott, és a System Center-alapú (2012 és újabb verzió) telepítések. [További](https://technet.microsoft.com/library/dn850370.aspx) Azure Pack csomaghoz és a Site Recovery integrálása.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Támogatott az egyetlen Azure Pack-re és az egyetlen VMM-kiszolgálóra alapuló üzembe helyezési modell?
Igen, a Hyper-V virtuális gépek replikálása, az Azure-ba, és replikálhat szolgáltatói helyek között.  Vegye figyelembe, hogy ha a szolgáltatást nyújtó helyei között történik a replikáció, az Azure-forgatókönyvek integrációja nem érhető el.

## <a name="next-steps"></a>Következő lépések
* Olvassa el a [Site Recovery áttekintését](site-recovery-overview.md)
* Információk a [Site Recovery architektúrájáról](site-recovery-components.md)  
