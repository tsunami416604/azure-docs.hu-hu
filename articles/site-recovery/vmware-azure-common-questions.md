---
title: Gyakori kérdések a VMware vész-helyreállítási azure Site Recovery
description: Válaszok at kaphat a helyszíni VMware virtuális gépek Azure-ba történő vész-helyreállítási gyakori kérdéseire az Azure Site Recovery használatával.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: d551cef7037c0b6d7286cbb4b70d8f7a8f7f5cae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259510"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Gyakori kérdések VMware-ből az Azure-ba történő replikációról

Ez a cikk választ ad azokra a gyakori kérdésekre, amelyek a helyszíni VMware virtuális gépek (VM-ek) vész-helyreállítási üzembe helyezésekor merülhetnek fel az Azure-ban.

## <a name="general"></a>Általános kérdések

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Mire van szükségem a VMware VM vész-helyreállítási?

[Ismerje meg a](vmware-azure-architecture.md) VMware virtuális gépek vész-helyreállítási összetevőit.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Használhatom a Site Recovery szolgáltatást a VMware virtuális gépek Azure-ba való áttelepítéséhez?

Igen. Amellett, hogy a Site Recovery használatával teljes vészhelyreállítást állíthat be a VMware virtuális gépekhez, a Site Recovery használatával is áttelepítheti a helyszíni VMware virtuális gépeket az Azure-ba. Ebben a forgatókönyvben replikálja a helyszíni VMware virtuális gépek et az Azure Storage-ba. Ezt követően a helyszíni Azure-ba való átvételt. Feladatátvétel után az alkalmazások és a számítási feladatok elérhetők és futnak az Azure virtuális gépeken. A folyamat olyan, mint a teljes vész-helyreállítás beállítása, azzal a különbséggel, hogy egy áttelepítés nem lehet visszafeladni az Azure-ból.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Az Azure-fiókomnak engedélyekre van szüksége a virtuális gépek létrehozásához?

Ha Ön előfizetés-rendszergazda, rendelkezik a szükséges replikációs engedélyekkel. Ha Ön nem rendszergazda, az alábbi műveletek végrehajtásához engedélyekre van szüksége:

- Hozzon létre egy Azure-beli virtuális gépet az erőforráscsoportban és a virtuális hálózatban, amelyet a Site Recovery konfigurálásakor ad meg.
- Írjon a kijelölt tárfiókba vagy felügyelt lemezre a konfiguráció alapján.

[További információ](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) a szükséges engedélyekről.

### <a name="what-applications-can-i-replicate"></a>Milyen alkalmazásokat replikálhatok?

A VMware virtuális gépeken futó, a [replikációs követelményeknek](vmware-physical-azure-support-matrix.md#replicated-machines)megfelelő bármely alkalmazást vagy számítási feladatot replikálhatja.

- A Site Recovery támogatja az alkalmazásbarát replikációt, így az alkalmazások feladatátvételt és az intelligens állapotba való visszaadást is letehetik.
- A Site Recovery integrálható a Microsoft alkalmazásaival, például a SharePointtal, az Exchange-szel, a Dynamics-szal, az SQL Serverrel és az Active Directoryval. Szorosan együttműködik a vezető gyártókkal, köztük az Oracle-lel, az SAP-val, az IBM-mel és a Red Hat-tal.

[További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Használhatom a vendég operációsrendszer-kiszolgáló licencét az Azure-ban?

Igen, a Microsoft Frissítési Garancia-ügyfelek az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) használatával megtakaríthatják az Azure-ba áttelepített Windows Server-gépek licencelési költségeit, vagy az Azure-t vészhelyreállításhoz használhatják.

## <a name="security"></a>Biztonság

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Milyen hozzáférésre van szüksége a VMware-kiszolgálókhoz a Site Recovery-nek?

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Állítsa be a Site Recovery konfigurációs kiszolgálót futtató VMware virtuális gép.
- A virtuális gépek automatikus felderítése a replikációhoz.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Milyen hozzáférésre van szüksége a VMware virtuális gépekhez a Site Recovery-hez?

- A replikáláshoz a VMware virtuális gépnek telepítve és futnia kell a Site Recovery Mobility szolgáltatást. Az eszközt manuálisan is telepítheti, vagy megadhatja, hogy a Site Recovery leküldéses telepítést végezzeel a szolgáltatásban, ha engedélyezi a virtuális gép replikációját.
- A replikáció során a virtuális gépek az alábbiak szerint kommunikálnak a Site Recovery szolgáltatással:
    - A virtuális gépek a 443-as HTTPS-porton lévő konfigurációs kiszolgálóval kommunikálnak a replikáció kezeléséhez.
    - A virtuális gépek replikációs adatokat küldenek a folyamatkiszolgálónak a 9443-as HTTPS-porton. (Ez a beállítás módosítható.)
    - Ha engedélyezi a több virtuális gép konzisztenciáját, a virtuális gépek a 20004-es porton keresztül kommunikálnak egymással.

### <a name="is-replication-data-sent-to-site-recovery"></a>A replikációs adatok elküldve a Site Recovery szolgáltatásba?

Nem, a Site Recovery nem fogja el a replikált adatokat, és nem rendelkezik semmilyen információt arról, hogy mi fut a virtuális gépeken. A replikációs adatok cseréje a VMware hipervizorok és az Azure Storage között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.

A Site Recovery az ISO 27001:2013 és a 27018, a HIPAA és a DPA minősítéssel rendelkezik. Ez a folyamat SOC2 és FedRAMP JAB értékelések.

## <a name="pricing"></a>Díjszabás

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hogyan számíthatom ki a VMware vész-helyreállítási díjainak hozzávetőleges díjait?

A site recovery használata során a [díjkalkulátor](https://aka.ms/asr_pricing_calculator) segítségével megbecsülhető a költségek.

A költségek részletes becsléséhez futtassa a [VMware](https://aka.ms/siterecovery_deployment_planner) üzembe helyezési tervező eszközét, és használja a [költségbecslési jelentést.](https://aka.ms/asr_DP_costreport)

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Van-e különbség a költségek között replikálás a tárolóba, vagy közvetlenül a felügyelt lemezekre?

A felügyelt lemezek díja némileg eltér a tárfiókoktól. [További információ](https://azure.microsoft.com/pricing/details/managed-disks/) a felügyelt lemezdíjszabásról.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Van-e különbség a költségek, amikor replikálja az általános célú v2 tárfiók?

Általában a GPv2-tárfiókok tranzakciós költségeinek növekedését fogja látni, mivel az Azure Site Recovery a tranzakciók száma nehéz. [További információ](../storage/common/storage-account-upgrade.md#pricing-and-billing) a változás becsléséhez.

## <a name="mobility-service"></a>Mobilitási szolgáltatás

### <a name="where-can-i-find-the-mobility-service-installers"></a>Hol találom a Mobilitás szolgáltatás telepítőit?

A telepítők a konfigurációs kiszolgáló %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappájában találhatók.

## <a name="how-do-i-install-the-mobility-service"></a>Hogyan telepíthetem a Mobilitás szolgáltatást?

Minden replikánskívánt virtuális gépre telepítse a szolgáltatást a következő módszerek egyikével:

- [Push telepítés](vmware-physical-mobility-service-overview.md#push-installation)
- [Kézi telepítés](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) a felhasználói felületről vagy a PowerShellből
- Központi telepítés egy központi telepítési eszköz, például a [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) használatával

## <a name="managed-disks"></a>Felügyelt lemezek

### <a name="where-does-site-recovery-replicate-data-to"></a>Hol replikálja a Site Recovery az adatokat?

A Site Recovery replikálja a helyszíni VMware virtuális gépeket és fizikai kiszolgálókat felügyelt lemezekre az Azure-ban.

- A Site Recovery folyamatkiszolgáló replikációs naplókat ír a célrégióban lévő gyorsítótártárfiókba.
- Ezek a naplók az asrseeddisk előtaggal rendelkező Azure által felügyelt lemezekhelyreállítási pontok **létrehozására szolgálnak.**
- Feladatátvétel esetén a kiválasztott helyreállítási pont egy új cél felügyelt lemez létrehozásához használatos. Ez a felügyelt lemez az Azure-ban csatlakozik a virtuális géphez.
- A korábban egy tárfiókba replikált virtuális gépeket (2019 márciusa előtt) nem érinti.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Replikálhatok új gépeket a tárfiókokba?

Nem. 2019 márciusátél kezdődően az Azure Portalon csak az Azure által felügyelt lemezekre replikálható.

Az új virtuális gépek replikálása egy tárfiókba csak a PowerShell vagy a REST API használatával érhető el (2018-01-10 vagy 2016-08-10 verzió).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Milyen előnyökkel jár a felügyelt lemezek replikálása?

[Ismerje meg, hogyan](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) A Site Recovery leegyszerűsíti a vészhelyreállítást a felügyelt lemezekkel.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Módosíthatom a felügyelt lemez típusát a számítógép védelme után?

Igen, könnyen [módosíthatja a felügyelt lemez típusát a](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) folyamatban lévő replikációkhoz. A típus módosítása előtt győződjön meg arról, hogy a felügyelt lemezen nem jön létre megosztott hozzáférésű aláírás URL-címe:

1. Nyissa meg a **Felügyelt lemez** erőforrást az Azure Portalon, és ellenőrizze, hogy rendelkezik-e megosztott hozzáférésű url-címzettel az **Áttekintés** panelen.
1. Ha a szalagcím jelen van, jelölje ki a folyamatban lévő exportálás megszakításához.
1. A következő néhány percben módosítsa a lemez típusát. Ha módosítja a felügyelt lemez típusát, várja meg, amíg az Azure Site Recovery friss helyreállítási pontokat hoz létre.
1. Használja az új helyreállítási pontokat a jövőben i tesztfeladat-átvételhez vagy feladatátvételhez.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Átválthatok a replikációt a felügyelt lemezekről a nem felügyelt lemezekre?

Nem. A felügyeltről a nem felügyeltre való váltás nem támogatott.

## <a name="replication"></a>Replikáció

### <a name="what-are-the-replicated-vm-requirements"></a>Mik a replikált virtuális gép követelményei?

[További információ](vmware-physical-azure-support-matrix.md#replicated-machines) a VMware virtuális gépek és a fizikai kiszolgálók támogatási követelményeiről.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatok az Azure-ba?

A replikáció folyamatos a VMware virtuális gépek Azure-ba történő replikálásakor.

### <a name="can-i-extend-replication"></a>Kiterjeszthetem a replikációt?

A kiterjesztett vagy láncolt replikáció nem támogatott. Kérje ezt a funkciót a [visszajelzésfórumban](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>El tudom végezni az offline kezdeti replikációt?

Az offline replikáció nem támogatott. Kérje ezt a funkciót a [visszajelzésfórumban](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Mi az asrseeddisk?

Az adatok minden forráslemezesetében replikálódnak egy felügyelt lemezre az Azure-ban. Ez a lemez az **asrseeddisk**előtaggal rendelkezik. Tárolja a forráslemez másolatát és az összes helyreállítási pont pillanatképét.

### <a name="can-i-exclude-disks-from-replication"></a>Kizárhatok lemezeket a replikációból?

Igen, kizárhatja a lemezeket.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Replikálható a dinamikus lemezekkel rendelkező virtuális gépek?

A dinamikus lemezek replikálhatók. Az operációs rendszer lemezének alaplemeznek kell lennie.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Ha replikációs csoportokat használok a többvirtuális gép konzisztenciájához, hozzáadhatok egy új virtuális gépet egy meglévő replikációs csoporthoz?

Igen, új virtuális gépeket adhat hozzá egy meglévő replikációs csoporthoz, ha engedélyezi a replikációt. Azonban:

- A replikáció megkezdése után nem adhat hozzá virtuális gépet egy meglévő replikációs csoporthoz.
- Nem hozhat létre replikációs csoportot a meglévő virtuális gépekhez.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Módosíthatom a lemezek hozzáadásával vagy átméretezésével replikáló virtuális gépeket?

A VMware-replikáció az Azure-ba, módosíthatja a lemez mérete a forrás virtuális gépek. Ha új lemezeket szeretne hozzáadni, hozzá kell adnia a lemezt, és újra engedélyeznie kell a virtuális gép védelmét.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Áttelepíthetem a helyszíni gépeket egy új vCenter-kiszolgálóra anélkül, hogy ez befolyásolna a folyamatos replikációt?

Nem. A VMware Vcenter módosítása vagy az áttelepítés hatással lesz a folyamatban lévő replikációra. Állítsa be a Site Recovery alkalmazást az új vCenter-kiszolgálóval, és engedélyezze újra a gépek replikációját.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Replikálható egy olyan gyorsítótárba vagy céltárfiókba, amelyen van egy virtuális hálózat (az Azure tűzfalak) konfigurálva van?

Nem, a Site Recovery nem támogatja az Azure Storage-ba való replikációt virtuális hálózatokon.

## <a name="component-upgrade"></a>Összetevő frissítése

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>A Mobility services ügynök vagy konfigurációs kiszolgáló saját verziója régi, és a frissítés nem sikerült. Mit tegyek?

Site Recovery követi az N-4 támogatási modell. [További információ](https://aka.ms/asr_support_statement) a nagyon régi verziókról való frissítésről.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Hol találhatom meg az Azure Site Recovery kiadási és összesítő csomagjait?

[További információ az új frissítésekről](site-recovery-whats-new.md), és [az összesítő információk beolvasása](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Hol találhatom meg a vészhelyreállítás azure-ba való frissítési adatait?

[További információ a frissítésről.](https://aka.ms/asr_vmware_upgrades)

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Minden frissítéshez újra kell indítanom a forrásgépeket?

Az újraindítás ajánlott, de nem kötelező minden frissítéshez. [További információ](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Konfigurációs kiszolgáló

### <a name="what-does-the-configuration-server-do"></a>Mire szolgál a konfigurációs kiszolgáló?

A konfigurációs kiszolgáló a helyszíni site recovery összetevőket futtatja, többek között a következőket:

- Maga a konfigurációs kiszolgáló. A kiszolgáló koordinálja a helyszíni összetevők és az Azure közötti kommunikációt, és kezeli az adatreplikációt.
- A folyamatkiszolgáló, amely replikációs átjáróként működik. Ez a szerver:
    1. Fogadja a replikaadatokat.
    2. Gyorsítótárazásával, tömörítéssel és titkosítással optimalizálja az adatokat.
    3. Elküldi az adatokat az Azure Storage-ba.
  A folyamatkiszolgáló a mobility szolgáltatás leküldéses telepítését is elvégzi virtuális gépeken, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A fő célkiszolgáló, amely az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.

[További információ](vmware-azure-architecture.md) a konfigurációs kiszolgáló összetevőiről és folyamatairól.

### <a name="where-do-i-set-up-the-configuration-server"></a>Hol állíthatom be a konfigurációs kiszolgálót?

Egyetlen, magas rendelkezésre állású, helyszíni VMware virtuális gépre van szüksége a konfigurációs kiszolgálóhoz. A kiszolgáló fizikai vészhelyreállítása érdekében telepítse a konfigurációs kiszolgálót egy fizikai számítógépre.

### <a name="what-do-i-need-for-the-configuration-server"></a>Mire van szükségem a konfigurációs kiszolgálóhoz?

Tekintse át az [előfeltételeket](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Sablon helyett manuálisan is beállíthatom a konfigurációs kiszolgálót?

Azt javasoljuk, hogy [hozza létre a konfigurációs kiszolgáló virtuális gép](vmware-azure-deploy-configuration-server.md) segítségével a legújabb verzióját az Open Virtualization Format (OVF) sablon. Ha nem tudja használni a sablont (például ha nincs hozzáférése a VMware-kiszolgálóhoz), [töltse le](physical-azure-set-up-source.md) a telepítőfájlt a portálról, és állítsa be a konfigurációs kiszolgálót.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Egy konfigurációs kiszolgáló replikálható egynél több régióra?

Nem. Több régióra való replikáláshoz minden régióban szükség van egy konfigurációs kiszolgálóra.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Üzemeltethetek konfigurációs kiszolgálót az Azure-ban?

Bár lehetséges, a konfigurációs kiszolgálót futtató Azure virtuális gépnek kommunikálnia kell a helyszíni VMware-infrastruktúrával és a virtuális gépekkel. Ez a kommunikáció késést ad hozzá, és hatással van a folyamatban lévő replikációra.

### <a name="how-do-i-update-the-configuration-server"></a>Hogyan frissíthetem a konfigurációs kiszolgálót?

[További információ](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) a konfigurációs kiszolgáló frissítéséhez.

- A legfrissebb frissítési információkat az [Azure frissítések oldalán](https://azure.microsoft.com/updates/?product=site-recovery)találja.
- A legújabb verziót a portálról töltheti le. Vagy letöltheti a konfigurációs kiszolgáló legújabb verzióját közvetlenül a [Microsoft letöltőközpontból.](https://aka.ms/asrconfigurationserver)
- Ha a verzió több mint négy verzióval régebbi, mint az aktuális verzió, tekintse meg a [támogatási útmutatót](https://aka.ms/asr_support_statement) a frissítési útmutatóban.

### <a name="should-i-back-up-the-configuration-server"></a>Érdemes biztonsági másolatot készítő biztonsági másolatot a konfigurációs kiszolgálóról?

Javasoljuk, hogy rendszeresen ütemezett biztonsági mentéseket készítsen a konfigurációs kiszolgálóról.

- A sikeres feladat-visszavétel hez a rendszer a rendszer meghibásodott virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában.
- A konfigurációs kiszolgálónak futnia kell, és csatlakoztatott állapotban kell lennie.
- [További információ](vmware-azure-manage-configuration-server.md) a konfigurációs kiszolgálók gyakori felügyeleti feladatairól.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>A konfigurációs kiszolgáló beállításakor manuálisan tölthetem le és telepíthetem a MySQL-t?

Igen. Töltse le a MySQL-t, és helyezze a C:\Temp\ASRSetup mappába. Ezután telepítse manuálisan. Amikor beállítja a konfigurációs szerver VM, és elfogadja a feltételeket, MySQL lesz felsorolva **már telepítve** **letöltése és telepítése**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Elkerülhetem a MySQL letöltését, de a Site Recovery telepítheti?

Igen. Töltse le a MySQL telepítőt, és helyezze a C:\Temp\ASRSetup mappába. Amikor beállítja a konfigurációs kiszolgáló virtuális gép, fogadja el a feltételeket, és válassza **a Letöltés és telepítés**lehetőséget . A portál a MySQL telepítéséhez a hozzáadott telepítőt fogja használni.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Használhatom a konfigurációs kiszolgáló virtuális gép mást?

Nem. Csak a konfigurációs kiszolgálóhoz használja a virtuális gép használatát.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Klónozhatok egy konfigurációs kiszolgálót, és használhatom vezénylésre?

Nem. A regisztrációs problémák elkerülése érdekében állítson be egy új konfigurációs kiszolgálót.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Módosíthatom azt a tárolót, amelyben a konfigurációs kiszolgáló regisztrálva van?

Nem. Miután a tároló társítva van a konfigurációs kiszolgáló, nem lehet módosítani. [További információ](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) egy másik tárolóval rendelkező konfigurációs kiszolgáló regisztrálásáról.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Használhatom ugyanazt a konfigurációs kiszolgálót a VMware virtuális gépek és a fizikai kiszolgálók vészhelyreállításához?

Igen, de vegye figyelembe, hogy a fizikai gép csak egy VMware virtuális gép re nem sikerült vissza.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Hol tölthetem le a konfigurációs kiszolgáló jelmondatát?

[További információ](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) a jelszó letöltéséhez.

### <a name="where-can-i-download-vault-registration-keys"></a>Hol tölthetem le a trezor regisztrációs kulcsait?

A Helyreállítási szolgáltatások tárolójában válassza a Configuration Servers (Hely-helyreállítási **infrastruktúra** > kezelése) **konfigurációs kiszolgálók** **lehetőséget.** Ezután a **Kiszolgálók alkalmazásban**válassza a **Regisztrációs kulcs letöltése** lehetőséget a tároló hitelesítő fájljának letöltéséhez.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Egyetlen konfigurációs kiszolgáló használható több vCenter-példány védelmére?

Igen, egyetlen konfigurációs kiszolgáló több virtuális központvirtuális gépek et is védhet.  Nincs korlátozva, hogy hány vCenter-példány adható hozzá a konfigurációs kiszolgálóhoz, azonban a korlátok, hogy hány virtuális gép egy konfigurációs kiszolgáló képes megvédeni nem vonatkoznak.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Egyetlen konfigurációs kiszolgáló több fürtöt is védhet a vCenteren belül?

Igen, az Azure Site Recovery különböző fürtök önkiszolgáló virtuális gépek et védhet.

## <a name="process-server"></a>Folyamatkiszolgáló

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Miért nem tudom kiválasztani a folyamatkiszolgálót, amikor engedélyezem a replikációt?

A 9.24-es és újabb verziókfrissítései a [replikáció engedélyezésekor megjelenítik a folyamatkiszolgáló állapotát.](vmware-azure-enable-replication.md#enable-replication) Ez a szolgáltatás segít elkerülni a folyamat-kiszolgáló szabályozását, és minimálisra csökkenti a nem megfelelő állapotú folyamatkiszolgálók használatát.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Hogyan frissíthetem a folyamatkiszolgálót a 9.24-es vagy újabb verzióra a pontos állapotinformációkért?

A [9.24-es verziótól](service-updates-how-to.md#links-to-currently-supported-update-rollups)kezdve további riasztások lettek hozzáadva a folyamatkiszolgáló állapotának jelzésére. [Frissítse a Site Recovery összetevőit a 9.24-es vagy újabb verzióra,](service-updates-how-to.md#links-to-currently-supported-update-rollups) hogy minden riasztás létrejön.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Hogyan biztosíthatom a folyamatkiszolgáló magas rendelkezésre állását?

Több folyamatkiszolgáló konfigurálásával a kialakítás rugalmasságot biztosít a védett gépek egészségtelen folyamatkiszolgálóról a munkafolyamat-kiszolgálóra való áthelyezéséhez. A gép egyik folyamatkiszolgálóról a másikra történő áthelyezését explicit módon/manuálisan kell kezdeményezni a megadott lépéseken keresztül: [virtuális gépek áthelyezése a folyamatkiszolgálók között](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Használhatom a helyszíni folyamatkiszolgálót feladat-visszavételhez?

Azt javasoljuk, hogy hozzon létre egy folyamatkiszolgálót az Azure-ban feladat-visszavételi célokra, az adatátviteli késések elkerülése érdekében. Emellett abban az esetben, ha a forrás virtuális gépek hálózatát az Azure-kapcsolatban álló hálózata a konfigurációs kiszolgálón, elengedhetetlen, hogy használja a folyamat kiszolgáló az Azure-ban létrehozott feladat-visszavétel.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Megtarthatom az IP-címet a feladatátvételen?

Igen, az IP-cím feladatátvételkor is megtartható. Győződjön meg arról, hogy adja meg a cél IP-címet a **számítási és hálózati** beállításokat a virtuális gép feladatátvétel előtt. Emellett állítsa le a gépeket a feladatátvétel időpontjában, hogy elkerülje az IP-cím ütközéseit a feladat-visszavétel során.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Módosíthatom a cél virtuális gép méretét vagy a virtuális gép típusát a feladatátvétel előtt?

Igen, a feladatátvétel előtt bármikor módosíthatja a virtuális gép típusát vagy méretét. A portálon használja a **számítási és hálózati** beállításokat a replikált virtuális gép.

### <a name="how-far-back-can-i-recover"></a>Milyen messzire tudok visszaállni?

A VMware az Azure-ba, a legrégebbi helyreállítási pont, amelyet használhat, 72 óra.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan érhetem el az Azure virtuális gépek feladatátvétel után?

Feladatátvétel után biztonságos internetkapcsolaton, helyek közötti VPN-en vagy Azure ExpressRoute-on keresztül érheti el az Azure virtuális gépeket. A csatlakozáshoz több dolgot is elő kell készítenie. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>A feladatátvételi adatok rugalmasak?

Az Azure-t hibatűrőnek terveztük. A Site Recovery egy másodlagos Azure-adatközpontba való feladatátvételre van tervezve, az Azure szolgáltatásiszint-szerződés (SLA) által megkövetelt szerint. Feladatátvétel esetén gondoskodunk arról, hogy a metaadatok és a tárolók ugyanabban a földrajzi régióban maradjanak, amelyet a tárolóhoz választott.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

[A feladatátvétel](site-recovery-failover.md) nem automatikus. A feladatátvételt egyetlen kijelöléssel a portálon, vagy a [PowerShell](/powershell/module/az.recoveryservices) használatával elindíthatja a feladatátvételt.

### <a name="can-i-fail-back-to-a-different-location"></a>Visszatehetek egy másik helyre?

Igen. Ha feladatátvételt végzett az Azure-ba, visszavehetegy másik helyre, ha az eredeti nem érhető el. [További információ](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Miért van szükségem egy VPN vagy ExpressRoute privát társviszony-létesítés a feladat-vissza?

Amikor visszaadja a feladatát az Azure-ból, az Azure-ból származó adatokat a rendszer visszamásolja a helyszíni virtuális gépre, és magánjellegű hozzáférés szükséges.


## <a name="automation-and-scripting"></a>Automatizálás és parancsfájlok

### <a name="can-i-set-up-replication-with-scripting"></a>Beállíthatok replikációt parancsfájlokkal?

Igen. A Site Recovery-munkafolyamatok automatizálhatók a Rest API, a PowerShell vagy az Azure SDK használatával. [További információ](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Teljesítmény és kapacitás

### <a name="can-i-throttle-replication-bandwidth"></a>Szabályozható a replikációs sávszélesség?

Igen. [További információ](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>További lépések

- [Tekintse át](vmware-physical-azure-support-matrix.md) a támogatási követelményeket.
- [Beállítás](vmware-azure-tutorial.md) VMware az Azure replikációba.
