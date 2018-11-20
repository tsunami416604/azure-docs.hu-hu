---
title: Gyakori kérdések – VMware-ből az Azure Site recoveryvel Azure-beli vészhelyreállításához |} A Microsoft Docs
description: Ez a cikk gyakori kérdésekre foglalja össze, amikor, vészhelyreállítás beállítása helyszíni VMware virtuális gépek az Azure-bA az Azure Site Recovery használatával
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 11/19/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 248b2a748088330f91b3cc76564d5d8743f04411
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162483"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Gyakori kérdések – VMware-ből az Azure-bA

Ez a cikk gyakori kérdéseket, láthatjuk, a helyszíni VMware virtuális gépek vészhelyreállítása az Azure-bA telepítésekor. Ha kérdése van a cikk elolvasása után, el őket az a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


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

A helyszíni lesz szüksége:
- Site Recovery-összetevőkhöz, egyetlen VMware virtuális Gépeken telepíthető.
- VMware-infrastruktúrára, legalább egy ESXi-gazdagépen, és a egy vCenter-kiszolgáló javasoljuk.
- Egy vagy több VMware virtuális gépek replikálásához.

[További](vmware-azure-architecture.md) VMware-ből az Azure-architektúra kapcsolatban.

A helyszíni konfigurációs kiszolgálót a következőképpen telepíthető:

- A konfigurációs kiszolgáló VMware virtuális gépként üzembe helyezése használatát javasoljuk az OVA sablon előre telepítve van a konfigurációs kiszolgálóval.
- Ha bármilyen okból nem egy sablont használ, manuálisan is beállítása a konfigurációs kiszolgálón. [További információk](physical-azure-disaster-recovery.md#set-up-the-source-environment).



### <a name="where-do-on-premises-vms-replicate-to"></a>Ha ehhez a helyszíni virtuális gépek replikálása?
Az Azure storage replikálja az adatokat. Feladatátvétel futtatásakor a Site Recovery automatikusan létrehozza az Azure virtuális gépek a tárfiókból.

## <a name="replication"></a>Replikáció

### <a name="what-apps-can-i-replicate"></a>Mely alkalmazások replikálhatok?
Bármilyen alkalmazást vagy a VMware virtuális gép, amely megfelel az számítási feladatot replikálhat [replikációs követelményeit](vmware-physical-azure-support-matrix.md##replicated-machines). A Site Recovery támogatja az alkalmazásbarát replikációt, hogy az alkalmazások feladatátvételt, és újra működőképes állapotban nem sikerült. A Site Recovery integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory, és szorosan együttműködik az olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Replikálás az Azure site-to-site VPN-nel is?
A Site Recovery replikálja az adatokat a helyszínről az Azure storage egy nyilvános végpontot, vagy használja az ExpressRoute nyilvános társviszony-létesítés. Site-to-site VPN hálózaton keresztül a replikáció nem támogatott.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Replikálás az Azure ExpressRoute használatával is?
Igen, az ExpressRoute segítségével virtuális gépek replikálása az Azure-bA. A Site Recovery replikálja az adatokat egy Azure Storage-fiók egy nyilvános végpontot keresztül, és be kell állítania [nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) a Site Recovery replikációjára. Miután a virtuális gépek átadja a feladatokat az Azure virtuális hálózat, elérheti azokat használó [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem tudja replikálni VPN-kapcsolaton keresztül?

Az Azure-bA replikálja, amikor replikációs forgalom eléri a nyilvános végpontokat az Azure Storage-fiók, így csak replikálhatja az expressroute-tal (nyilvános társviszony-létesítés) a nyilvános interneten keresztül, és VPN nem működik.


### <a name="what-are-the-replicated-vm-requirements"></a>Mik azok a replikált virtuális gépek követelményeinek?

A replikáció VMware virtuális gép futnia kell egy támogatott operációs rendszert. Emellett a virtuális gép meg kell felelnie az Azure-beli virtuális gépek követelményeinek. [További](vmware-physical-azure-support-matrix.md##replicated-machines) a támogatási mátrixa.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatja az Azure-bA?
Replikáció Azure-bA replikált VMware virtuális gépek esetén a folyamatos.

### <a name="can-i-extend-replication"></a>Ki lehet terjeszteni a replikációt?
A kiterjesztett vagy láncolt replikáció nem támogatott. Ennek a funkciónak a kérelem [Visszajelzési fórum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-initial-replication"></a>Használhatom az offline kezdeti replikációt?
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

[További](vmware-azure-architecture.md) a konfigurációs kiszolgáló összetevőit és folyamatokról.

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

### <a name="how-do-i-update-the-configuration-server"></a>Hogyan frissíthetem a konfigurációs kiszolgálót?
[Ismerje meg](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) a konfigurációs kiszolgáló frissítése. A legújabb frissítés információkat a a [Azure frissítéseit tartalmazó lapon](https://azure.microsoft.com/updates/?product=site-recovery). Emellett közvetlenül is letöltheti a legújabb verzióra a konfigurációs kiszolgáló [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="should-i-backup-the-deployed-configuration-server"></a>Kell-e biztonsági a központilag telepített konfigurációs kiszolgálót?
Azt javasoljuk, hogy a konfigurációs kiszolgáló rendszeres ütemezett biztonsági másolatok készítése. A sikeres feladat-visszavételhez a virtuális gép folyamatban van a feladatátvételben szerepelniük kell a konfigurációs kiszolgáló adatbázisát, és a konfigurációs kiszolgálón fut, és a egy csatlakoztatott állapotban kell lennie. További információ a konfigurációs kiszolgáló gyakori felügyeleti feladatok [Itt](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Beállítom a konfigurációs kiszolgáló, amikor is tölthető le és telepítse manuálisan a MySQL?
Igen. Töltse le a MySQL, és elhelyezheti a **C:\Temp\ASRSetup** mappát. Ezután telepítse manuálisan. Állítsa be a konfigurációs kiszolgáló virtuális Géphez, és fogadja el a feltételeket, amikor megjelennek-e a MySQL **már telepítve van** a **töltse le és telepítse**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kerülje a MySQL letöltése azonban telepíteni a Site Recovery segítségével?
Igen. Töltse le a MySQL-telepítőt, és elhelyezheti a **C:\Temp\ASRSetup** mappát.  Ha beállította a konfigurációs kiszolgáló virtuális gép, fogadja el a feltételeket, majd kattintson a **töltse le és telepítse**, a portálon a telepítő a MySQL telepítése hozzáadott fogja használni.
 
### <a name="canl-i-use-the-configuration-server-vm-for-anything-else"></a>A konfigurációs kiszolgáló virtuális Géphez használni semmi másra CanL?
Nem, csak használja a virtuális gép a konfigurációs kiszolgáló. 

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>Módosíthatom a tárolót a konfigurációs kiszolgáló regisztrálva?
Nem. Egy tárolót a konfigurációs kiszolgáló regisztrálása után nem módosítható.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Használható a ugyanazon konfigurációs kiszolgáló VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítási
Igen, de vegye figyelembe, hogy a fizikai gép is lehet csak akkor nem vissza a VMware virtuális gép.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Honnan tölthetem le a hozzáférési kódot a konfigurációs kiszolgáló?
[Ebből a cikkből](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) további információ a hozzáférési kód letöltése.

### <a name="where-can-i-download-vault-registration-keys"></a>Honnan tölthetem le tároló regisztrációs kulcsokat?

Az a **Recovery Services-tároló**, **kezelése** > **Site Recovery-infrastruktúra** > **konfigurációskiszolgálók**. A **kiszolgálók**válassza **regisztrációs kulcs letöltése** töltheti le a tároló hitelesítőadat-fájlja.



## <a name="mobility-service"></a>Mobilitási szolgáltatás

### <a name="where-can-i-find-the-mobility-service-installers"></a>Hol található a mobilitási szolgáltatás telepítőcsomagokat?
A telepítőcsomagokat tartják a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** mappát a konfigurációs kiszolgálón.

## <a name="how-do-i-install-the-mobility-service"></a>Hogyan kell telepíteni a mobilitási szolgáltatás?
Telepíti az egyes virtuális Gépeken szeretne replikálni, használatával egy [leküldéses telepítése](vmware-azure-install-mobility-service.md), vagy [manuális telepítés](vmware-physical-mobility-service-install-manual.md) a felhasználói felület vagy Powershell. Azt is megteheti, telepítheti, mint például egy üzembe helyezési eszközzel [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).



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
