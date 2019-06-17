---
title: Az Azure Site Recoveryvel Azure-beli vészhelyreállításához VMware kapcsolatos gyakori kérdésekre |} A Microsoft Docs
description: Válaszok az Azure-bA a helyszíni VMware virtuális gépek vész-helyreállítási kapcsolatos gyakori kérdéseket az Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 05/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 59be8e0585f0bedcafc868ee42f5113509c9c4ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417780"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Az Azure-bA VMware kapcsolatos gyakori kérdésekre

Ez a cikk gyakori kérdésekre, előfordulhat, hogy merülnek fel, amikor üzembe helyezi a vész-helyreállítási a helyszíni VMware virtuális gépek (VM) az Azure ad választ.

## <a name="general"></a>Általános kérdések

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Hogyan készüljek VMware virtuális gép vész-helyreállítási?

[Ismerje meg a következő összetevők kapnak szerepet](vmware-azure-architecture.md) a VMware virtuális gépek vészhelyreállítását.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Site Recovery használata VMware virtuális gépek áttelepítése az Azure-bA?

Igen. A Site Recovery segítségével teljes vészhelyreállítás beállítása VMware virtuális gépek mellett is használhatja a Site Recovery a helyszíni VMware virtuális gépek áttelepítése az Azure-bA. Ebben a forgatókönyvben a helyszíni VMware virtuális gépeket az Azure Storage-replikáció. Ezt követően átadja a feladatokat a helyszínről az Azure-bA. A feladatátvételt követően az alkalmazások és számítási feladatok rendelkezésre állnak és futó Azure virtuális gépeken. A folyamat létrehozásához hasonlít teljes vészhelyreállítás, azzal a különbséggel, hogy az áttelepítés nem utasíthat el az Azure-ból.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Nem a saját Azure-fiók létrehozása a virtuális gépek van szüksége?

Ha Ön olyan előfizetés rendszergazdája, akkor a replikációs szükséges engedélyekkel. Ha Ön nem rendszergazda, ezek a műveletek engedélyek szükségesek:

- Egy Azure virtuális gép létrehozása az erőforrás a csoport- és virtuális hálózat, a Site Recovery konfigurálása során meghatározott.
- Írás a kiválasztott tárfiók vagy a felügyelt lemez, a konfiguráció alapján.

[További](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) kapcsolatos szükséges engedélyekkel.

### <a name="what-applications-can-i-replicate"></a>Milyen alkalmazásokat replikálhatok?

Bármilyen alkalmazást vagy a VMware virtuális gép, amely megfelel a számítási feladatot replikálhat a [replikációs követelményeit](vmware-physical-azure-support-matrix.md#replicated-machines).

- A Site Recovery alkalmazásbarát replikációt is támogatja, így a alkalmazások átadta a feladatait, és újra működőképes állapotban nem sikerült.
- A Site Recovery integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory. Azt is szorosan együttműködik olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat.

[További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Használható az Azure-ban egy vendég operációs rendszer kiszolgálói licenc?

Igen, a Microsoft frissítési garanciával rendelkező ügyfelek használhatják a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) menteni a licencelési költségeit az Azure-bA migrálta a Windows Server számítógépek esetében, vagy használhatja az Azure-vész-helyreállítási.

## <a name="security"></a>Biztonság

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Milyen VMware-kiszolgálókhoz való hozzáférés nem kell a Site Recovery?

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Állítsa be a VMware virtuális gépek futtatása a Site Recovery konfigurációs kiszolgálónak.
- Replikáció a virtuális gépek felderítéséhez.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Milyen VMware virtuális gépekhez való hozzáférés nem kell a Site Recovery?

- VMware virtuális gép replikálása, a Site Recovery mobilitási szolgáltatást telepítenie és futtatnia kell rendelkeznie. Manuálisan telepítheti az eszközt, vagy megadhatja, hogy a Site Recovery leküldéses telepítését a szolgáltatást, akkor hajtsa végre, ha engedélyezi a replikációt a virtuális gép számára.
- A replikáció során virtuális gépek kommunikálnak a Site Recovery a következő:
    - Virtuális gépek kommunikálnak a konfigurációs kiszolgáló 443-as HTTPS-port kezelése érdekében a replikálást a.
    - Virtuális gépek a 9443-as HTTPS-port a folyamatkiszolgálón küldhetnek replikációs adatokat. (Ez a beállítás módosítható.)
    - Ha engedélyezi a virtuális gépre kiterjedő konzisztencia, virtuális gépek kommunikálnak egymással 20004-es porton.

### <a name="is-replication-data-sent-to-site-recovery"></a>Site Recovery számára küldött adatokat?

Nem, a Site Recovery nem intercept a replikált adatokat, és nem rendelkezik semmilyen információval, hogy a virtuális gépeken futó. Replikációs adatcsere VMware-hipervizorok és az Azure Storage között. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

A Site Recovery ISO 27001:2013 és 27018, és a HIPAA, DPA minősítéssel. SOC2 és FedRAMP JAB-folyamatban van.

## <a name="pricing"></a>Díjszabás

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hogyan kiszámítja a VMware-vészhelyreállításhoz hozzávetőleges kell fizetni?

Használja a [díjkalkulátor](https://aka.ms/asr_pricing_calculator) költségeket megbecsülheti a Site Recovery használata közben.

A részletes becsült költségek, futtassa a deployment planner eszköz a [VMware](https://aka.ms/siterecovery_deployment_planner) , és a [költségbecslési jelentés költség](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Van bármilyen költségek replikálása tárolóba vagy közvetlenül a felügyelt lemezek közötti különbség?

A felügyelt lemezek tárfiókokban kissé különbözően díjkötelesek. [További](https://azure.microsoft.com/pricing/details/managed-disks/) kapcsolatos felügyelt lemezek díjszabása.

## <a name="mobility-service"></a>Mobilitási szolgáltatás

### <a name="where-can-i-find-the-mobility-service-installers"></a>Hol található a mobilitási szolgáltatás telepítőcsomagokat?

A telepítők a konfigurációs kiszolgálón %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappában találhatók.

## <a name="how-do-i-install-the-mobility-service"></a>Hogyan kell telepíteni a mobilitási szolgáltatás?

A replikálni kívánt virtuális gépek telepítése a szolgáltatás többféle módszer egyikét:

- [Ügyfélleküldéses telepítés](vmware-physical-mobility-service-overview.md#push-installation)
- [Manuális telepítés](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) a felhasználói felület vagy PowerShell
- Üzembe helyezés, például egy olyan üzembe helyezési eszközzel [System Center Configuration Managerben](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Felügyelt lemezek

### <a name="where-does-site-recovery-replicate-data-to"></a>Ha a Site Recovery az adatok replikálása?

A Site Recovery replikálja a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-ban felügyelt lemezekre.

- A Site Recovery folyamatkiszolgáló replikációs naplók ír a gyorsítótárfiók a célrégióban.
- Ezek a naplók az Azure managed disks-előtaggal rendelkező helyreállítási pontok létrehozásához használt **asrseeddisk**.
- Feladatátvétel esetén a helyreállítási pontot választja segítségével hozzon létre egy új cél felügyelt lemezt. A felügyelt lemez csatolva van a virtuális gép az Azure-ban.
- Virtuális gépek, korábban is replikálva a tárfiók (előtt 2019. március) a szabályzat nem vonatkozik.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Új gépek replikálhatok storage-fiókká?

Nem. Március 2019, kezdve az Azure Portalon is csak Azure-bA replikált felügyelt lemezek.

Storage-fiókba új virtuális gépek replikálását csak a PowerShell vagy a REST API (2018-01-10-es vagy 2016-08-10-es verzió) használatával érhető el.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Milyen előnyökkel jár a managed Disks szolgáltatásba replikálása?

[Ismerje meg, hogyan](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery egyszerűsíti a managed disks szolgáltatással vészhelyreállítás.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Módosíthatom a felügyelt lemez típusa, a gép védelme után?

Igen, egyszerűen [felügyelt lemez típusának módosítása](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) folyamatban lévő replikáció esetében. Típusának módosítása előtt győződjön meg arról, hogy nincs közös hozzáférésű jogosultságkód URL-cím a felügyelt lemez jön létre:

1. Nyissa meg a **Managed Disk** erőforrást az Azure Portalon, és ellenőrizze, hogy rendelkezünk egy közös hozzáférésű jogosultságkód URL-cím szalagcím a **áttekintése** panelen.
1. Ha a szalagcím látható, válassza ki, hogy a folyamatos Exportálás megszakítása.
1. Módosítsa a lemez típusát a postafiókjába pár percen belül. Ha módosítja a felügyelt lemez típusa, várjon, amíg az új helyreállítási pontot kell létrehozni az Azure Site Recovery.
1. Az új helyreállítási pontok használata bármilyen feladatátvételi teszt vagy feladatátvétel a jövőben.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Válthatok-e a felügyelt lemezekre történő replikálás nem felügyelt lemezek?

Nem. Váltás a felügyelt, nem felügyelt nem támogatott.

## <a name="replication"></a>Replikáció

### <a name="what-are-the-replicated-vm-requirements"></a>Mik azok a replikált virtuális gépek követelményeinek?

[További](vmware-physical-azure-support-matrix.md#replicated-machines) VMware virtuális gépek és fizikai kiszolgálók támogatása követelményeiről.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatja az Azure-bA?

Replikáció Azure-bA replikált VMware virtuális gépek esetén a folyamatos.

### <a name="can-i-extend-replication"></a>Ki lehet terjeszteni a replikációt?

A kiterjesztett vagy láncolt replikáció nem támogatott. Ennek a funkciónak a kérelem a [Visszajelzési fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Használhatom az offline kezdeti replikációt?

Offline replikáció nem támogatott. Ennek a funkciónak a kérelem a [Visszajelzési fórum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Mit jelent a asrseeddisk?

Adatok replikálódnak a minden forráslemezt, egy felügyelt lemezt az Azure-ban. Ez a lemez rendelkezik előtagja **asrseeddisk**. A forrás lemez és a helyreállítási pontok pillanatképeiből másolatát tárolja.

### <a name="can-i-exclude-disks-from-replication"></a>Kizárhatok lemezeket a replikációból?

Igen, lemezeket zárhat ki.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Replikálhatok dinamikus lemezekkel rendelkező virtuális gépeken?

A dinamikus lemezek lehet replikálni. Az operációsrendszer-lemez alaplemeznek kell lennie.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>A virtuális gépre kiterjedő konzisztencia replikációs csoportok használata esetén is hozzáadhatok egy új virtuális gép egy meglévő replikációs csoporthoz?

Igen, adhat hozzá új virtuális gépek egy meglévő replikációs csoporthoz. Ha engedélyezi azok replikációját. Azonban:

- Virtuális gép nem hozzáadása egy meglévő replikációs csoport replikációs megkezdése után.
- Meglévő virtuális gépek egy replikációs csoport nem hozható létre.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Módosíthatja a hozzáadásával vagy a lemezek átméretezése replikáló virtuális gépek?

A VMware replikálásához az Azure-bA módosíthatja a lemez méretét. Adjon hozzá új lemezeket szeretne, ha kell a lemezt adja hozzá, és engedélyezheti a virtuális gép védelmét.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Át tudom telepíteni a helyszíni gépeket az új vCenter-kiszolgáló folyamatban lévő replikáció befolyásolása nélkül?

Nem. VMware Vcenter vagy a migrálás folyamatban lévő replikáció negatív hatással lesz. Állítsa be a Site Recovery az új vCenter-kiszolgálóhoz, és újra gépek replikációjának engedélyezéséhez.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Replikálhatok gyorsítótár vagy a cél tárfiók, amely rendelkezik egy virtuális hálózatot (az Azure-tűzfalak) konfigurálva?

Nem, az a Site Recovery nem támogatja az Azure Storage-replikáció a virtuális hálózatok.

## <a name="component-upgrade"></a>Összetevő frissítése

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Saját a mobilitási szolgáltatások ügynök vagy a konfigurációs kiszolgáló verziója korábbi, és saját frissítése sikertelen volt. Mit tegyek?

A Site Recovery a N-4 támogatási modelljét követi. [További](https://aka.ms/asr_support_statement) nagyon korábbi verziókról frissítésével kapcsolatos.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Ahol is a kibocsátási megjegyzések és kumulatív frissítések az Azure Site Recovery?

[További információ az új frissítések](site-recovery-whats-new.md), és [összesített adatok lekérése](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Hol található az Azure-bA vész-helyreállítási frissítési információk?

[Ismerje meg a frissítés](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Kell minden egyes frissítés forrásgépek újraindítás?

Újraindítás ajánlott, de nem kötelező, minden egyes frissítés. [További információk](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Konfigurációs kiszolgáló

### <a name="what-does-the-configuration-server-do"></a>Hogyan működik a konfigurációs kiszolgálót?

A konfigurációs kiszolgáló futtatja a helyszíni Site Recovery-összetevők, beleértve:

- Magát a konfigurációs kiszolgálót. A kiszolgáló koordinálja a helyszíni összetevők és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló, amely egy replikációs átjáróként üzemel. Ez a kiszolgáló:
    1. Fogadja a replikált adatokat.
    2. Optimalizálja az adatokat a gyorsítótárazás, tömörítés és titkosítás.
    3. Elküldi az adatokat az Azure Storage.
  A folyamatkiszolgáló is nem egy virtuális gépeken futó mobilitási szolgáltatás leküldéses telepítését, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A fő célkiszolgálón, amely az Azure-ból a feladat-visszavétel során kezeli a replikációs adatokat.

[További](vmware-azure-architecture.md) a konfigurációs kiszolgáló összetevőit és folyamatokról.

### <a name="where-do-i-set-up-the-configuration-server"></a>Ha a konfigurációs kiszolgáló beállítása?

A konfigurációs kiszolgálón egy egyetlen, magas rendelkezésre állású, helyszíni VMware virtuális gép van szükség. Fizikai kiszolgáló vész-helyreállítási telepítse a konfigurációs kiszolgáló fizikai gépen.

### <a name="what-do-i-need-for-the-configuration-server"></a>Mi szükséges a konfigurációs kiszolgáló?

Tekintse át a [Előfeltételek](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kézzel állítható be a konfigurációs kiszolgálót, egy sablon használata helyett?

Azt javasoljuk, hogy Ön [a konfigurációs kiszolgáló virtuális gép létrehozása](vmware-azure-deploy-configuration-server.md) a legújabb verzióra a nyissa meg a virtuális gép formátum (OVF-) sablon használatával. Ha a sablon nem használható (például, ha nem rendelkezik hozzáféréssel a VMware-kiszolgáló), [letöltése](physical-azure-set-up-source.md) a fájl a portálon, és állítsa be a konfigurációs kiszolgáló.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Konfigurációs kiszolgáló replikálhatja egynél több régióban?

Nem. Replikálása több régióban, szüksége van a konfigurációs kiszolgáló, az egyes régiókban.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Üzemeltethet a konfigurációs kiszolgáló az Azure-ban?

Bár lehetséges, a konfigurációs kiszolgálón futó Azure virtuális gép kell a helyszíni VMware-infrastruktúra és a virtuális gépek kommunikálnak. Ez a kommunikáció késleltetéssel, és hatással van a folyamatban lévő replikáció.

### <a name="how-do-i-update-the-configuration-server"></a>Hogyan frissíthetem a konfigurációs kiszolgálót?

[Ismerje meg,](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) a konfigurációs kiszolgáló frissítése.

- A a legújabb frissítésekkel kapcsolatos információkat talál a [Azure frissítéseit tartalmazó lapon](https://azure.microsoft.com/updates/?product=site-recovery).
- Letöltheti a legújabb verzióra a portálról. Másik lehetőségként letöltheti a legújabb verzióját közvetlenül a konfigurációs kiszolgáló a [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Ha a verzió négynél több verzió régebbi, mint a jelenlegi verziót, tekintse meg a [támogatási nyilatkozattal](https://aka.ms/asr_support_statement) frissítési útmutató.

### <a name="should-i-back-up-the-configuration-server"></a>Érdemes biztonsági másolatot a konfigurációs kiszolgálót?

Azt javasoljuk, hogy a konfigurációs kiszolgáló rendszeres ütemezett biztonsági másolatok készítése.

- A sikeres feladat-visszavételhez a virtuális gép folyamatban van a feladatátvételben léteznie kell a konfigurációs kiszolgáló adatbázisát.
- A konfigurációs kiszolgálón fut, és csatlakoztatott állapotban kell lennie.
- [További](vmware-azure-manage-configuration-server.md) kapcsolatos általános konfigurációs kiszolgáló felügyeleti feladatokat.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Beállítom a konfigurációs kiszolgáló, amikor is tölthető le és telepítse manuálisan a MySQL?

Igen. Töltse le a MySQL, és helyezze azokat a C:\Temp\ASRSetup mappájába. Akkor manuálisan kell telepíteni. Állítsa be a konfigurációs kiszolgáló virtuális Géphez, és fogadja el a feltételeket, amikor megjelennek-e a MySQL **már telepítve van** a **töltse le és telepítse**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kerülje a MySQL letöltése azonban telepíteni a Site Recovery segítségével?

Igen. Töltse le a MySQL-telepítőt, és helyezze a C:\Temp\ASRSetup mappában. Ha beállította a konfigurációs kiszolgáló virtuális gép, fogadja el a feltételeket, és válassza ki **töltse le és telepítse**. A portálon korábban hozzáadott a MySQL telepítése a telepítő fogja használni.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Használhatom-e a konfigurációs kiszolgáló virtuális gép semmi másra?

Nem. A virtuális gép csak a konfigurációs kiszolgálót használja.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Klónozza a konfigurációs kiszolgáló és vezénylési használni?

Nem. Állítsa be egy friss konfigurációs kiszolgáló regisztrációjával kapcsolatos problémák elkerülése érdekében.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Módosíthatom a tárolót, amelyben a konfigurációs kiszolgáló regisztrálva van?

Nem. Miután egy tárolót a konfigurációs kiszolgáló társítva, nem lehet módosítani. [Ismerje meg,](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) konfigurációs kiszolgáló regisztrálása másik tárolóval.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Használható a ugyanazon konfigurációs kiszolgáló VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítási?

Igen, de vegye figyelembe, hogy a fizikai gép is rendszer nem csak a VMware virtuális gép vissza.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Honnan tölthetem le a hozzáférési kódot a konfigurációs kiszolgáló?

[Ismerje meg,](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) hogyan töltheti le a hozzáférési kódot.

### <a name="where-can-i-download-vault-registration-keys"></a>Honnan tölthetem le tároló regisztrációs kulcsokat?

Válassza ki a Recovery Services-tárolóban **konfigurációs kiszolgálók** a **Site Recovery-infrastruktúra** > **kezelés**. Ezt követően a **kiszolgálók**válassza **regisztrációs kulcs letöltése** töltheti le a tároló hitelesítőadat-fájlja.

## <a name="process-server"></a>Folyamatkiszolgáló

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Miért nem engedélyezhető a replikáció során, jelölje be a folyamatkiszolgáló?

Frissítések 9,24 verziók és az újabb most megjelenített a [állapotát, amikor engedélyezi a replikációt a folyamatkiszolgáló](vmware-azure-enable-replication.md#enable-replication). Ez a funkció segít a folyamatkiszolgáló szabályozás elkerülése érdekében, és minimalizálja a nem megfelelő állapotú folyamatkiszolgálók használatát.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Hogyan frissíthetem a folyamatkiszolgáló pontos állapotinformációkat 9.24 vagy újabb verzióra?

Kezdve [verzió 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), további riasztások lettek hozzáadva a folyamatkiszolgáló állapotát. [Frissítse a Site Recovery-összetevők 9.24 vagy újabb verzió] (service-updates-how-to.md#links-to-currently-supported-update-rollups), hogy az összes riasztások jönnek létre.

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Használható a feladat-visszavételhez a helyi folyamatkiszolgáló?

Javasoljuk, hogy a folyamatkiszolgáló létrehozása az Azure-ban feladat-visszavétel célú adatok átvitel késések elkerülése érdekében. Emellett abban az esetben, ha a forrás virtuális gépek hálózati az Azure internetkapcsolattal rendelkező hálózathoz a konfigurációs kiszolgáló összetevő elkülönül, elengedhetetlen fontosságú használni létrehozott Azure-ban feladat-visszavételi folyamatkiszolgáló.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Az IP-cím őrizhetem feladatátvételi?

Igen, az IP-cím is megőrizheti a feladatátvételkor. A cél IP-címet adjon meg a **számítás és hálózat** a feladatátvétel előtt a virtuális gép beállításait. Ezenkívül gépek leállítása a feladatátvétel, feladat-visszavétel során IP-címütközés elkerülése érdekében idején.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Módosíthatom a cél virtuális gép méretét vagy a virtuális gép típusát a feladatátvétel előtt?

Igen, módosíthatja a típusát vagy a virtuális gép méretét a feladatátvétel előtt bármikor. A portálon, használja a **számítás és hálózat** a replikált virtuális gép beállításait.

### <a name="how-far-back-can-i-recover"></a>Milyen biztonsági állíthatja helyre?

VMware-ről az Azure-bA a legrégebbi helyreállítási pont használható érték 72 óra.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan érhetem el az Azure virtuális gépek a feladatátvételt követően

A feladatátvételt követően elérheti az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, egy helyek közötti VPN-kapcsolaton keresztül, vagy Azure expressroute-on keresztül. Szeretne csatlakozni, előbb elő kell készítenie számos dolog. [További információk](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Feladatátvételi adatok hibatűréséről?

Az Azure-t hibatűrőnek terveztük. A Site Recovery a feladatátvétel egy másodlagos Azure adatközpontba, az Azure szolgáltatásiszint-szerződés (SLA) által kért kategóriában. Feladatátvétel esetén biztosítjuk, hogy a metaadatokat, és a tárolók is ugyanabban a földrajzi régióban, a tároló számára is választott.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

[Feladatátvétel](site-recovery-failover.md) nem automatikus. A feladatátvétel indítása azáltal, hogy egyetlen kijelölésre a portálon, vagy használhat [PowerShell](/powershell/module/az.recoveryservices) feladatátvétel indításához.

### <a name="can-i-fail-back-to-a-different-location"></a>E visszaadhatja egy másik helyre?

Igen. Ha az Azure-bA feladatátvétel, visszaadhatja a másik helyet, ha az eredeti kapcsolatot nem érhető el. [További információk](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Miért egy VPN- vagy ExpressRoute kell a privát társviszony-létesítés visszavételhez?

Ha nem sikerül az Azure-ból, Azure származó adatokat másolja vissza a helyszíni virtuális Géphez, és privát hozzáférésre szükség.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Átméretezhetek az Azure virtuális Géphez feladatátvétel után?

Nem, nem módosíthatja a méretét vagy a cél virtuális gép típusát a feladatátvétel után.

## <a name="automation-and-scripting"></a>Automatizálás és parancsfájl-kezelési

### <a name="can-i-set-up-replication-with-scripting"></a>Állítható be a parancsfájl-replikáció?

Igen. Site Recovery munkafolyamatainak automatizálható a Rest API, PowerShell vagy az Azure SDK használatával. [További információk](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Teljesítmény és kapacitás

### <a name="can-i-throttle-replication-bandwidth"></a>Szabályozhatja a sávszélesség?

Igen. [További információk](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>További lépések

- [Felülvizsgálat](vmware-physical-azure-support-matrix.md) követelmények támogatására.
- [Állítsa be a](vmware-azure-tutorial.md) VMware-ből az Azure-bA.
