---
title: Gyakori kérdések – VMware-ből az Azure Site recoveryvel Azure-beli vészhelyreállításához |} A Microsoft Docs
description: Ez a cikk gyakori kérdésekre részéhez vész-helyreállítási a helyszíni VMware virtuális gépek az Azure-ban az Azure Site Recovery foglalja össze.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 04/08/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 2ab29c6e41204104320f4c2f583a24e53786bf3c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360539"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Gyakori kérdések – VMware-ből az Azure-bA

Ez a cikk gyakori kérdésekre adott válaszok a helyszíni VMware virtuális gépek vészhelyreállítása az Azure-bA telepítésekor. 

## <a name="general"></a>Általános kérdések 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Hogyan készüljek VMware virtuális gép vész-helyreállítási?

[Ismerje meg](vmware-azure-architecture.md) a VMware virtuális gépek vészhelyreállítása részt vevő összetevők. 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Site Recovery használata VMware virtuális gépek áttelepítése az Azure-bA?

Igen, a Site Recovery segítségével teljes vészhelyreállítás beállítása VMware virtuális gépek mellett is használhatja a Site Recovery a helyszíni VMware virtuális gépek áttelepítése az Azure-bA. Ebben a forgatókönyvben a helyszíni VMware virtuális gépeket az Azure storage-replikáció. Ezt követően átadja a feladatokat a helyszínről az Azure-bA. A feladatátvételt követően az alkalmazások és számítási feladatok rendelkezésre állnak és futó Azure virtuális gépeken. A folyamat hasonlít a teljes vészhelyreállítás beállítása, azzal a különbséggel, hogy az áttelepítés nem utasíthat el az Azure-ból.


### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Nem a saját Azure-fiók létrehozása a virtuális gépek van szüksége?
Ha Ön olyan előfizetés rendszergazdája, akkor a replikációs szükséges engedélyekkel. Ha még nem, egy Azure virtuális gép létrehozása az erőforráscsoportot és a virtuális hálózatot, adja meg a Site Recovery és a kiválasztott tárfiók írási engedélyek konfigurálására vagy felügyelt lemez a konfiguráció alapján az engedélyek szükségesek. [További információk](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="what-applications-can-i-replicate"></a>Milyen alkalmazásokat replikálhatok?
Bármilyen alkalmazást vagy a VMware virtuális gép, amely megfelel az számítási feladatot replikálhat [replikációs követelményeit](vmware-physical-azure-support-matrix.md##replicated-machines).
- A Site Recovery támogatja az alkalmazásbarát replikációt, hogy az alkalmazások feladatátvételt, és újra működőképes állapotban nem sikerült.
- A Site Recovery integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory, és szorosan együttműködik az olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat.
- [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Használható az Azure-ban egy vendég operációs rendszer kiszolgálói licenc?
Igen, a Microsoft frissítési garanciával rendelkező ügyfelek használhatják a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) menteni a licencelési költségei **Windows Serveres gépek** , amely az Azure-ba, vagy használhatja az Azure-vész-helyreállítási lesznek áttelepítve.

## <a name="security"></a>Biztonság

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Milyen hozzáférést kell a Site Recovery VMware-kiszolgálók?
A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A Site Recovery konfigurációs kiszolgálónak futtató VMware virtuális gép beállítása
- Replikáció a virtuális gépek felderítéséhez. 


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Milyen hozzáférést kell a Site Recovery VMware virtuális gépeket?

- Annak érdekében, hogy a replikációt, egy VMware virtuális gép a Site Recovery mobilitási szolgáltatást telepítenie és futtatnia kell rendelkeznie. Az eszköz telepítheti manuálisan, vagy adja meg, hogy a Site Recovery kell tennie a leküldéses telepítését a szolgáltatást, amikor engedélyezi egy virtuális gép replikációját. 
- A replikáció során virtuális gépek kommunikálnak a Site Recovery a következő:
    - Virtuális gépek kommunikálnak a konfigurációs kiszolgáló 443-as porton HTTPS kezelése érdekében a replikálást.
    - Virtuális gépek küldhetnek replikációs adatokat a folyamatkiszolgálónak, a HTTPS 9443-as porton (módosítható).
    - Ha engedélyezi a virtuális gépre kiterjedő konzisztencia, virtuális gépek kommunikálnak egymással 20004-es porton.


### <a name="is-replication-data-sent-to-site-recovery"></a>Site Recovery számára küldött adatokat?
Nem, a Site Recovery nem intercept a replikált adatokat, és nem rendelkezik semmilyen információval, hogy a virtuális gépeken futó. Replikációs adatcsere VMware-hipervizorok és az Azure storage között. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

Site Recovery szolgáltatás ISO 27001:2013, 27018, a HIPAA, DPA hitelesített, és SOC2 és FedRAMP JAB folyamatban van.


## <a name="pricing"></a>Díjszabás
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hogyan kiszámíthatja a VMware-vészhelyreállításhoz hozzávetőleges kell fizetni?

Használhatja a [díjkalkulátor](https://aka.ms/asr_pricing_calculator) költségeket megbecsülheti a Site Recovery használata közben.

Részletes becsült költségek, futtassa a deployment planner eszköz a [VMware](https://aka.ms/siterecovery_deployment_planner), és használja a [költségbecslési jelentés költség](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Van bármilyen költségek replikálása tárolóba vagy közvetlenül a felügyelt lemezek közötti különbség?

A felügyelt lemezek kismértékben eltér a storage-fiókok számítjuk fel. [További](https://azure.microsoft.com/pricing/details/managed-disks/) kapcsolatos felügyelt lemezek díjszabása.

## <a name="mobility-service"></a>Mobilitási szolgáltatás

### <a name="where-can-i-find-the-mobility-service-installers"></a>Hol található a mobilitási szolgáltatás telepítőcsomagokat?
A rendszer a telepítőcsomagokat a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** mappát a konfigurációs kiszolgálón.

## <a name="how-do-i-install-the-mobility-service"></a>Hogyan kell telepíteni a mobilitási szolgáltatás?
Minden virtuális Géphez szeretne replikálni, többféle használatával telepíthető:
- [Ügyfélleküldéses telepítés](vmware-physical-mobility-service-overview.md#push-installation)
- [Manuális telepítés](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) a felhasználói felület vagy Powershell.
- Üzembe helyezési eszköz segítségével például a központi telepítési [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).

## <a name="managed-disks"></a>Felügyelt lemezek

### <a name="where-does-site-recovery-replicate-data-to"></a>Ha a Site Recovery az adatok replikálása?

A Site Recovery replikálja a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-ban felügyelt lemezekre.
- A Site Recovery folyamatkiszolgáló replikációs naplók ír a gyorsítótárfiók a célrégióban.
- Ezek a naplók segítségével hozza létre a helyreállítási pontokat a felügyelt lemezeken.
- Feladatátvétel esetén a helyreállítási pontot választja szolgál a cél felügyelt lemez létrehozása.
- Virtuális gépek, amelyek korábban lettek replikálva egy storage-fiókba (előtt 2019. március) a szabályzat nem vonatkozik.


### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Új gépek replikálhatok storage-fiókká?

Nem, március 2019, kezdve a portálon is csak Azure-bA replikált felügyelt lemezek. 

Storage-fiókba új virtuális gépek replikálását csak akkor használható a PowerShell vagy a REST API (2018-01-10-es vagy 2016-08-10-es verzió) használatával.

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Milyen előnyökkel a felügyelt lemezekkel való replikálása?

[Ismerje meg, hogyan](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery egyszerűsíti a managed disks szolgáltatással vészhelyreállítás.


### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>Módosíthatom a felügyelt lemez típusa, után a gép védelméhez?

Igen, egyszerűen [felügyelt lemez típusának módosítása](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). Azonban ha a felügyelt lemez típusa, várjon, amíg új helyreállítási pontot kell létrehozni, ha a feladatátvételi teszt vagy feladatátvétel a váltás után kell.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Válthatok-e a felügyelt lemezekre történő replikálás nem felügyelt lemezek?

Nem, a nem felügyelt való váltás felügyelt nem támogatott.

## <a name="replication"></a>Replikáció


### <a name="what-are-the-replicated-vm-requirements"></a>Mik azok a replikált virtuális gépek követelményeinek?

[További](vmware-physical-azure-support-matrix.md##replicated-machines) a VMware virtuális gépek/fizikai kiszolgáló követelmények és támogatást.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatja az Azure-bA?
Replikáció Azure-bA replikált VMware virtuális gépek esetén a folyamatos.


### <a name="can-i-extend-replication"></a>Ki lehet terjeszteni a replikációt?
A kiterjesztett vagy láncolt replikáció nem támogatott. Ennek a funkciónak a kérelem [Visszajelzési fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Használhatom az offline kezdeti replikációt?
Ez a funkció nem támogatott. Ennek a funkciónak a kérelem a [Visszajelzési fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks-from-replication"></a>Kizárhatok lemezeket a replikációból?
Igen, lemezeket zárhat ki.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Virtuális gépek replikálhatok dinamikus lemezeken?
A dinamikus lemezek lehet replikálni. Az operációsrendszer-lemez alaplemeznek kell lennie.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>A virtuális gépre kiterjedő konzisztencia replikációs csoportok használata esetén is hozzáadhatok egy új virtuális gép egy meglévő replikációs csoporthoz?
Igen, adhat hozzá új virtuális gépek egy meglévő replikációs csoporthoz. Ha engedélyezi azok replikációját.
- Virtuális gép nem hozzáadása egy meglévő replikációs csoport replikációs kezdeményezése után.
- Meglévő virtuális gépek egy replikációs csoport nem hozható létre.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Módosíthatja a hozzáadásával vagy a lemezek átméretezése replikáló virtuális gépek?

A VMware – Azure replikálás módosíthatja a lemez méretét. Ha azt szeretné, adjon hozzá új lemezeket kell a lemezt adja hozzá, és engedélyezheti a virtuális gép védelmét.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Át tudom telepíteni a helyszíni gépeket az új vCenter-kiszolgáló folyamatban lévő replikáció befolyásolása nélkül?
Nem, Vcenter vagy a migrálás folyamatban lévő replikáció hatással van. Állítsa be a Site Recovery az új vCenter-kiszolgálóhoz, és újra gépek replikációjának engedélyezéséhez kell.

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Replikálhatok egy gyorsítótárban vagy a céloldali tárfiókot, amely egy virtuális hálózathoz (az Azure storage-tűzfalak) konfigurálva van?
Nem, a Site Recovery nem támogatja a Storage-replikáció a virtuális hálózaton.





## <a name="component-upgrade"></a>Összetevő frissítése

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Saját a mobilitási szolgáltatások ügynök vagy a konfigurációs kiszolgáló verziója nem a régi és a frissítés nem sikerült. Mit tegyek?  

A Site Recovery a N-4 támogatási modelljét követi. [További](https://aka.ms/asr_support_statement) nagyon korábbi verziókról frissítésével kapcsolatos.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Hol található a kibocsátási megjegyzések és kumulatív az Azure Site Recovery?

[Ismerje meg,](site-recovery-whats-new.md) új frissítésekről, és [összesített adatok lekérése](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Hol található az Azure-bA vész-helyreállítási frissítési információk?

[Ismerje meg](https://aka.ms/asr_vmware_upgrades) frissítése.

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Kell minden egyes frissítés forrásgépek újraindítás?

Ajánlott, de már nem kötelező minden frissítést. [További információk](https://aka.ms/asr_vmware_upgrades).


## <a name="configuration-server"></a>Konfigurációs kiszolgáló

### <a name="what-does-the-configuration-server-do"></a>Hogyan működik a konfigurációs kiszolgálót?

A konfigurációs kiszolgáló futtatja a helyszíni Site Recovery-összetevők, beleértve:
- A konfigurációs kiszolgáló magát, hogy koordinálja a helyszíni és Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak. Emellett a folyamatkiszolgáló nem egy virtuális gépeken futó mobilitási szolgáltatás leküldéses telepítését, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A fő célkiszolgálón, amely az Azure-ból a feladat-visszavétel során kezeli a replikációs adatokat.

[További](vmware-azure-architecture.md) a konfigurációs kiszolgáló összetevőit és folyamatokról.

### <a name="where-do-i-set-up-the-configuration-server"></a>Ha a konfigurációs kiszolgáló beállítása?
A konfigurációs kiszolgálón egy egyetlen magas rendelkezésre állású helyszíni VMware virtuális gép van szükség. Fizikai kiszolgáló vész-helyreállítási a konfigurációs kiszolgáló fizikai gépen telepítheti.

### <a name="what-do-i-need-for-the-configuration-server"></a>Mi szükséges a konfigurációs kiszolgáló?

Tekintse át a [Előfeltételek](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kézzel állítható be a konfigurációs kiszolgálót, egy sablon használata helyett?
Azt javasoljuk, hogy Ön [a konfigurációs kiszolgáló virtuális gép létrehozása](vmware-azure-deploy-configuration-server.md) OVF-sablon a legújabb verzióra. Ha valamilyen okból nem lehet, például nem rendelkezik a VMware-kiszolgálóhoz való hozzáférést, akkor [letöltése](physical-azure-set-up-source.md) a fájl a portálon, és állítsa be a konfigurációs kiszolgáló.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Konfigurációs kiszolgáló replikálhatja egynél több régióban?
Nem. Ehhez szüksége van a konfigurációs kiszolgáló, az egyes régiókban.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Üzemeltethet a konfigurációs kiszolgáló az Azure-ban?
Bár lehetséges a konfigurációs kiszolgálón futó Azure virtuális gép kell a helyszíni VMware-infrastruktúra és a virtuális gépek kommunikálnak. Ez hozzáadja a késés, és hatással van a folyamatban lévő replikáció.

### <a name="how-do-i-update-the-configuration-server"></a>Hogyan frissíthetem a konfigurációs kiszolgálót?

[Ismerje meg,](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) konfigurációs kiszolgáló frissítése.
- A a legújabb frissítésekkel kapcsolatos információkat talál a [Azure frissítéseit tartalmazó lapon](https://azure.microsoft.com/updates/?product=site-recovery).
- Letöltheti a legújabb verzióra a portálról. Másik megoldásként közvetlenül letöltheti a konfigurációs kiszolgáló és a legújabb verzióját a [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Ha a verzió négynél több verzió régebbi, mint a jelenlegi verziót, tekintse meg a [támogatási nyilatkozattal](https://aka.ms/asr_support_statement) frissítési útmutató.

### <a name="should-i-back-up-the-configuration-server"></a>Érdemes biztonsági másolatot a konfigurációs kiszolgálót?
Azt javasoljuk, hogy a konfigurációs kiszolgáló rendszeres ütemezett biztonsági másolatok készítése.
- A sikeres feladat-visszavételhez a virtuális gép folyamatban van a feladatátvételben léteznie kell a konfigurációs kiszolgáló adatbázisát.
- A konfigurációs kiszolgálón fut, és csatlakoztatott állapotban kell lennie.
- [További](vmware-azure-manage-configuration-server.md) kapcsolatos általános konfigurációs kiszolgáló felügyeleti feladatokat.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Beállítom a konfigurációs kiszolgáló, amikor is tölthető le és telepítse manuálisan a MySQL?

Igen. Töltse le a MySQL, és elhelyezheti a **C:\Temp\ASRSetup** mappát. Ezután telepítse manuálisan. Állítsa be a konfigurációs kiszolgáló virtuális Géphez, és fogadja el a feltételeket, amikor megjelennek-e a MySQL **már telepítve van** a **töltse le és telepítse**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kerülje a MySQL letöltése azonban telepíteni a Site Recovery segítségével?

Igen. Töltse le a MySQL-telepítőt, és elhelyezheti a **C:\Temp\ASRSetup** mappát.  Ha beállította a konfigurációs kiszolgáló virtuális gép, fogadja el a feltételeket, majd kattintson a **töltse le és telepítse**. A portálon a telepítő a MySQL telepítése hozzáadott fogja használni.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Használhatom-e a konfigurációs kiszolgáló virtuális gép semmi másra?
Nem, csak használja a virtuális gép a konfigurációs kiszolgáló. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Klónozza a konfigurációs kiszolgáló és vezénylési használni?
Nem, akkor kell beállítása friss konfigurációs kiszolgáló regisztrációjával kapcsolatos problémák elkerülése érdekében.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Módosíthatom a tárolót, amelyben a konfigurációs kiszolgáló regisztrálva van?
Nem. Miután egy tárolót a konfigurációs kiszolgáló társítva, nem lehet módosítani. Felülvizsgálat [Ez a cikk](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) újraregisztrálásával megismeréséhez.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Használható a ugyanazon konfigurációs kiszolgáló VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítási
Igen, de vegye figyelembe, hogy a fizikai gép csak akkor nem vissza a VMware virtuális gép.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Honnan tölthetem le a hozzáférési kódot a konfigurációs kiszolgáló?
[Ismerje meg, hogyan](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) töltse le a hozzáférési kódot.

### <a name="where-can-i-download-vault-registration-keys"></a>Honnan tölthetem le tároló regisztrációs kulcsokat?

A Recovery Services-tárolóban kattintson **konfigurációs kiszolgálók** a **Site Recovery-infrastruktúra** > **kezelés**. Ezt a **kiszolgálók**válassza **regisztrációs kulcs letöltése** töltheti le a tároló hitelesítőadat-fájlja.







## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Használható a folyamatkiszolgálót a helyszínen, a feladat-visszavételhez?
Erősen ajánlott a folyamatkiszolgáló létrehozása az Azure-ban feladat-visszavétel célú adatok átvitel késések elkerülése érdekében. Emellett abban az esetben, ha a forrás virtuális gépek hálózati az Azure internetkapcsolattal rendelkező hálózathoz a konfigurációs kiszolgáló összetevő elkülönül, elengedhetetlen fontosságú használni létrehozott Azure-ban feladat-visszavételi folyamatkiszolgáló.

### <a name="can-i-retain-the-ip-address-on-failover"></a>A feladatátvételi IP-cím is megőrizheti?
Igen, őrizheti meg a feladatátvételi IP-címe. Győződjön meg arról, hogy a feladatátvétel előtt a virtuális gép "Számítás és hálózat" beállításaiban adjon meg célként megadott IP-cím. Ezenkívül gépek leállítása a feladatátvétel, feladat-visszavétel során IP-címütközés elkerülése érdekében idején.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Módosíthatom a cél virtuális gép méretét vagy a virtuális gép típusát a feladatátvétel előtt?
Igen, módosíthatja a típust vagy a bármikor a portálon a replikált virtuális gép számítási és hálózati beállításainál a feladatátvétel előtt a virtuális gép mérete.

### <a name="how-far-back-can-i-recover"></a>Milyen biztonsági állíthatja helyre?
VMware-ről az Azure-bA a legrégebbi helyreállítási pont használható érték 72 óra.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan érhetem el az Azure virtuális gépek a feladatátvételt követően
A feladatátvételt követően elérheti az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, egy helyek közötti VPN-kapcsolaton keresztül, vagy Azure expressroute-on keresztül. Készítse elő a számos dolgot, hogy csatlakozni kell. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Nem sikerült rugalmas adatokon?

Az Azure-t hibatűrőnek terveztük. A Site Recovery a feladatátvétel egy másodlagos Azure adatközpontba, az Azure SLA kategóriában. Feladatátvétel esetén biztosítjuk, hogy a metaadatokat, és a tárolók maradjanak ugyanabban a földrajzi régióban, a tároló számára is választott.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
[Feladatátvétel](site-recovery-failover.md) nem automatikus. A portálon egyetlen kattintással feladatátvételt, vagy használhat [PowerShell](/powershell/module/az.recoveryservices) feladatátvétel indításához.

### <a name="can-i-fail-back-to-a-different-location"></a>E visszaadhatja egy másik helyre?
Igen, ha az Azure-bA feladatátvétel, visszaadhatja a másik helyet, ha az eredeti kapcsolatot nem érhető el. [További információk](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Miért kell egy VPN- vagy ExpressRoute visszavételhez?
Ha nem sikerül az Azure-ból, Azure származó adatokat másolja vissza a helyszíni virtuális gép, és privát hozzáférésre szükség.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Átméretezhetek az Azure virtuális Géphez feladatátvétel után?
Nem, nem módosíthatja a méretét vagy a cél virtuális gép típusát a feladatátvétel után.


## <a name="automation-and-scripting"></a>Automatizálás és parancsfájl-kezelési

### <a name="can-i-set-up-replication-with-scripting"></a>Állítható be a parancsfájl-replikáció?
Igen. A Rest API, PowerShell vagy az Azure SDK használatával a Site Recovery munkafolyamatainak automatizálható. [További](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Teljesítmény és kapacitás
### <a name="can-i-throttle-replication-bandwidth"></a>Szabályozhatja a sávszélesség?
Igen. [További információk](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>További lépések
* [Felülvizsgálat](vmware-physical-azure-support-matrix.md) követelmények támogatására.
* [Állítsa be a](vmware-azure-tutorial.md) VMware-ből az Azure-bA.
