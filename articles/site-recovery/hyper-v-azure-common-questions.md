---
title: Gyakori kérdések – a Hyper-V – Azure vészhelyreállítás az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk összefoglalja a vész-helyreállítási beállítása a helyszíni Hyper-V virtuális gépeket az Azure-bA az Azure Site Recovery-hely kapcsolat használatával kapcsolatos gyakori kérdésekre.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 03/18/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 4888d019065e557cb49574e2268515323b3fd005
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310704"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Gyakori kérdések – Hyper-V – Azure-beli vészhelyreállításához

Ez a cikk gyakori kérdéseket, láthatjuk, ha a helyszíni Hyper-V virtuális gépek replikálása Azure-bA. 


## <a name="general"></a>Általános kérdések

### <a name="how-is-site-recovery-priced"></a>Hogyan van a Site Recovery díjszabása?
Felülvizsgálat [Azure Site Recovery díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/) részleteit.

### <a name="how-do-i-pay-for-azure-vms"></a>Hogyan kell fizetnem az Azure virtuális gépek?
A replikáció során az adatok az Azure storage replikációja, és nem kell fizetnie a virtuális gép módosításokat. Ha feladatátvételt végez az Azure-ba, a Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket. Ezt követően a számlázás az Azure-ban felhasznált számítási erőforrások.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-azure"></a>Mi szükséges az Azure-ban?
Azure-előfizetés, egy Recovery Services-tárolót, egy storage-fiókot és egy virtuális hálózaton van szüksége. A tároló, a storage-fiók és a hálózati ugyanabban a régióban kell lennie.

### <a name="what-azure-storage-account-do-i-need"></a>Milyen Azure-tárfiókra van szükségem?
Az LRS vagy GRS tárfiókra van szükség. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A Premium storage használata támogatott.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Nem a saját Azure-fiók létrehozása a virtuális gépek van szüksége?
Ha Ön olyan előfizetés rendszergazdája, akkor a replikációs szükséges engedélyekkel. Ha nem Ön, szüksége van egy Azure virtuális gép létrehozása az az erőforráscsoport és a Site Recovery konfigurálásakor megadott virtuális hálózat és a kiválasztott tárfiók írási engedélyekkel. [További információk](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Site Recovery számára küldött adatokat?
Nem, a Site Recovery nem intercept a replikált adatokat, és nem rendelkezik semmilyen információval, hogy a virtuális gépeken futó. Replikációs adatcsere a Hyper-V-gazdagépek és az Azure storage között. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

Site Recovery szolgáltatás ISO 27001:2013, 27018, a HIPAA, DPA hitelesített, és SOC2 és FedRAMP JAB folyamatban van.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Is tárolódik a helyi metaadatok földrajzi régiók?
Igen. Ha olyan régióban hozzon létre egy tárolót, biztosítható, hogy minden metaadat használni a Site Recovery továbbra is adott régióban földrajzi határ belül.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Igen, mindkét – az átvitel közbeni titkosítás és [titkosítás az Azure-ban](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.


## <a name="deployment"></a>Környezet

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Mire használhatom az Azure-bA Hyper-V-vel?

- **Vész-helyreállítási**: Beállíthat teljes vészhelyreállítás. Ebben a forgatókönyvben a helyszíni Hyper-V virtuális gépeket az Azure storage replikáció:
    - Virtuális gépek az Azure-bA replikálhatja. A helyszíni infrastruktúra nem érhető el, ha átadja a feladatokat az Azure-bA.
    - Amikor feladatátvételt hajt végre, az Azure virtuális gépek jönnek létre a replikált adatok felhasználásával. Alkalmazások és számítási feladatok az Azure virtuális gépeken is elérheti.
    - Ha a helyszíni adatközpont újra elérhetővé válik, is átadja az Azure-ból a helyszíni helyre.
- **Áttelepítési**: A Site Recovery használatával a helyszíni Hyper-V virtuális gépek áttelepítése az Azure storage. Ezt követően átadja a feladatokat a helyszínről az Azure-bA. A feladatátvételt követően az alkalmazások és számítási feladatok rendelkezésre állnak és futó Azure virtuális gépeken.


### <a name="what-do-i-need-on-premises"></a>Mire van szükségem helyszíni?

Egy vagy több virtuális gépet kell egy vagy több önálló vagy fürtözött Hyper-V-gazdagépeken futó. A System Center Virtual Machine Manager (VMM) által felügyelt gazdagépeken futó virtuális gépeket is replikálhat.
- Ha a VMM-ben, a Site Recovery üzembe helyezése során nem futtatja, gyűjtse össze a Hyper-V-gazdagépek és fürtök Hyper-V helyek be. A Site Recovery-ügynökök (az Azure Site Recovery Provider és Recovery Services agent) minden egyes Hyper-V gazdagépen telepítenie.
- Ha a Hyper-V-gazdagépek VMM-felhőben található, akkor koordinálhatja a replikálást, a VMM-ben. A Site Recovery Providert a VMM-kiszolgáló és a Recovery Services-ügynököt minden Hyper-V gazdagépre telepíti. Képezze le a VMM logikai és Virtuálisgép-hálózatok és az Azure virtuális hálózatok között.
- [További](hyper-v-azure-architecture.md) Hyper-V Azure-ra architektúra kapcsolatban.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>A Hyper-V fürtön elhelyezkedő virtuális gépeket lehet replikálni?

Igen, a Site Recovery támogatja a fürtözött Hyper-V-gazdagépeket. Vegye figyelembe:

- A fürt minden csomópontján ugyanahhoz a tárolóhoz kell regisztrálni.
- Ha a VMM nem használ, a fürt összes Hyper-V gazdagépeket hozzá kell azonos Hyper-V helyhez.
- Az Azure Site Recovery Provider és Recovery Services-ügynök telepítése a fürt minden egyes Hyper-V gazdagépen, és adjon hozzá egy Hyper-V helyhez, hogy minden gazdagépen.
- Adott lépés nem kell tenni a fürtön.
- Ha futtatta a Deployment Planner eszköz a Hyper-V, az eszköz a profil adatait gyűjti a csomópont, amely fut, és ahol a virtuális gép fut-e. Az eszköz nem gyűjt adatokat a csomópont, amely ki van kapcsolva, de azt fogja követni a csomóponton. Miután a csomópont helyezheti üzembe, az eszköz elindul, VM profil adatok gyűjtése eszközökről (Ha a virtuális gép a profilt Virtuálisgép-lista egy részét, és a csomóponton fut).
- Ha egy virtuális Gépet egy Hyper-V-gazdagépen, a Site Recovery-tároló ugyanazon fürt egy másik Hyper-V-gazdagépre, vagy egy önálló gazdagépre telepíti át, a virtuális gép nem negatív. A Hyper-V-gazdagépnek teljesítenie kell [Előfeltételek](hyper-v-azure-support-matrix.md#on-premises-servers), és a Site Recovery-tároló lehet beállítani. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Képes virtuális gépek védelme, ha Hyper-V egy ügyfél operációs rendszeren fut?
Nem. A virtuális gépeknek egy támogatott Windows kiszolgáló gépen futó Hyper-V gazdakiszolgálón kell lenniük. Ha védeni kell sikerült [replikálhatja azt fizikai gépként](physical-azure-disaster-recovery.md) az Azure-bA.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Replikálható Hyper-V 2. generációs virtuális gép az Azure-ba?
Igen. A Site Recovery a 2. generációs konvertálja az 1. a feladatátvétel során. Feladat-visszavételt, a gép vissza a 2. generációs alakítja át.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálhatja a Site Recovery-forgatókönyvek az SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. Jelenleg támogatott forgatókönyveket az Azure PowerShell-lel történő replikálásához a Hyper-V:

- [Replikálhatja a Hyper-V anélkül, hogy a VMM PowerShell használatával](hyper-v-azure-powershell-resource-manager.md)
- [Replikálásához a Hyper-V és a VMM Powershell használatával](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikáció

### <a name="where-do-on-premises-vms-replicate-to"></a>Ha ehhez a helyszíni virtuális gépek replikálása?
Az Azure storage replikálja az adatokat. Feladatátvétel futtatásakor a Site Recovery automatikusan létrehozza az Azure virtuális gépek a tárfiókból.

### <a name="what-apps-can-i-replicate"></a>Mely alkalmazások replikálhatok?
Bármilyen alkalmazás vagy munkaterhelés fut a Hyper-V virtuális gép, amely megfelel az replikálhatja [replikációs követelményeit](hyper-v-azure-support-matrix.md#replicated-vms). A Site Recovery támogatja az alkalmazásbarát replikációt, hogy az alkalmazások feladatátvételt, és újra működőképes állapotban nem sikerült. A Site Recovery integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory, és szorosan együttműködik az olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="whats-the-replication-process"></a>Mi az a replikálási folyamat?

1. Kezdeti replikáció akkor aktiválódik, amikor a rendszer egy Hyper-V virtuális gép pillanatképet készít.
2. Virtuális merevlemezek a virtuális gép replikált egy olyan, amíg átmásolja ezeket az Azure-bA. Előfordulhat, hogy ez eltarthat egy ideig a virtuális gép méretétől függően és a hálózati sávszélesség. Hálózati sávszélesség növelésével kapcsolatos további információkért.
3. Ha lemezt, miközben a kezdeti replikáció folyamatban van, a Hyper-V Replica Replication Tracker eszköz Hyper-V replikálási naplók (.hrl) formájában nyomon követi a módosításokat. Ezek a naplófájlok a lemezek ugyanabban a mappában találhatók. Minden lemezhez tartozik egy .hrl fájl, amelyet elküld a másodlagos tárhelyen. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét.
5. Bármely a naplóban rögzített változásokat szinkronizálja és egyesíti a szülőlemezzel.
6. A véglegesítés védelmet a virtuális gép feladat fut, a kezdeti replikáció befejezése után. Ez konfigurálja a hálózatot és más replikáció utáni beállításokat a virtuális gép védelméhez.
7. Ebben a szakaszban ellenőrizheti a virtuális gép beállításait, győződjön meg arról, hogy készen áll a feladatátvételre. A vészhelyreállítási próba végrehajtása (teszt feladatátvétel) a virtuális géphez, ellenőrizze a sikertelen lesz, mint a várt módon futtathatja.
8. A kezdeti replikációt követően változásreplikálás kezdődik, a replikáció szabályzatának megfelelően.
9. Módosítások a naplózott .hrl fájlokban. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl.
10. A napló az ügyfél tárfiókja küld. Ha a napló az átvitel során, az Azure-ba, az elsődleges lemez változásait ugyanabban a mappában egy másik naplófájlban követi nyomon.
11. Kezdeti és a változásreplikáció során a virtuális gép az Azure Portalon követheti nyomon.

[További](hyper-v-azure-architecture.md#replication-process) a replikációs folyamatról.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Replikálás az Azure site-to-site VPN-nel is?

A Site Recovery replikálja az adatokat a helyszínről az Azure storage egy nyilvános végpontot, vagy használja az ExpressRoute nyilvános társviszony-létesítés. Site-to-site VPN hálózaton keresztül a replikáció nem támogatott.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Replikálás az Azure ExpressRoute használatával is?

Igen, az ExpressRoute segítségével virtuális gépek replikálása az Azure-bA. A Site Recovery replikálja az adatokat egy Azure Storage-fiók egy nyilvános végpontot keresztül, és be kell állítania [nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#publicpeering) a Site Recovery replikációjára. Miután a virtuális gépek átadja a feladatokat az Azure virtuális hálózat, elérheti azokat használó [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem tudja replikálni VPN-kapcsolaton keresztül?

Az Azure-bA replikálja, amikor replikációs forgalom eléri a nyilvános végpontokat az Azure Storage-fiók, így csak replikálhatja az expressroute-tal (nyilvános társviszony-létesítés) a nyilvános interneten keresztül, és VPN nem működik. 

### <a name="what-are-the-replicated-vm-requirements"></a>Mik azok a replikált virtuális gépek követelményeinek?

A replikáció a Hyper-V virtuális gép futnia kell egy támogatott operációs rendszert. Emellett a virtuális gép meg kell felelnie az Azure-beli virtuális gépek követelményeinek. [További](hyper-v-azure-support-matrix.md#replicated-vms) a támogatási mátrixa.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatja az Azure-bA?

A Hyper-V virtuális gépek replikálhatók (kivéve a premium storage) 30 másodperc, 5 percenként vagy 15 perc.

### <a name="can-i-extend-replication"></a>Ki lehet terjeszteni a replikációt?
A kiterjesztett vagy láncolt replikáció nem támogatott. Ennek a funkciónak a kérelem [Visszajelzési fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Használhatom az offline kezdeti replikációt?
Ez a funkció nem támogatott. Ennek a funkciónak a kérelem a [Visszajelzési fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kizárhatok egyes lemezek?
Igen, kizárhat lemezeket a replikációból. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Virtuális gépek replikálhatok dinamikus lemezeken?
A dinamikus lemezek lehet replikálni. Az operációsrendszer-lemez alaplemeznek kell lennie.



## <a name="security"></a>Biztonság

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Milyen hozzáférést kell a Site Recovery a Hyper-V-gazdagépek

A Site Recovery replikálja a virtuális gépeket, jelölje be a Hyper-V gazdagépeket hozzá kell férnie. A Hyper-V-gazdagépek a Site Recovery telepíti a következő:

- Ha nem VMM-ben, az Azure Site Recovery Provider és Recovery Services Agent ügynököt minden gazdagépre telepítve van.
- Ha a VMM futtatja, a Recovery Services Agent ügynököt minden gazdagépre telepítve van. A szolgáltató fut, a VMM-kiszolgálón.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Mi a Site Recovery telepíti a Hyper-V virtuális gépeken?

A Site Recovery nem explicit módon telepít semmit a Hyper-V virtuális gépek engedélyezve van a replikáció.




## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel


### <a name="how-do-i-fail-over-to-azure"></a>Hogyan lehet feladatátvételt beállítani az Azure-bA?

A helyszíni Hyper-V virtuális gépek tervezett vagy nem tervezett feladatátvétel futtathatja az Azure-bA.
    - Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
    - Nem tervezett feladatátvételt is futtathatja, ha az elsődleges hely nem érhető el.
    - Egyetlen gép feladatátvételét, vagy több gép összehangolt feladatátadását helyreállítási terveket hozhat létre.
    - Feladatátvétel futtatása. Az első szakasz a feladatátvétel befejezése után megtekintheti a létrehozott replika virtuális gépek az Azure-ban kell lennie. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges. Ekkor véglegesíti a feladatátvételt, a munkaterhelés elérése a replika Azure virtuális gép elindításához.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan érhetem el az Azure virtuális gépek a feladatátvételt követően
A feladatátvételt követően elérheti az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, egy helyek közötti VPN-kapcsolaton keresztül, vagy Azure expressroute-on keresztül. Készítse elő a számos dolgot, hogy csatlakozni kell. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Nem sikerült rugalmas adatokon?
Az Azure-t hibatűrőnek terveztük. A Site Recovery a feladatátvétel egy másodlagos Azure adatközpontba, az Azure SLA kategóriában. Feladatátvétel esetén biztosítjuk, hogy a metaadatokat, és a tárolók maradjanak ugyanabban a földrajzi régióban, a tároló számára is választott.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
[Feladatátvétel](site-recovery-failover.md) nem automatikus. A portálon egyetlen kattintással feladatátvételt, vagy használhat [PowerShell](/powershell/module/azurerm.siterecovery) feladatátvétel indításához.

### <a name="how-do-i-fail-back"></a>Hogyan do I feladat-visszavételt?

Miután a helyszíni infrastruktúra újra működik, visszaadhatja a. Feladat-visszavétel három lépésben történik:

1. Elindít egy tervezett feladatátvételt az Azure-ból a helyszíni hely használatával több különböző lehetőség közül választhat:

    - Állásidő minimálisra csökkentése érdekében: Ha ezt a beállítást használja a Site Recovery szinkronizálja az adatokat a feladatátvétel előtt. Azt ellenőrzi, hogy megváltozott az adatblokkokat, és letölti azokat a helyszíni helyre, miközben az Azure virtuális gép megőrzi fut, minimálisra csökkentik az állásidőt. Manuálisan adja meg, hogy a feladatátvételt kell elvégezni, amikor az Azure virtuális gép leállt, bármely végső változásokat másolja, és a feladatátvétel indítása.
    - Teljes letöltés: Ez a beállítás az adatok szinkronizálása feladatátvétel során. Ez a beállítás a teljes lemez tölti le. Ez azért gyorsabban nincs ellenőrzőösszegek számítása, de több állásidőt. Használja ezt a beállítást, ha futtatja, a replika Azure virtuális gépek egy kis ideig, vagy ha a helyszíni virtuális gép törölve lett.

2. Választhatja azt, hogy hajtja végre, vissza ugyanazon a virtuális Gépen vagy egy másik virtuális géphez. Megadhatja, hogy a Site Recovery a virtuális Gépet kell létrehoznia, ha még nem létezik.
3. Miután a kezdeti szinkronizálás befejezését követően válassza ki a feladatátvétel végrehajtásához. Miután ez befejeződik, bejelentkezhetnek a helyszíni virtuális Gépre, és ellenőrizze, hogy minden a várt módon működik. Az Azure Portalon látható, hogy az Azure virtuális gépek lett leállítva.
4. Véglegesíti a feladatátvételt és a Befejezés ismét hozzáférhessen a számítási a helyszíni virtuális gépről.
5. Követően számítási feladatok biztonsági sikertelenek voltak, engedélyezheti a visszirányú replikálás, hogy a helyszíni virtuális gépek replikálása az Azure-bA újra.

### <a name="can-i-fail-back-to-a-different-location"></a>E visszaadhatja egy másik helyre?
Igen, ha az Azure-bA feladatátvétel, visszaadhatja a másik helyet, ha az eredeti kapcsolatot nem érhető el. [További információk](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment).
