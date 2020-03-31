---
title: A PowerShell használata a DPM-munkaterhelések biztonsági ellátásához
description: Megtudhatja, hogy miként telepítheti és kezelheti az Azure Backup for Data Protection Manager (DPM) szolgáltatást a PowerShell használatával
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 06c138a4015a0b730369e091fc57a34d2190051d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616737"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Az Azure-ba történő biztonsági mentés üzembe helyezése és kezelése DPM-kiszolgálókon a PowerShell-lel

Ez a cikk bemutatja, hogyan használhatja a PowerShellt az Azure Backup dpm-kiszolgálón történő beállításához, valamint a biztonsági mentés és helyreállítás kezeléséhez.

## <a name="setting-up-the-powershell-environment"></a>A PowerShell-környezet beállítása

Mielőtt a PowerShell segítségével kezelheti a biztonsági mentéseket az Adatvédelmi menedzsertől az Azure-ba, a megfelelő környezettel kell rendelkeznie a PowerShellben. A PowerShell-munkamenet kezdetén győződjön meg arról, hogy a következő parancsot futtatja a megfelelő modulok importálásához, és lehetővé teszi a DPM-parancsmagok helyes hivatkozását:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Beállítás és regisztráció

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Először [töltse le a legújabb Azure PowerShellt.](/powershell/azure/install-az-ps)

A Következő beállítási és regisztrációs feladatok automatizálhatók a PowerShell segítségével:

* Recovery Services-tároló létrehozása
* Az Azure Backup ügynök telepítése
* Regisztráció az Azure Backup szolgáltatással
* Hálózati beállítások
* Titkosítási beállítások

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések segítségével hozzon létre egy Recovery Services-tároló. A Recovery Services-tároló eltér a biztonsági mentési tároló.

1. Ha első alkalommal használja az Azure Backup szolgáltatást, a **Register-AzResourceProvider** parancsmaggal kell regisztrálnia az Azure Recovery Service szolgáltatót az előfizetéssel.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. A Recovery Services-tároló egy ARM-erőforrás, ezért egy erőforráscsoporton belül kell elhelyeznie. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat. Új erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. A **New-AzRecoveryServicesVault** parancsmag használatával hozzon létre egy új tárolót. Ügyeljen arra, hogy adja meg ugyanazt a helyet a tároló, mint az erőforráscsoport használt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Adja meg a használni kívánt tárolási redundancia típusát; [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) vagy [georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md)használható. A következő példa bemutatja a -BackupStorageRedundancy beállítás testVault van beállítva GeoRedundant.

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintése egy előfizetésben

A **Get-AzRecoveryServicesVault** segítségével tekintse meg az aktuális előfizetés összes tárolójának listáját. Ezzel a paranccsal ellenőrizheti, hogy új tárolót hoztak-e létre, vagy megtekintheti, hogy milyen tárolók érhetők el az előfizetésben.

Futtassa a Get-AzRecoveryServicesVault parancsot, és az előfizetés összes tárolója megjelenik.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Az Azure Backup-ügynök telepítése DPM-kiszolgálóra

Az Azure Backup-ügynök telepítése előtt le kell töltenie a telepítőt, és meg kell jelenjen a Windows Serveren. A telepítő legújabb verzióját a Microsoft [letöltőközpontjából](https://aka.ms/azurebackup_agent) vagy a Helyreállítási szolgáltatások tároló irányítópultja lapjáról szerezheti be. Mentse a telepítőt egy könnyen elérhető helyre, például *C:\Letöltések\*.

Az ügynök telepítéséhez futtassa a következő parancsot egy emelt szintű **PowerShell-konzolon a DPM-kiszolgálón:**

```powershell
MARSAgentInstaller.exe /q
```

Ezzel telepíti az ügynököt az összes alapértelmezett beállítással. A telepítés néhány percet vesz igénybe a háttérben. Ha nem adja meg a */nu* kapcsolót, a **Windows Update** ablak a telepítés végén megnyílik, hogy ellenőrizze a frissítéseket.

Az ügynök megjelenik a telepített programok listájában. A telepített programok listájának megtekintéséhez nyissa meg a **Vezérlőpult** > **programok** > **és szolgáltatások segédprogramját.**

![Ügynök telepítve](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Telepítési lehetőségek

A parancssoron keresztül elérhető összes beállítás megtekintéséhez használja a következő parancsot:

```powershell
MARSAgentInstaller.exe /?
```

A rendelkezésre álló lehetőségek a következők:

| Beállítás | Részletek | Alapértelmezett |
| --- | --- | --- |
| /q |Csendes telepítés |- |
| /p:"hely" |Az Azure Backup-ügynök telepítési mappájának elérési útja. |C:\Program Files\Microsoft Azure helyreállítási szolgáltatások ügynöke |
| /s:"hely" |Az Azure backup-ügynök gyorsítótár-mappájának elérési útja. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Engedélyezés a Microsoft Update szolgáltatásba |- |
| /nu |A telepítés befejezése után ne ellenőrizze a frissítéseket |- |
| /d |A Microsoft Azure helyreállítási szolgáltatások ügynökének eltávolítása |- |
| /h |Proxyállomás címe |- |
| /po |Proxyállomás portjának száma |- |
| /pu |Proxyállomás felhasználóneve |- |
| /pw |Proxy jelszava |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>A DPM regisztrálása a helyreállítási szolgáltatások tárolójában

Miután létrehozta a Recovery Services-tárolót, töltse le a legújabb ügynököt és a tároló hitelesítő adatait, és tárolja azt egy kényelmes helyen, például a C:\Letöltések.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

A DPM-kiszolgálón futtassa a [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) parancsmafut a gép regisztrálásához a tárolóval.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Kezdeti konfigurációs beállítások

Miután regisztrálta a DPM-kiszolgálót a Helyreállítási szolgáltatások tárolójában, az alapértelmezett előfizetési beállításokkal kezdődik. Ezek az előfizetési beállítások közé tartozik a hálózatkezelés, a titkosítás és az átmeneti terület. Az előfizetési beállítások módosításához először le kell tudnia a meglévő (alapértelmezett) beállításokat a [Get-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) parancsmag használatával:

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Minden módosítás történik a helyi PowerShell-objektum, ```$setting``` majd a teljes objektum véglegesíti a DPM és az Azure Backup menteni őket a [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) parancsmag használatával. A módosítások megőrzéséhez a ```–Commit``` jelzőt kell használnia. A beállításokat az Azure Backup nem alkalmazza és használja, ha nincs véglegesítve.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Hálózat

Ha a DPM-gép kapcsolata az Azure Backup szolgáltatás az interneten keresztül egy proxy kiszolgálón keresztül, majd a proxykiszolgáló beállításait kell biztosítani a sikeres biztonsági mentések. Ez a és ```-ProxyServer```a ```-ProxyPort``` ```-ProxyUsername``` és ```ProxyPassword``` a paraméterek et használja a [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) parancsmaggal. Ebben a példában nincs proxykiszolgáló, ezért kifejezetten törli a proxyval kapcsolatos információkat.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Sávszélesség-használat is vezérelhető ```-WorkHourBandwidth``` a ```-NonWorkHourBandwidth``` lehetőségeket, és egy adott sor a hét. Ebben a példában nem állítunk be semmilyen szabályozást.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Az átmeneti terület konfigurálása

A DPM-kiszolgálón futó Azure Backup-ügynöknek ideiglenes tárolásra van szüksége a felhőből (helyi átmeneti területről) visszaállított adatokhoz. Konfigurálja az átmeneti területet a [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) ```-StagingAreaPath``` parancsmag és a paraméter használatával.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

A fenti példában az átmeneti terület ```$setting``` *A PowerShell-objektumBan C:\StagingArea* lesz. Győződjön meg arról, hogy a megadott mappa már létezik, különben az előfizetési beállítások végleges véglegesítése sikertelen lesz.

### <a name="encryption-settings"></a>Titkosítási beállítások

Az Azure Backup-ba küldött biztonsági mentési adatok titkosítva vannak az adatok titkosságának védelme érdekében. A titkosítási jelszó az adatok visszafejtésének "jelszava". Fontos, hogy ezeket az információkat biztonságosan és biztonságosan őrizzék meg, amint be van állítva.

Az alábbi példában az első parancs ```passphrase123456789``` biztonságos karakterláncká alakítja a karakterláncot, ```$Passphrase```és hozzárendeli a biztonságos karakterláncot a nevű változóhoz. a második parancs beállítja a biztonságos karakterláncot a biztonsági mentések titkosításához szükséges ```$Passphrase``` jelszóként.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> A jelszó adatainak biztonsága és biztonsága a beállítás után. Ez a jelszó nélkül nem tudja visszaállítani az azure-beli adatokat.
>
>

Ezen a ponton el kellett volna végeznie ```$setting``` az objektum összes szükséges módosítását. Ne felejtse el véglegesen végrehajtani a módosításokat.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Adatok védelme az Azure Backup szolgáltatásban

Ebben a szakaszban egy éles kiszolgálót ad hozzá a DPM-hez, majd megvédi az adatokat a helyi DPM-tárolóhoz, majd az Azure Backuphoz. A példákban bemutatjuk, hogyan lehet biztonsági másolatot adni a fájlokról és mappákról. A logika könnyen kiterjeszthető bármely DPM által támogatott adatforrás biztonsági mentésére. Az összes DPM biztonsági mentést egy négy részből álló védelmi csoport (PG) szabályozza:

1. **A csoporttagok** azoknak a védett objektumoknak (más néven *adatforrások* a DPM-ben) a listája, amelyeket ugyanabban a védelmi csoportban szeretne megvédeni. Előfordulhat például, hogy az egyik védelmi csoportban és egy másik védelmi csoportban lévő SQL Server-adatbázisokban lévő éles virtuális gépeket szeretné védeni, mivel eltérő biztonsági mentési követelményekkel rendelkezhetnek. Az éles kiszolgálón lévő adatforrások biztonsági másolata előtt meg kell győződnie arról, hogy a DPM-ügynök telepítve van a kiszolgálón, és a DPM kezeli. Kövesse a [DPM-ügynök telepítésének](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) és a megfelelő DPM-kiszolgálóhoz való csatolásának lépéseit.
2. **Az adatvédelmi módszer** meghatározza a célbiztonsági mentési helyeket - szalag, lemez és felhő. A példánkban a helyi lemezre és a felhőre vonatkozó adatokat védjük.
3. Biztonsági **másolat ütemezése,** amely meghatározza, hogy mikor kell biztonsági másolatokat készíteni, és milyen gyakran kell szinkronizálni az adatokat a DPM-kiszolgáló és az éles kiszolgáló között.
4. Megőrzési **ütemezés,** amely meghatározza, hogy mennyi ideig kell megőrizni a helyreállítási pontokat az Azure-ban.

### <a name="creating-a-protection-group"></a>Védelmi csoport létrehozása

Kezdje azzal, hogy új védelmi csoportot hoz létre a [New-DPMProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019) parancsmag használatával.

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

A fenti parancsmag létrehoz egy *ProtectGroup01*nevű védelmi csoportot. Egy meglévő védelmi csoport is módosítható később biztonsági mentés hozzáadása az Azure-felhőben. Ahhoz azonban, hogy bármilyen módosítást a védelmi csoport - új vagy meglévő - meg kell, hogy egy fogantyút egy *módosítható* objektum segítségével [get-DPMModifiableProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019) parancsmag.

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Csoporttagok hozzáadása a védelmi csoporthoz

Minden DPM-ügynök ismeri azon a kiszolgálón lévő adatforrások listáját, amelyen telepítve van. Ha adatforrást szeretne hozzáadni a védelmi csoporthoz, a DPM-ügynöknek először vissza kell küldenie az adatforrások listáját a DPM-kiszolgálóra. Ezután kijelöl egy vagy több adatforrást, és hozzáadódik a védelmi csoporthoz. Ennek eléréséhez szükséges PowerShell-lépések a következők:

1. A DPM által a DPM-ügynökön keresztül kezelt kiszolgálók listájának lekérése.
2. Válasszon egy adott kiszolgálót.
3. A kiszolgálón lévő összes adatforrás listájának beolvasása.
4. Válasszon ki egy vagy több adatforrást, és vegye fel őket a védelmi csoportba

Azoknak a kiszolgálóknak a listáját, amelyekre a DPM-ügynök telepítve van, és amelyet a DPM-kiszolgáló kezel, a [Get-DPMProductionServer](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) parancsmag gal szerzi be. Ebben a példában szűrjük, és csak konfigurálni PS név *productionserver01* biztonsági mentés.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Most a ```$server``` [Get-DPMDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019) parancsmag használatával olvassa be az adatforrások listáját. Ebben a példában a D kötetet *szűrjük:\\ * amelyet biztonsági mentéshez szeretnénk konfigurálni. Ez az adatforrás ezután hozzáadódik a védelmi csoporthoz az [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) parancsmag használatával. Ne felejtse *modifiable* el használni a ```$MPG``` módosítható védelmi csoport objektumot a kiegészítések.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Ismételje meg ezt a lépést annyiszor, ahányszor csak szükséges, amíg hozzá nem adta az összes kiválasztott adatforrást a védelmi csoporthoz. Akár csak egy adatforrással is elindíthatja, és befejezheti a védelmi csoport létrehozásához szükséges munkafolyamatot, majd egy későbbi időpontban további adatforrásokat adhat hozzá a védelmi csoporthoz.

### <a name="selecting-the-data-protection-method"></a>Az adatvédelmi módszer kiválasztása

Miután az adatforrásokat hozzáadta a védelmi csoporthoz, a következő lépés a védelmi módszer megadása a [Set-DPMProtectionType](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) parancsmag használatával. Ebben a példában a védelmi csoport a helyi lemez és a felhőalapú biztonsági mentés beállításával van beállítva. Azt is meg kell adnia az adatforrást, amelyet meg szeretne védeni a felhőbe az [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) parancsmag -Online jelzővel.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>A megőrzési tartomány beállítása

Állítsa be a biztonsági mentési pontok megőrzését a [Set-DPMPolicyObjective](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) parancsmag használatával. Bár furcsának tűnhet, hogy a biztonsági mentésütemezés definiálása ```Set-DPMPolicyObjective``` előtt állítsa be a megőrzést, a parancsmag használatával automatikusan beállítja az alapértelmezett biztonsági mentési ütemezést, amely et aztán módosítani lehet. Mindig lehet beállítani a biztonsági mentési ütemezés első és az adatmegőrzési szabály után.

Az alábbi példában a parancsmag beállítja a lemezbiztonsági mentések megőrzési paramétereit. Ez 10 napig megőrzi a biztonsági másolatokat, és 6 óránként szinkronizálja az adatokat az éles kiszolgáló és a DPM-kiszolgáló között. A ```SynchronizationFrequencyMinutes``` nem határozza meg, hogy milyen gyakran jön létre egy biztonsági mentési pont, hanem azt, hogy milyen gyakran másolja az adatokat a DPM-kiszolgálóra.  Ez a beállítás megakadályozza, hogy a biztonsági mentések túl nagyok legyenek.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Az Azure-ba irányuló biztonsági mentések (a DPM online biztonsági mentésként hivatkozik rájuk) a megőrzési tartományok hosszú távú megőrzésre konfigurálhatók [egy Nagyapa-Apa-Fia séma (GFS) használatával.](backup-azure-backup-cloud-as-tape.md) Ez azt, hogy napi, heti, havi és éves adatmegőrzési szabályzatokat tartalmazó kombinált adatmegőrzési szabályzatot határozhat meg. Ebben a példában létrehozunk egy tömbet, amely a kívánt összetett megőrzési sémát képviseli, majd konfigurálja a megőrzési tartományt a [Set-DPMPolicyObjective](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) parancsmag használatával.

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>A biztonsági mentés ütemezésének beállítása

A DPM automatikusan beállítja az alapértelmezett ```Set-DPMPolicyObjective``` biztonsági mentési ütemezést, ha a védelmi célt a parancsmag használatával adja meg. Az alapértelmezett ütemezések módosításához használja a [Get-DPMPolicySchedule](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019) parancsmac, majd a [Set-DPMPolicySchedule](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019) parancsmag.

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

A fenti példában egy négy elemet tartalmazó tömb található, ```$onlineSch``` amely a GFS-séma védelmi csoportjának meglévő online védelmi ütemezését tartalmazza:

1. ```$onlineSch[0]```tartalmazza a napi ütemezést
2. ```$onlineSch[1]```tartalmazza a heti ütemezést
3. ```$onlineSch[2]```tartalmazza a havi ütemezést
4. ```$onlineSch[3]```tartalmazza az éves ütemezést

Tehát, ha módosítania kell a heti ütemezést, akkor a . ```$onlineSch[1]```

### <a name="initial-backup"></a>Kezdeti biztonsági mentés

Amikor első alkalommal készít biztonsági másolatot egy adatforrásról, a DPM-nek létre kell adnia a kezdeti kópiát, amely létrehozza a DPM replikaköteten védendő adatforrás teljes másolatát. Ez a tevékenység ütemezhető egy adott időre, vagy manuálisan is elindítható a [Set-DPMReplicaCreationMethod](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) parancsmag használatával a paraméterrel. ```-NOW```

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>A DPM-kópia & helyreállításipont-kötet méretének módosítása

A DPM replikakötet és az árnyékmásolat-kötet méretét a [Set-DPMDatasourceDiskAllocation](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019) parancsmag segítségével is módosíthatja a következő példában: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>A módosítások véglegesítése a védelmi csoportban

Végül a módosításokat kell végleges, mielőtt DPM vehet a biztonsági mentés az új védelmi csoport konfigurációja. Ez a [Set-DPMProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019) parancsmag használatával érhető el.

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>A biztonsági mentési pontok megtekintése

A [Get-DPMRecoveryPoint](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) parancsmag segítségével lehívhatja az adatforrás összes helyreállítási pontjának listáját. Ebben a példában a következőket fogjuk:

* a DPM-kiszolgáló összes PG-jét, és egy tömbben tároljuk```$PG```
* a megfelelő adatforrások beszerezhetése```$PG[0]```
* az adatforrás összes helyreállítási pontját.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Az Azure-ban védett adatok visszaállítása

Az adatok visszaállítása egy ```RecoverableItem``` objektum ```RecoveryOption``` és egy objektum kombinációja. Az előző szakaszban egy adatforrás biztonsági mentési pontjainak listája található.

Az alábbi példában bemutatjuk, hogyan állíthatja vissza a Hyper-V virtuális gépet az Azure Backup szolgáltatásból a biztonsági mentési pontok és a helyreállítási cél kombinálásával. Ez a példa a következőket tartalmazza:

* Helyreállítási lehetőség létrehozása az [Új-DPMRecoveryOption](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019) parancsmag használatával.
* A biztonsági mentési pontok ```Get-DPMRecoveryPoint``` tömbjének beolvasása a parancsmag használatával.
* A visszaállítani hozandó biztonsági mentési pont kiválasztása.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

A parancsok könnyen bővíthetők bármilyen adatforrástípushoz.

## <a name="next-steps"></a>További lépések

* Az Azure Backup DPM szolgáltatásáról további információt [A DPM biztonsági mentése című témakörben talál.](backup-azure-dpm-introduction.md)
