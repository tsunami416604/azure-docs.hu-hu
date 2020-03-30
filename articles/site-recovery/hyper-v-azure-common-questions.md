---
title: Gyakori kérdések a Hyper-V vészhelyreállításhoz az Azure Site Recovery szolgáltatással
description: Ez a cikk összefoglalja a helyszíni hyper-v virtuális gépek vész-helyreállítási beállításával kapcsolatos gyakori kérdéseket az Azure-beli Webhely-helyreállítási webhely használatával.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 7c5f55fbea67567ddf7a2afa6a61f6c76568d829
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498208"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Vészhelyreállítás Hyper-V-ről Azure-ba – Gyakori kérdések

Ez a cikk választ ad a helyszíni Hyper-V virtuális gépek Azure-ba replikálásakor látható gyakori kérdésekre. 

## <a name="general"></a>Általános kérdések

### <a name="how-is-site-recovery-priced"></a>Hogyan történik a Site Recovery ára?
Tekintse át [az Azure Site Recovery díjszabási](https://azure.microsoft.com/pricing/details/site-recovery/) adatait.

### <a name="how-do-i-pay-for-azure-vms"></a>Hogyan fizethetek az Azure-beli virtuális gépekért?
A replikáció során az adatok replikálódnak az Azure storage-ba, és nem fizet semmilyen virtuális gép módosításait. Amikor feladatátvételt futtat az Azure-ba, a Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket. Ezt követően az Azure-ban felhasznált számítási erőforrásokért díjat számítunk fel.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Van-e különbség a költségek, amikor replikálja az általános célú v2 tárfiók?

Általában a GPv2-tárfiókok tranzakciós költségeinek növekedését fogja látni, mivel az Azure Site Recovery a tranzakciók száma nehéz. [További információ](../storage/common/storage-account-upgrade.md#pricing-and-billing) a változás becsléséhez.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Mire van szükségem a Hyper-V-ben a replikáció site recovery-vel való koordinálásához?

A szükséges Hyper-V gazdakiszolgálóra vonatkozó feltételek a telepítési forgatókönyvtől függenek. A Hyper-V-vel kapcsolatos előfeltételekről az alábbi cikkekben olvashat:

* [Hyper-V virtuális gépek replikálása Azure-ba (VMM nélkül)](site-recovery-hyper-v-site-to-azure.md)
* [Hyper-V virtuális gépek replikálása Azure-ba (VMM-mel)](site-recovery-vmm-to-azure.md)
* [Hyper-V virtuális gépek replikálása másodlagos adatközpontba](site-recovery-vmm-to-vmm.md)
* Ha egy másodlagos adatközpontba replikál, olvassa el [a Támogatott vendég operációs rendszerek hyper-V virtuális gépekhez című témakört.](https://technet.microsoft.com/library/mt126277.aspx)
* Ha az Azure-ba replikál, a Site Recovery támogatja az [Azure által támogatott](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)összes vendég operációs rendszert.

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Védhetem a virtuális gépeket, ha a Hyper-V ügyféloperációs rendszeren fut?
Nem. A virtuális gépeknek egy támogatott Windows kiszolgáló gépen futó Hyper-V gazdakiszolgálón kell lenniük. Ha egy ügyfélszámítógépet kell védenie, fizikai gépként replikálhatja az [Azure-ba](site-recovery-vmware-to-azure.md) vagy egy [másodlagos adatközpontba.](site-recovery-vmware-to-vmware.md)

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>A Hyper-V gazdagépeknek VMM-felhőkben kell lenniük?
Ha egy másodlagos adatközpontba szeretne replikálni, akkor a Hyper-V virtuális gépeknek a VMM-felhőben található Hyper-V gazdakiszolgálókon kell lenniük. Ha az Azure-ba szeretne replikálni, majd vmm-felhőkkel vagy anélkül replikálhatja a virtuális gépeket. [További információ](tutorial-hyper-v-to-azure.md) az Azure-ba irányuló Hyper-V replikációról.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Replikálható Hyper-V 2. generációs virtuális gép az Azure-ba?
Igen. A Site Recovery a feladatátvétel során a 2-es generációról az 1.generációra konvertáló. Feladat-visszavételkor a gép visszaváltanak a 2- es generációra. [Tovább](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)- tovább .


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Üzembe helyezhetem VMM-mel a Site Recovery-t, ha csak egy VMM-kiszolgálóm van?

Igen. A VMM-felhőben lévő Hyper-V-kiszolgálókban lévő virtuális gépeket replikálhatja az Azure-ba, vagy replikálhatja a VMM-felhők között ugyanazon a kiszolgálón. A helyszíni és a helyszíni replikáció esetén azt javasoljuk, hogy az elsődleges és a másodlagos helyeken is rendelkezik VMM-kiszolgálóval. 

### <a name="what-do-i-need-in-azure"></a>Mire van szükségem az Azure-ban?
Szüksége van egy Azure-előfizetésre, egy Recovery Services-tárolóra, egy tárfiókra és egy virtuális hálózatra. A tárolónak, a tárfióknak és a hálózatnak ugyanabban a régióban kell lennie.

### <a name="what-azure-storage-account-do-i-need"></a>Milyen Azure storage-fiókra van szükségem?
Szüksége van egy LRS vagy GRS tárfiókra. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A prémium szintű tárhely támogatott.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Az Azure-fiókomnak engedélyekre van szüksége a virtuális gépek létrehozásához?
Ha Ön előfizetés-rendszergazda, rendelkezik a szükséges replikációs engedélyekkel. Ha nem, engedélyeket kell létrehozniegy Azure virtuális gép az erőforráscsoportban és a virtuális hálózat, amelyet a Site Recovery konfigurálásakor megadott, és a kijelölt tárfiókba való írásra vonatkozó engedélyeket. [További információ](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>A replikációs adatok elküldve a Site Recovery szolgáltatásba?
Nem, a Site Recovery nem fogja el a replikált adatokat, és nem rendelkezik semmilyen információt arról, hogy mi fut a virtuális gépeken. A replikációs adatok cseréje a Hyper-V gazdagépek és az Azure storage között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

Site Recovery ISO 27001:2013, 27018, HIPAA, DPA tanúsítvánnyal rendelkezik, és folyamatban van az SOC2 és A FedRAMP JAB értékelések.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Megőrizhetjük a helyszíni metaadatokat egy földrajzi régión belül?
Igen. Amikor egy régióban hoz létre egy tárolót, biztosítjuk, hogy a Site Recovery által használt összes metaadat az adott régió földrajzi határán belül maradjon.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Igen, mind a titkosítás-in-transit és [titkosítás az Azure-ban](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.


## <a name="deployment"></a>Környezet

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Mire használható a Hyper-V és az Azure replikációja?

- **Vészhelyreállítás:** Beállíthatja a teljes vészhelyreállítást. Ebben a forgatókönyvben replikálja a helyszíni Hyper-V virtuális gépeket az Azure storage-ba:
    - Virtuális gépek et replikálhat az Azure-ba. Ha a helyszíni infrastruktúra nem érhető el, adja át a feladatát az Azure-ba.
    - Ha feladatátvételt, az Azure virtuális gépek jönnek létre a replikált adatok használatával. Az Azure-beli virtuális gépeken alkalmazásokat és számítási feladatokat érhet el.
    - Ha a helyszíni adatközpont ismét elérhetővé válik, az Azure-ból visszaléphet a helyszíni webhelyre.
- **Áttelepítés**: A Site Recovery segítségével áttelepítheti a helyszíni Hyper-V virtuális gépeket az Azure storage-ba. Ezt követően a helyszíni Azure-ba való átvételt. Feladatátvétel után az alkalmazások és a számítási feladatok elérhetők és futnak az Azure virtuális gépeken.


### <a name="what-do-i-need-on-premises"></a>Mire van szükségem a helyszínen?

Egy vagy több önálló vagy fürtözött Hyper-V gazdaszámítógépen futó egy vagy több virtuális gépre van szükség. A System Center Virtual Machine Manager (VMM) által felügyelt gazdagépeken futó virtuális gépeket is replikálhatja.
- Ha nem a VMM-et futtatja, a Site Recovery telepítése során Hyper-V gazdagépeket és fürtöket gyűjthet hyper-V helyekre. A Site Recovery ügynököket (Az Azure Site Recovery Provider and Recovery Services ügynök) minden Hyper-V gazdagépen telepíti.
- Ha a Hyper-V gazdagépek egy VMM-felhőben találhatók, a replikációt a VMM-ben vezényli. A Site Recovery Provider telepítése a VMM-kiszolgálóra, a Helyreállítási szolgáltatások ügynöke pedig minden Hyper-V állomásra. Leképezheti a VMM logikai/virtuális gép hálózatok és az Azure virtuális hálózatok között.
- [További információ](hyper-v-azure-architecture.md) a Hyper-V és az Azure architektúra.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Replikálhatom a Hyper-V-fürtön található virtuális gépeket?

Igen, a Site Recovery támogatja a fürtözött Hyper-V állomásokat. Vegye figyelembe:

- A fürt összes csomópontját ugyanahhoz a tárolóhoz kell regisztrálni.
- Ha nem használja a VMM-et, a fürt összes Hyper-V állomását hozzá kell adni ugyanahhoz a Hyper-V-helyhez.
- Az Azure Site Recovery Provider and Recovery Services ügynököt a fürt minden Hyper-V állomására telepíti, és minden állomást hozzáad egy Hyper-V-helyhez.
- A fürtön nincs szükség konkrét lépésekre.
- Ha a Deployment Planner eszközt futtatja a Hyper-V-höz, az eszköz összegyűjti a profiladatokat a futó csomópontról, és ahol a virtuális gép fut. Az eszköz nem tud adatokat gyűjteni egy kikapcsolt csomópontról, de nyomon követi a csomópontot. A csomópont működése után az eszköz megkezdi a virtuális gép profiladatainak gyűjtését (ha a virtuális gép a profil virtuálisgép-listájának része, és a csomóponton fut).
- Ha egy Site Recovery-tárolóban egy Hyper-V gazdagépen lévő virtuális gép ugyanabban a fürtben lévő másik Hyper-V-állomásra vagy egy önálló állomásra telepíti át a virtuális gép replikációját, ez nem érinti. A Hyper-V állomásnak meg kell [felelnie az előfeltételeknek,](hyper-v-azure-support-matrix.md#on-premises-servers)és konfigurálnia kell egy hely-helyreállítási tárolóban. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Védhetem a virtuális gépeket, ha a Hyper-V ügyféloperációs rendszeren fut?
Nem. A virtuális gépeknek egy támogatott Windows kiszolgáló gépen futó Hyper-V gazdakiszolgálón kell lenniük. Ha egy ügyfélszámítógép védelmére van szüksége, [replikálhatja azt fizikai gépként az](physical-azure-disaster-recovery.md) Azure-ba.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Replikálható Hyper-V 2. generációs virtuális gép az Azure-ba?
Igen. A Site Recovery a feladatátvétel során a 2-es generációról az 1.generációra konvertáló. Feladat-visszavételkor a gép visszaváltanak a 2- es generációra.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálhatom a hely-helyreállítási forgatókönyveket SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. Jelenleg támogatott forgatókönyvek a Hyper-V Azure-ba történő replikálására a PowerShell használatával:

- [Hyper-V replikálása VMM nélkül a PowerShell használatával](hyper-v-azure-powershell-resource-manager.md)
- [Hyper-V replikálása VMM-mel a Powershell használatával](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikáció

### <a name="where-do-on-premises-vms-replicate-to"></a>Hol a helyszíni virtuális gépek replikálódik?
Az adatok replikálódnak az Azure storage-ba. Feladatátvétel futtatásakor a Site Recovery automatikusan létrehozza az Azure virtuális gépeket a tárfiókból.

### <a name="what-apps-can-i-replicate"></a>Milyen alkalmazásokat replikálhatok?
A [replikációs követelményeknek](hyper-v-azure-support-matrix.md#replicated-vms)megfelelő, Hyper-V virtuális gépen futó bármely alkalmazást vagy számítási feladatot replikálhat. A Site Recovery támogatja az alkalmazásbarát replikációt, így az alkalmazások feladatátvételt és az intelligens állapotba való visszaadást is lehetővé teszik. A Site Recovery integrálható a Microsoft alkalmazásaival, például a SharePointtal, az Exchange-szel, a Dynamics-szal, az SQL Serverrel és az Active Directoryval, és szorosan együttműködik a vezető gyártókkal, például az Oracle-lel, az SAP-val, az IBM-mel és a Red Hat-tal. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="whats-the-replication-process"></a>Mi a replikációs folyamat?

1. A kezdeti replikáció aktiválásakor egy Hyper-V VM pillanatkép készül.
2. Virtuális merevlemezek a virtuális gépen replikálódik egyenként, amíg ők minden másolt az Azure-ba. Ez eltarthat egy ideig, a virtuális gép méretétől és a hálózati sávszélességtől függően. További információ a hálózati sávszélesség növeléséről.
3. Ha a lemez változások a kezdeti replikáció közben történnek, a Hyper-V replikációs követő hyper-V replikációs naplóként (.hrl) követi a módosításokat. Ezek a naplófájlok ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy társított .hrl fájl, amelyet a rendszer a másodlagos tárolóba küld. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét.
5. A naplóban lévő lemezmódosítások szinkronizálása és egyesítése a szülőlemezre történik.
6. A kezdeti replikáció befejezése után a Véglegesítés védelem a virtuális gép feladat fut. Konfigurálja a hálózati és egyéb replikáció utáni beállításokat, így a virtuális gép védett.
7. Ebben a szakaszban ellenőrizheti a virtuális gép beállításait, hogy megbizonyosodjon arról, hogy készen áll a feladatátvételre. Futtathat egy vész-helyreállítási gyakorlatot (teszt feladatátvétel) a virtuális gép, annak ellenőrzésére, hogy a feladatátvétel várt módon.
8. A kezdeti replikáció után megkezdődik a különbözeti replikáció a replikációs házirendnek megfelelően.
9. A módosítások naplózva .hrl fájlokat. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl.
10. A napló az ügyfél tárfiókjába kerül. Amikor egy napló az Azure-ba kerül átvitelalatt, az elsődleges lemez módosításait egy másik naplófájlban, ugyanabban a mappában követi nyomon a rendszer.
11. A kezdeti és a különbözeti replikáció során figyelheti a virtuális gép az Azure Portalon.

[További információ](hyper-v-azure-architecture.md#replication-process) a replikációs folyamatról.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Replikálható az Azure-ba egy helyek közötti VPN?Can I Replicate to Azure with a site-to-site VPN?

A Site Recovery replikálja az adatokat a helyszíni azure-tárolóból egy nyilvános végponton keresztül, vagy expressroute-i Microsoft-társviszony-létesítés használatával. A helyek közötti VPN-hálózaton keresztüli replikáció nem támogatott.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Replikálható az Azure-ba az ExpressRoute segítségével?

Igen, expressroute segítségével replikálható virtuális gépek az Azure-ba. A Site Recovery replikálja az adatokat egy Azure Storage-fiókba egy nyilvános végponton keresztül, és be kell állítania a [Microsoft társviszony-létesítést](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) a Site Recovery replikációhoz. Miután a virtuális gépek átadják a feladatátvételt egy Azure virtuális hálózatra, [privát társviszony-létesítéssel](../expressroute/expressroute-circuit-peerings.md#privatepeering)érheti el őket.


### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem tudok vpn-en keresztül replikálni?

Amikor replikálja az Azure-ba, replikációs forgalom eléri a nyilvános végpontok egy Azure Storage-fiók. Így csak az ExpressRoute (Microsoft társviszony-létesítés) segítségével replikálható a nyilvános interneten keresztül, és a VPN nem működik. 

### <a name="what-are-the-replicated-vm-requirements"></a>Mik a replikált virtuális gép követelményei?

A replikációhoz a Hyper-V virtuális gépnek támogatott operációs rendszert kell futtatnia. Emellett a virtuális gépnek meg kell felelnie az Azure virtuális gépek követelményeinek. [További információ](hyper-v-azure-support-matrix.md#replicated-vms) a támogatási mátrixban.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatok az Azure-ba?

A Hyper-V virtuális gépek 30 másodpercenként replikálhatók (kivéve a prémium szintű tárhelyet), 5 perc vagy 15 perc.

### <a name="can-i-extend-replication"></a>Kiterjeszthetem a replikációt?
A kiterjesztett vagy láncolt replikáció nem támogatott. Kérje ezt a funkciót [a visszajelzés fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>El tudom végezni az offline kezdeti replikációt?
Ez a funkció nem támogatott. Kérje ezt a funkciót a [visszajelzésfórumban](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kizárhatom a lemezeket?
Igen, kizárhatja a lemezeket a replikációból. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Replikálható virtuális gépek dinamikus lemezekkel?
A dinamikus lemezek replikálhatók. Az operációs rendszer lemezének alaplemeznek kell lennie.



## <a name="security"></a>Biztonság

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Milyen hozzáférésre van szüksége a Site Recovery-nek a Hyper-V gazdagépekhez?

A Site Recovery-nek hozzáférésre van szüksége a Hyper-V gazdagépekhez a kiválasztott virtuális gépek replikálásához. A Site Recovery a következőket telepíti a Hyper-V gazdagépekre:

- Ha nem a VMM-et futtatja, az Azure Site Recovery Provider és a Recovery Services ügynök minden állomásra telepítve van.
- Ha VMM-et futtat, a Helyreállítási szolgáltatások ügynöke minden állomásra telepítve van. A szolgáltató a VMM-kiszolgálón fut.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Mit telepít a Site Recovery a Hyper-V virtuális gépekre?

A Site Recovery nem telepít kifejezetten semmit a replikációra engedélyezett Hyper-V virtuális gépeken.




## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel


### <a name="how-do-i-fail-over-to-azure"></a>Hogyan lehet átkerülni az Azure-ba?

Futtathat tervezett vagy nem tervezett feladatátvételt a helyszíni Hyper-V virtuális gépekről az Azure-ra.

- Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
- Futtathat nem tervezett feladatátvételt, ha az elsődleges hely nem érhető el.
- Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó helyreállítási terveket is.
- A feladatátvétel két részből áll:
    - Miután a feladatátvétel első szakasza befejeződött, látnia kell a létrehozott replika virtuális gépek az Azure-ban. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges.
    - Ezután véglegesíti a feladatátvételt, hogy megkezdje a számítási feladatok elérését az Azure virtuális gép replikájáról.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan érhetem el az Azure virtuális gépek feladatátvétel után?
Feladatátvétel után biztonságos internetkapcsolaton, helyek közötti VPN-en vagy Azure ExpressRoute-on keresztül érheti el az Azure virtuális gépeket. A csatlakozáshoz számos dolgot elő kell készítenie. [További információ](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>A feladatátvétel imaszilika az adatok felett?
Az Azure-t hibatűrőnek terveztük. A Site Recovery egy másodlagos Azure-adatközpontba való feladatátvételre van tervezve, az Azure SLA-nak megfelelően. Feladatátvétel esetén gondoskodunk arról, hogy a metaadatok és a tárolók ugyanabban a földrajzi régióban maradjanak, amelyet a tárolóhoz választott.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
[A feladatátvétel](site-recovery-failover.md) nem automatikus. Feladatátvételt kezdeményez egyetlen kattintással a portálon, vagy a [PowerShell](/powershell/module/az.recoveryservices) használatával elindíthatja a feladatátvételt.

### <a name="how-do-i-fail-back"></a>Hogyan lehet visszakapni?

Miután a helyszíni infrastruktúra újra működik, visszaléphet. A feladat-visszavétel három szakaszban történik:

1. Az Azure-ból a helyszíni webhelyre tervezett feladatátvételt néhány különböző lehetőség használatával indítja el:

    - Állásidő minimalizálása: Ha ezt a beállítást használja, a Site Recovery szinkronizálja az adatokat a feladatátvétel előtt. Ellenőrzi a módosított adatblokkokat, és letölti őket a helyszíni webhelyre, miközben az Azure virtuális gép folyamatosan fut, minimalizálva az állásidőt. Ha manuálisan adja meg, hogy a feladatátvétel befejeződnie kell, az Azure virtuális gép leáll, a végső különbözeti módosítások másolása, és a feladatátvétel elindul.
    - Teljes letöltés: Ezzel a beállítással az adatok szinkronizálása a feladatátvétel során történik. Ez a beállítás letölti a teljes lemezt. Ez gyorsabb, mert nincs ellenőrző összeg számítva, de van több állásidő. Akkor használja ezt a beállítást, ha már egy ideje futtatja az Azure-beli virtuális gépek replikáját, vagy ha a helyszíni virtuális gépet törölték.

2. Kiválaszthatja, hogy adja vissza ugyanazt a virtuális gép, vagy egy másik virtuális gép. Megadhatja, hogy a Site Recovery létre kell hoznia a virtuális gép, ha még nem létezik.
3. A kezdeti szinkronizálás befejezése után a feladatátvétel befejezését választja. Miután befejeződött, bejelentkezhet a helyszíni virtuális gépbe, hogy ellenőrizze, hogy minden a várt módon működik-e. Az Azure Portalon láthatja, hogy az Azure-beli virtuális gépek leálltak.
4. Véglegesíti a feladatátvételt, hogy befejezze, és indítsa el a számítási feladatok elérése a helyszíni virtuális gép újra.
5. Miután a számítási feladatok visszavétele után engedélyezi a fordított replikációt, hogy a helyszíni virtuális gépek ismét replikáljanak az Azure-ba.

### <a name="can-i-fail-back-to-a-different-location"></a>Visszatehetek egy másik helyre?
Igen, ha feladatátvételt végzett az Azure-ba, visszavehetegy másik helyre, ha az eredeti nem érhető el. [További információ](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
