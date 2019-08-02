---
title: A Windows Server biztonsági mentése a PowerShell használatával az Azure-ba
description: Útmutató Azure Backup üzembe helyezéséhez és kezeléséhez a PowerShell használatával
ms.reviewer: shivamg
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: dacurwin
ms.openlocfilehash: bb488db036b99d3826a3060a7f4143bec7aea3e5
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688572"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Az Azure-ba történő biztonsági mentés üzembe helyezése és kezelése Windows Server vagy Windows-ügyfél rendszereken a PowerShell-lel

Ez a cikk bemutatja, hogyan használható a PowerShell a Azure Backup Windows Serveren vagy Windows-ügyfélen való beállításához, valamint a biztonsági mentés és a helyreállítás kezeléséhez.

## <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Első lépésként [telepítse a PowerShell legújabb verzióját](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések végigvezetik a Recovery Services-tároló létrehozásának lépésein. Egy Recovery Services-tár nem azonos a Backup-tárolóval.

1. Ha első alkalommal használja a Azure Backup-t, a **Register-AzResourceProvider** parancsmaggal regisztrálnia kell az Azure Recovery Service providert az előfizetésében.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. A Recovery Services tároló egy ARM-erőforrás, ezért egy erőforráscsoporthoz kell helyeznie. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat. Új erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Az új tároló létrehozásához használja a **New-AzRecoveryServicesVault** parancsmagot. Ügyeljen arra, hogy ugyanazt a helyet határozza meg a tárolóhoz, mint amelyet az erőforráscsoport használ.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Adja meg a használandó tárolási redundancia típusát; használhatja a [helyileg redundáns tárolást (LRS)](../storage/common/storage-redundancy-lrs.md) vagy a [geo redundáns tárolást (GRS)](../storage/common/storage-redundancy-grs.md). Az alábbi példa a-BackupStorageRedundancy beállítást mutatja be a testVault beállításnál a GeoRedundant értékre.

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Az előfizetésben található tárolók megtekintése

A **Get-AzRecoveryServicesVault** használatával megtekintheti az aktuális előfizetésben található összes tároló listáját. Ezzel a paranccsal ellenőrizhető, hogy az új tároló létrejött-e, vagy hogy milyen tárolók érhetők el az előfizetésben.

Futtassa a parancsot, a **Get-AzRecoveryServicesVault**és az előfizetés összes tárolóját.

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


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Az Azure Backup-ügynök telepítése

A Azure Backup ügynök telepítése előtt le kell töltenie a telepítőt, és be kell jelentkeznie a Windows Serveren. A telepítő legújabb verzióját a [Microsoft letöltőközpontból](https://aka.ms/azurebackup_agent) vagy a Recovery Services-tároló irányítópult-lapjáról szerezheti be. Mentse a telepítőt egy könnyen elérhető helyre, például *\*letöltések.

Másik lehetőségként a PowerShellt is használhatja a letöltött letöltéséhez:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Az ügynök telepítéséhez futtassa a következő parancsot egy emelt szintű PowerShell-konzolon:

```powershell
MARSAgentInstaller.exe /q
```

Ezzel telepíti az ügynököt az összes alapértelmezett beállítással. A telepítés eltarthat néhány percig a háttérben. Ha nem adja meg a */Nu* beállítást, akkor a telepítés végén megnyílik a **Windows Update** ablak, hogy ellenőrizze a frissítéseket. A telepítés után az ügynök megjelenik a telepített programok listájában.

 > A telepített programok listájának megtekintéséhez válassza a Vezérlőpult**programok** > **programok és szolgáltatások**elemét.

![Ügynök telepítve](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Telepítési beállítások

A parancssoron keresztül elérhető összes lehetőség megtekintéséhez használja a következő parancsot:

```powershell
MARSAgentInstaller.exe /?
```

Az elérhető lehetőségek a következők:

| Beállítás | Részletek | Alapértelmezett |
| --- | --- | --- |
| /q |Csendes telepítés |- |
| /p: "location" |A Azure Backup ügynök telepítési mappájának elérési útja. |C:\Program Files\Microsoft Azure Recovery Services ügynök |
| /s: "location" |A Azure Backup ügynökhöz tartozó gyorsítótár mappájának elérési útja. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Microsoft Update |- |
| /nu |Frissítések keresése a telepítés befejezése után |- |
| /d |Microsoft Azure Recovery Services ügynök eltávolítása |- |
| /ph |Proxy gazdagép címe |- |
| /po |Proxy gazdagép portszáma |- |
| /pu |Proxy gazdagép felhasználóneve |- |
| /PW |Proxy jelszava |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Windows Server vagy Windows rendszerű ügyfélszámítógép regisztrálása Recovery Services-tárolóba

Miután létrehozta a Recovery Services-tárolót, töltse le a legújabb ügynököt és a tároló hitelesítő adatait, és tárolja a megfelelő helyen (például C:\Downloads.).

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

A Windows Server vagy a Windows rendszerű ügyfélszámítógépen futtassa a [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) parancsmagot a gép a tárolóban való regisztrálásához.
A biztonsági mentéshez használt egyéb parancsmagok a MSONLINE modulból származnak, amely a Mars AgentInstaller a telepítési folyamat részeként lett hozzáadva.

Az ügynök telepítője nem frissíti a $Env:P SModulePath változót. Ez azt jelenti, hogy a modul automatikus betöltése meghiúsul. A probléma megoldásához tegye a következőket:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Azt is megteheti, hogy manuálisan betölti a modult a szkriptben a következőképpen:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Miután betöltötte az online Backup-parancsmagokat, regisztrálja a tároló hitelesítő adatait:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Ne használja a relatív elérési utakat a tár hitelesítő adatainak fájljának megadásához. Abszolút elérési utat kell megadnia bemenetként a parancsmaghoz.
>
>

## <a name="networking-settings"></a>Hálózati beállítások

Ha a Windows rendszerű gép internetkapcsolata egy proxykiszolgálón keresztül történik, a proxybeállítások az ügynöknek is megadhatók. Ebben a példában nincs proxykiszolgáló, ezért explicit módon töröljük a proxyval kapcsolatos információkat.

A sávszélesség `work hour bandwidth` - `non-work hour bandwidth` használat a hét egy adott halmazának beállításaival is vezérelhető.

A proxy és a sávszélesség adatainak beállítása a [set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) parancsmag használatával történik:

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Titkosítási beállítások

A Azure Backup elküldett biztonsági mentési adatok titkosítva vannak az adatok titkosságának védelme érdekében. A titkosítási jelszó a "password" (jelszó), amely a visszaállításkor visszafejti az adatmennyiséget.

A **Létrehozás**lehetőség kiválasztásával biztonsági PIN-kódot kell megadnia a Azure Portal **Recovery Services** tároló szakaszának **Beállítások** > **tulajdonságai** > **biztonsági PIN** -kódjában. Ezt `generatedPIN` követően használja a parancsot a következő paranccsal:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Ha beállította, a hozzáférési kód adatai biztonságban és biztonságban maradnak. Ezen jelszó nélkül nem állíthatók vissza az Azure-ból származó adatok.
>
>

## <a name="back-up-files-and-folders"></a>Fájlok és mappák biztonsági mentése

A Windows-kiszolgálókról és-ügyfelekről Azure Backup összes biztonsági mentését egy házirend szabályozza. A szabályzat három részből áll:

1. **Biztonsági mentési ütemezés** , amely meghatározza, hogy mikor kell a biztonsági mentést készíteni és szinkronizálni a szolgáltatással.
2. Egy **adatmegőrzési ütemterv** , amely meghatározza, hogy mennyi ideig kell megőrizni a helyreállítási pontokat az Azure-ban.
3. A **fájlok felvételének/kizárásának specifikációja** , amely azt határozza meg, hogy miről kell biztonsági mentést készíteni.

Ebben a dokumentumban a biztonsági mentés automatizálásakor feltételezzük, hogy a rendszer semmit sem konfigurált. Először hozzon létre egy új biztonsági mentési szabályzatot a [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) parancsmag használatával.

```powershell
$NewPolicy = New-OBPolicy
```

Ekkor a szabályzat üres, és más parancsmagokra van szükség ahhoz, hogy meghatározza, hogy milyen elemek lesznek befoglalva vagy kizárva a biztonsági mentések futtatásakor, és hol lesznek tárolva a biztonsági másolatok.

### <a name="configuring-the-backup-schedule"></a>A biztonsági mentés ütemtervének konfigurálása

A szabályzat 3 részének első eleme a biztonsági mentési ütemterv, amely a [New-OBSchedule](https://technet.microsoft.com/library/hh770401) parancsmag használatával jön létre. A biztonsági mentés ütemezése határozza meg, hogy mikor kell a biztonsági mentést készíteni. Az ütemterv létrehozásakor 2 bemeneti paramétert kell megadnia:

* A **hét azon napjai** , amelyeken a biztonsági mentést futtatni kell. A biztonsági mentési feladatot csak egy napon, vagy a hét minden napján, illetve a kettő közötti kombinációban futtathatja.
* A **nap azon időpontja,** amikor a biztonsági mentést futtatni kell. A biztonsági mentés elindítása után akár 3 különböző időpontot is meghatározhat.

Beállíthat például egy olyan biztonsági mentési szabályzatot, amely minden szombaton és vasárnap 16:00 órakor fut.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

A biztonsági mentési ütemtervet hozzá kell rendelni egy szabályzathoz, és ezt a [set-OBSchedule](https://technet.microsoft.com/library/hh770407) parancsmag használatával lehet megvalósítani.

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Adatmegőrzési szabály konfigurálása

A megőrzési házirend határozza meg, hogy a biztonsági mentési feladatokból létrehozott helyreállítási pontok mennyi ideig maradnak meg. Amikor új adatmegőrzési szabályzatot hoz létre a [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) parancsmaggal, megadhatja, hogy hány nap elteltével kell megőrizni a biztonsági mentési helyreállítási pontokat a Azure Backup. Az alábbi példa egy 7 napos adatmegőrzési szabályt állít be.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Az adatmegőrzési szabályzatot a [set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405)parancsmag használatával kell társítani a fő házirenddel:

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### <a name="including-and-excluding-files-to-be-backed-up"></a>A biztonsági mentéshez szükséges fájlok belefoglalása és kizárása

Egy `OBFileSpec` objektum határozza meg a biztonsági másolatban szerepeltetni és kizárni kívánt fájlokat. Ez a szabályok olyan halmaza, amely a számítógépen lévő védett fájlokat és mappákat hatókörbe állítja. A szükségesnél több befoglalási vagy kizárási szabályt is megadhat, és hozzárendelheti őket egy szabályzathoz. Új OBFileSpec-objektum létrehozásakor a következőket teheti:

* A felvenni kívánt fájlok és mappák meghatározása
* A kizárandó fájlok és mappák meghatározása
* Adja meg a mappában található adatbiztonsági mentést (vagy), hogy csak a megadott mappában lévő legfelső szintű fájlokat kell-e biztonsági másolatot készíteni.

Az utóbbit a New-OBFileSpec parancsban a-nem rekurzív jelző használatával érhetjük el.

Az alábbi példában biztonsági mentést készítünk a C: és D kötetről, és kizárjuk az operációs rendszer bináris fájljait a Windows mappában és bármely ideiglenes mappában. Ehhez hozzon létre két fájl specifikációt a [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) parancsmag használatával – egyet a bevonáshoz, egyet pedig a kizáráshoz. A fájl specifikációinak létrehozása után a [rendszer az Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) parancsmaggal társítja őket a szabályzathoz.

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>A Windows Server rendszerállapotának biztonsági mentése a MABS-ügynökben

Ez a szakasz a MABS-ügynök rendszerállapotának beállítására szolgáló PowerShell-parancsot ismerteti.

### <a name="schedule"></a>Ütemezés
```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Adatmegőrzés

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Az ütemterv és a megőrzés konfigurálása

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>A szabályzat ellenőrzése

```powershell
Get-OBSystemStatePolicy
 ```

### <a name="applying-the-policy"></a>A szabályzat alkalmazása

A házirend-objektum most elkészült, és a biztonsági mentési ütemtervtel, a megőrzési házirenddel és a fájlok befoglalásával/kizárásával kapcsolatos listával rendelkezik. Ezt a szabályzatot most már véglegesítheti Azure Backup használatra. Az újonnan létrehozott házirend alkalmazása előtt győződjön meg arról, hogy a [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) parancsmag használatával nincsenek társítva meglévő biztonsági mentési szabályzatok a kiszolgálóhoz. Ha eltávolítja a szabályzatot, a rendszer megerősítést kér. A megerősítés kihagyásához használja `-Confirm:$false` a jelzőt a parancsmaggal.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A házirend-objektum véglegesítése a [set-OBPolicy](https://technet.microsoft.com/library/hh770421) parancsmag használatával történik. A művelet megerősítést is kér. A megerősítés kihagyásához használja `-Confirm:$false` a jelzőt a parancsmaggal.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

A meglévő biztonsági mentési szabályzat részleteit a [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) parancsmaggal tekintheti meg. A [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) parancsmaggal tovább részletezheti a biztonsági mentési ütemtervet és a [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) parancsmagot az adatmegőrzési szabályzatokhoz

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-ad-hoc-backup"></a>Alkalmi biztonsági mentés végrehajtása

Miután beállította a biztonsági mentési szabályzatot, a biztonsági mentések ütemezés szerint történnek. Az ad hoc biztonsági mentés a [Start-OBBackup](https://technet.microsoft.com/library/hh770426) parancsmag használatával is lehetséges:

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Adatok visszaállítása Azure Backupról

Ez a szakasz végigvezeti a Azure Backup-ból származó adatok helyreállításának automatizálásához szükséges lépéseken. Ehhez a következő lépések szükségesek:

1. Válassza ki a forrás kötetet
2. Válassza ki a visszaállítani kívánt biztonsági mentési pontot
3. Válassza ki a visszaállítani kívánt elemet
4. A visszaállítási folyamat elindítása

### <a name="picking-the-source-volume"></a>A forrásoldali kötet kiválogatása

Egy elem Azure Backupból való visszaállításához először azonosítania kell az elem forrását. Mivel a parancsokat egy Windows-kiszolgáló vagy egy Windows-ügyfél kontextusában futtatjuk, a gép már azonosítva van. A forrás azonosításának következő lépése az azt tartalmazó kötet azonosítása. A számítógépről biztonsági mentés alatt álló kötetek vagy források listáját a [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) parancsmag végrehajtásával kérheti le. Ez a parancs az összes olyan forrás tömbjét adja vissza, amelyről biztonsági másolat készül a kiszolgálóról/ügyfélről.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>A visszaállítani kívánt biztonsági mentési pont kiválasztása

A biztonsági mentési pontok listáját a [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) parancsmag megfelelő paraméterekkel való végrehajtásával kérheti le. A példánkban a *D* kötethez tartozó legújabb biztonsági mentési pontot választjuk, és egy adott fájl helyreállításához használjuk.

```powershell
$Rps = Get-OBRecoverableItem -Source $Source[1]
```

```Output
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```

Az objektum `$Rps` a biztonsági mentési pontok tömbje. Az első elem a legutóbbi pont, az n-edik elem pedig a legrégebbi pont. A legutóbbi pont kiválasztásához a következőt `$Rps[0]`fogjuk használni:.

### <a name="choosing-an-item-to-restore"></a>A visszaállítani kívánt elem kiválasztása

A visszaállítani kívánt fájl vagy mappa azonosításához rekurzív módon használja a [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) parancsmagot. Így a mappa-hierarchia csak a `Get-OBRecoverableItem`használatával tallózható.

Ebben a példában, ha vissza szeretnénk állítani a *Pénzügy. xls* fájlt, hivatkozhatunk arra, hogy az objektumot `$FilesFolders[1]`használja.

```powershell
$FilesFolders = Get-OBRecoverableItem $Rps[0]
$FilesFolders
```

```Output
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
```

```powershell
$FilesFolders = Get-OBRecoverableItem $FilesFolders[0]
$FilesFolders
```

```Output
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

A ```Get-OBRecoverableItem``` parancsmag használatával is megkeresheti a visszaállítani kívánt elemeket. A példánkban a *Pénzügy. xls* fájlban a következő parancs futtatásával kaphatunk egy leírót a fájlhoz:

```powershell
$Item = Get-OBRecoverableItem -RecoveryPoint $Rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>A visszaállítási folyamat elindítása

A visszaállítási folyamat elindításához először meg kell adnia a helyreállítási beállításokat. Ezt a [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) parancsmag használatával teheti meg. Ebben a példában feltételezzük, hogy a fájlokat vissza szeretnénk állítani a *C:\Temp*-re. Tegyük fel, hogy a célmappában már létező fájlokat is szeretnénk kihagyni a *C:\Temp*. Ilyen helyreállítási lehetőség létrehozásához használja a következő parancsot:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Most aktiválja a visszaállítási folyamatot a következő `Get-OBRecoverableItem` parancsmag kimenetében kiválasztott `$Item` [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) paranccsal:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>A Azure Backup ügynök eltávolítása

A Azure Backup ügynök eltávolítása a következő paranccsal végezhető el:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Az ügynök bináris fájljainak számítógépről való eltávolítása néhány következménnyel jár:

* Eltávolítja a fájl-szűrőt a gépről, és leállítja a módosítások nyomon követését.
* A rendszer eltávolítja az összes házirend-információt a gépről, de a házirend-információkat továbbra is a szolgáltatás tárolja.
* Az összes biztonsági mentési ütemezés el lesz távolítva, és nem készül további biztonsági másolat.

Az Azure-ban tárolt adatok azonban továbbra is megmaradnak, és az adatmegőrzési szabályzat beállításának megfelelően megmaradnak. A régebbi pontok automatikusan elévülnek.

## <a name="remote-management"></a>Távfelügyelet

A Azure Backup ügynök, szabályzatok és adatforrások kezelése a PowerShell használatával távolról is elvégezhető. A távolról felügyelni kívánt gépet helyesen kell előkészíteni.

Alapértelmezés szerint a WinRM szolgáltatás kézi indításra van konfigurálva. Az indítási típust *automatikus* értékre kell beállítani, és a szolgáltatásnak elindítva kell lennie. Annak ellenőrzéséhez, hogy a WinRM szolgáltatás fut-e, az állapot tulajdonságnak *futnia*kell.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

A PowerShellt konfigurálni kell a távoli eljáráshívás szolgáltatáshoz.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

A gép mostantól távolról is felügyelhető az ügynök telepítésével. Az alábbi szkript például átmásolja az ügynököt a Távoli gépre, és telepíti azt.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>További lépések

További információ a Windows Server/Client Azure Backupról:

* [Az Azure Backup bemutatása](backup-introduction-to-azure-backup.md)
* [Windows-kiszolgálók biztonsági mentése](backup-configure-vault.md)
