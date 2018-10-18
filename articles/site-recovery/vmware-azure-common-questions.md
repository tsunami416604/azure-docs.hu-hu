---
title: Gyakori kérdések – VMware-ből az Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk összefoglalja a gyakori kérdésekre, az Azure-ban az Azure Site Recovery a helyszíni VMware virtuális gépek replikálásakor
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 07/19/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 487e0c763ca4b247f1818b2beaf3282734fc4e27
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388442"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Gyakori kérdések – VMware-ből az Azure-bA

Ez a cikk gyakori kérdéseket, láthatjuk, ha a helyszíni VMware virtuális gépek replikálása Azure-bA. Ha kérdése van a cikk elolvasása után, el őket az a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Általános kérdések
### <a name="how-is-site-recovery-priced"></a>Hogyan van a Site Recovery díjszabása?
Felülvizsgálat [Azure Site Recovery díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/) részleteit.

### <a name="how-do-i-pay-for-azure-vms"></a>Hogyan kell fizetnem az Azure virtuális gépek?
A replikáció során az adatok az Azure storage replikációja, és nem kell fizetnie a virtuális gép módosításokat. Ha feladatátvételt végez az Azure-ba, a Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket. Ezt követően a számlázás az Azure-ban felhasznált számítási erőforrások.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Mire használhatom az Azure-bA VMware-rel?
- **Vész-helyreállítási**: teljes vész-helyreállítási beállíthat. Ebben a forgatókönyvben a helyszíni VMware virtuális gépeket az Azure storage-replikáció. Ezt követően a helyszíni infrastruktúra nem érhető el, ha is átadja a feladatokat az Azure-bA. Amikor feladatátvételt hajt végre, az Azure virtuális gépek jönnek létre a replikált adatok felhasználásával. Alkalmazások és számítási feladatok az Azure virtuális gépeken, amíg a helyszíni adatközpont újra elérhetővé hozzáférhet. Ezután Ön is nem az Azure-ból a helyszíni helyre.
- **Áttelepítési**: a Site Recovery segítségével a helyszíni VMware virtuális gépek áttelepítése az Azure-bA. Ebben a forgatókönyvben a helyszíni VMware virtuális gépeket az Azure storage-replikáció. Ezt követően átadja a feladatokat a helyszínről az Azure-bA. A feladatátvételt követően az alkalmazások és számítási feladatok rendelkezésre állnak és futó Azure virtuális gépeken.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Mi szükséges az Azure-ban?
Azure-előfizetés, egy Recovery Services-tárolót, egy storage-fiókot és egy virtuális hálózaton van szüksége. A tároló, a storage-fiók és a hálózati ugyanabban a régióban kell lennie.

### <a name="what-azure-storage-account-do-i-need"></a>Milyen Azure-tárfiókra van szükségem?
Az LRS vagy GRS tárfiókra van szükség. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A Premium storage használata támogatott.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Nem a saját Azure-fiók létrehozása a virtuális gépek van szüksége?
Ha Ön olyan előfizetés rendszergazdája, akkor a replikációs szükséges engedélyekkel. Ha nem Ön, szüksége van egy Azure virtuális gép létrehozása az az erőforráscsoport és a Site Recovery konfigurálásakor megadott virtuális hálózat és a kiválasztott tárfiók írási engedélyekkel. [További információk](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Helyszíni követelmények

### <a name="what-do-i-need-on-premises"></a>Mire van szükségem helyszíni?
A helyszíni Site Recovery-összetevőit egyetlen VMware virtuális gépeken telepített kell. Legalább egy ESXi-gazdagépen VMware-infrastruktúrára is szüksége, és azt javasoljuk, hogy a vCenter-kiszolgáló. Ezenkívül egy vagy több VMware virtuális gépek replikálásához szüksége. [További](vmware-azure-architecture.md) VMware-ből az Azure-architektúra kapcsolatban.

A helyszíni konfigurációs kiszolgálón is üzembe helyezhetők az alábbi két módszer közül

1. Helyezze üzembe azt a Virtuálisgép-sablont, amely rendelkezik a konfigurációs kiszolgáló előre telepítve van. [Itt olvashat](vmware-azure-tutorial.md#download-the-vm-template).
2. Helyezze üzembe azt egy Windows Server 2016 gépen tetszőleges telepítővel. [Itt olvashat](physical-azure-disaster-recovery.md#set-up-the-source-environment).

Az első lépések a konfigurációs kiszolgáló a saját Windows Server-gépek, a védelem engedélyezése a védelmi cél történő telepítésének lépésein áruházakból **az Azure-bA > nem virtualizált/egyéb**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Ha ehhez a helyszíni virtuális gépek replikálása?
Az Azure storage replikálja az adatokat. Feladatátvétel futtatásakor a Site Recovery automatikusan létrehozza az Azure virtuális gépek a tárfiókból.

### <a name="what-apps-can-i-replicate"></a>Mely alkalmazások replikálhatok?
Bármilyen alkalmazást vagy a VMware virtuális gép, amely megfelel az számítási feladatot replikálhat [replikációs követelményeit](vmware-physical-azure-support-matrix.md##replicated-machines). A Site Recovery támogatja az alkalmazásbarát replikációt, hogy az alkalmazások feladatátvételt, és újra működőképes állapotban nem sikerült. A Site Recovery integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory, és szorosan együttműködik az olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Replikálás az Azure site-to-site VPN-nel is?
A Site Recovery replikálja az adatokat a helyszínről az Azure storage egy nyilvános végpontot, vagy használja az ExpressRoute nyilvános társviszony-létesítés. Site-to-site VPN hálózaton keresztül a replikáció nem támogatott.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Replikálás az Azure ExpressRoute használatával is?
Igen, az ExpressRoute segítségével virtuális gépek replikálása az Azure-bA. A Site Recovery replikálja az adatokat egy Azure Storage-fiók egy nyilvános végpontot keresztül, és be kell állítania [nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) a Site Recovery replikációjára. Miután a virtuális gépek átadja a feladatokat az Azure virtuális hálózat, elérheti azokat használó [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem tudja replikálni VPN-kapcsolaton keresztül?

Az Azure-bA replikálja, amikor replikációs forgalom eléri a nyilvános végpontokat az Azure Storage-fiók, így csak replikálhatja az expressroute-tal (nyilvános társviszony-létesítés) a nyilvános interneten keresztül, és VPN nem működik.



## <a name="what-are-the-replicated-vm-requirements"></a>Mik azok a replikált virtuális gépek követelményeinek?

A replikáció VMware virtuális gép futnia kell egy támogatott operációs rendszert. Emellett a virtuális gép meg kell felelnie az Azure-beli virtuális gépek követelményeinek. [További](vmware-physical-azure-support-matrix.md##replicated-machines) a támogatási mátrixa.

## <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatja az Azure-bA?
Replikáció Azure-bA replikált VMware virtuális gépek esetén a folyamatos.

## <a name="can-i-extend-replication"></a>Ki lehet terjeszteni a replikációt?
A kiterjesztett vagy láncolt replikáció nem támogatott. Ennek a funkciónak a kérelem [Visszajelzési fórum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>Használhatom az offline kezdeti replikációt?
Ez a funkció nem támogatott. Ennek a funkciónak a kérelem a [Visszajelzési fórum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kizárhatok egyes lemezek?
Igen, kizárhat lemezeket a replikációból.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Virtuális gépek replikálhatok dinamikus lemezeken?
A dinamikus lemezek lehet replikálni. Az operációsrendszer-lemez alaplemeznek kell lennie.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>A virtuális gépre kiterjedő konzisztencia replikációs csoportok használata esetén is hozzáadhatok egy új virtuális gép egy meglévő replikációs csoporthoz?
Igen, adhat hozzá új virtuális gépek egy meglévő replikációs csoporthoz. Ha engedélyezi azok replikációját. Nem adhat egy virtuális Gépet egy meglévő replikációs csoporthoz, miután replikációs indul, és a meglévő virtuális gépek egy replikációs csoport nem hozható létre.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Módosíthatja a hozzáadásával vagy a lemezek átméretezése replikáló virtuális gépek?

A VMware – Azure replikálás módosíthatja a lemez méretét. Ha azt szeretné, adjon hozzá új lemezeket kell a lemezt adja hozzá, és engedélyezheti a virtuális gép védelmét.

## <a name="configuration-server"></a>Konfigurációs kiszolgáló

### <a name="what-does-the-configuration-server-do"></a>Hogyan működik a konfigurációs kiszolgálót?
A konfigurációs kiszolgáló futtatja a helyszíni Site Recovery-összetevők, beleértve:
- A konfigurációs kiszolgáló koordinálja a helyszíni és Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítással optimalizálja őket majd küld, hogy az Azure storage-., a folyamatkiszolgáló Ezenfelül telepíti a mobilitási szolgáltatás a virtuális gépeket replikálhat, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A fő célkiszolgálón, amely az Azure-ból a feladat-visszavétel során kezeli a replikációs adatokat.

### <a name="where-do-i-set-up-the-configuration-server"></a>Ha a konfigurációs kiszolgáló beállítása?
A konfigurációs kiszolgálón egy egyetlen magas rendelkezésre állású helyszíni VMware virtuális gép van szükség.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Mik azok a konfigurációs kiszolgálónak a követelményei?

Tekintse át a [Előfeltételek](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kézzel állítható be a konfigurációs kiszolgálót, egy sablon használata helyett?
Azt javasoljuk, hogy használja-e a legújabb verzióra az OVF-sablon [a konfigurációs kiszolgáló virtuális gép létrehozása](vmware-azure-deploy-configuration-server.md). Ha valamilyen okból nem lehet, például nem rendelkezik a VMware-kiszolgálóhoz való hozzáférést, akkor [töltse le az egyesített telepítő fájlját](physical-azure-set-up-source.md) a portálról, és futtassa azt a virtuális gép.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Konfigurációs kiszolgáló replikálhatja egynél több régióban?
Nem. Ehhez állítsa be a konfigurációs kiszolgáló az egyes régiókban kell.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Üzemeltethet a konfigurációs kiszolgáló az Azure-ban?
Bár lehetséges a konfigurációs kiszolgálón futó Azure virtuális gép kell a helyszíni VMware-infrastruktúra és a virtuális gépek kommunikálnak. Ez adja hozzá a késésük, és hatással van a folyamatban lévő replikáció.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Hol kaphatok a konfigurációs kiszolgálói sablon legújabb verzióját?
Töltse le a legújabb verziót a [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Hogyan frissíthetem a konfigurációs kiszolgálót?
Kumulatív frissítés telepítése. A legújabb frissítés információkat a a [frissítések wikioldal](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

### <a name="should-i-backup-the-deployed-configuration-server"></a>Kell-e biztonsági a központilag telepített konfigurációs kiszolgálót?
Azt javasoljuk, hogy a konfigurációs kiszolgáló rendszeres ütemezett biztonsági másolatok készítése. A sikeres feladat-visszavételhez a virtuális gép folyamatban van a feladatátvételben szerepelniük kell a konfigurációs kiszolgáló adatbázisát, és a konfigurációs kiszolgálón fut, és a egy csatlakoztatott állapotban kell lennie. További információ a konfigurációs kiszolgáló gyakori felügyeleti feladatok [Itt](vmware-azure-manage-configuration-server.md).

## <a name="mobility-service"></a>Mobilitási szolgáltatás

### <a name="where-can-i-find-the-mobility-service-installers"></a>Hol található a mobilitási szolgáltatás telepítőcsomagokat?
A telepítőcsomagokat tartják a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** mappát a konfigurációs kiszolgálón.

## <a name="how-do-i-install-the-mobility-service"></a>Hogyan kell telepíteni a mobilitási szolgáltatás?
Telepíti az egyes virtuális Gépeken szeretne replikálni, használatával egy [leküldéses telepítése](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery), vagy történő manuális telepítést [a felhasználói felület](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui), vagy [PowerShell-lel](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). Azt is megteheti, telepítheti, mint például egy üzembe helyezési eszközzel [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md), vagy [Azure Automation és a DSC](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Biztonság

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Milyen hozzáférést kell a Site Recovery VMware-kiszolgálók?
A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Állítsa be a VMware virtuális gép fut a konfigurációs kiszolgálót, és a helyszíni Site Recovery egyéb összetevőit. [További információ](vmware-azure-deploy-configuration-server.md)
- Replikáció a virtuális gépek felderítéséhez. Ismerje meg a fiók előkészítése automatikus felderítéshez. [További információ](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Milyen hozzáférést kell a Site Recovery VMware virtuális gépeket?

- Annak érdekében, hogy a replikációt, egy VMware virtuális gép a Site Recovery mobilitási szolgáltatást telepítenie és futtatnia kell rendelkeznie. Az eszköz telepítheti manuálisan, vagy adja meg, hogy a Site Recovery kell tennie a leküldéses telepítését a szolgáltatást, amikor engedélyezi egy virtuális gép replikációját. A leküldéses telepítéshez a Site recoverynek egy fiókot, amely a szolgáltatás telepítésekor használhatja. [További információk](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). A folyamatkiszolgáló (futtató alapértelmezés szerint a konfigurációs kiszolgálón) hajt végre a telepítést. [További](vmware-azure-install-mobility-service.md) kapcsolatos telepíteni a mobilitási szolgáltatást.
- A replikáció során virtuális gépek kommunikálnak a Site Recovery a következő:
    - Virtuális gépek kommunikálnak a konfigurációs kiszolgáló 443-as porton HTTPS kezelése érdekében a replikálást.
    - Virtuális gépek küldhetnek replikációs adatokat a folyamatkiszolgálónak, a HTTPS 9443-as porton (módosítható).
    - Ha engedélyezi a virtuális gépre kiterjedő konzisztencia, virtuális gépek kommunikálnak egymással 20004-es porton.


### <a name="is-replication-data-sent-to-site-recovery"></a>Site Recovery számára küldött adatokat?
Nem, a Site Recovery nem intercept a replikált adatokat, és nem rendelkezik semmilyen információval, hogy a virtuális gépeken futó. Replikációs adatcsere VMware-hipervizorok és az Azure storage között. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

Site Recovery szolgáltatás ISO 27001:2013, 27018, a HIPAA, DPA hitelesített, és SOC2 és FedRAMP JAB folyamatban van.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Is tárolódik a helyi metaadatok földrajzi régiók?
Igen. Ha olyan régióban hozzon létre egy tárolót, biztosítható, hogy minden metaadat használni a Site Recovery továbbra is adott régióban földrajzi határ belül.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Igen, mindkét – az átvitel közbeni titkosítás és [titkosítás az Azure-ban](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.


## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel
### <a name="how-far-back-can-i-recover"></a>Milyen biztonsági állíthatja helyre?
VMware-ről az Azure-bA a legrégebbi helyreállítási pont használható érték 72 óra.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan érhetem el az Azure virtuális gépek a feladatátvételt követően
A feladatátvételt követően elérheti az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, egy helyek közötti VPN-kapcsolaton keresztül, vagy Azure expressroute-on keresztül. Készítse elő a számos dolgot, hogy csatlakozni kell. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Nem sikerült rugalmas adatokon?
Az Azure-t hibatűrőnek terveztük. A Site Recovery a feladatátvétel egy másodlagos Azure adatközpontba, az Azure SLA kategóriában. Feladatátvétel esetén biztosítjuk, hogy a metaadatokat, és a tárolók maradjanak ugyanabban a földrajzi régióban, a tároló számára is választott.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
[Feladatátvétel](site-recovery-failover.md) nem automatikus. A portálon egyetlen kattintással feladatátvételt, vagy használhat [ PowerShell](/powershell/module/azurerm.siterecovery) feladatátvétel indításához.

### <a name="can-i-fail-back-to-a-different-location"></a>E visszaadhatja egy másik helyre?
Igen, ha az Azure-bA feladatátvétel, visszaadhatja a másik helyet, ha az eredeti kapcsolatot nem érhető el. [További információk](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Miért kell egy VPN- vagy ExpressRoute visszavételhez?

Ha nem sikerül az Azure-ból, Azure származó adatokat másolja vissza a helyszíni virtuális gép, és privát hozzáférésre szükség.



## <a name="automation-and-scripting"></a>Automatizálás és parancsfájl-kezelési

### <a name="can-i-set-up-replication-with-scripting"></a>Állítható be a parancsfájl-replikáció?
Igen. A Rest API, PowerShell vagy az Azure SDK használatával a Site Recovery munkafolyamatainak automatizálható. [További](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Teljesítmény és kapacitás
### <a name="can-i-throttle-replication-bandwidth"></a>Szabályozhatja a sávszélesség?
Igen. [További információk](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>További lépések
* [Felülvizsgálat](vmware-physical-azure-support-matrix.md) követelmények támogatására.
* [Állítsa be a](vmware-azure-tutorial.md) VMware-ből az Azure-bA.
