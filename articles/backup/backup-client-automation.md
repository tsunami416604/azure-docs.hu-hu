---
title: A PowerShell használata a Windows Server azure-ra való biztonsági ellátásához
description: Ebből a cikkből megtudhatja, hogy miként használhatja a PowerShellt az Azure Backup windows Server vagy Windows-ügyfélrendszeren történő beállításához, valamint a biztonsági mentés és helyreállítás kezeléséhez.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: efe0b93fe1e37990422ffbd2256e38c12401dca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673201"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Az Azure-ba történő biztonsági mentés üzembe helyezése és kezelése Windows Server vagy Windows-ügyfél rendszereken a PowerShell-lel

Ez a cikk bemutatja, hogyan használhatja a PowerShellt az Azure Backup windows Server vagy Windows-ügyfélen történő beállításához, valamint a biztonsági mentés és helyreállítás kezeléséhez.

## <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Első lépésekhez [telepítse a legújabb PowerShell-kiadást.](/powershell/azure/install-az-ps)

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések segítségével hozzon létre egy Recovery Services-tároló. A Recovery Services-tároló eltér a biztonsági mentési tároló.

1. Ha első alkalommal használja az Azure Backup szolgáltatást, a **Register-AzResourceProvider** parancsmaggal kell regisztrálnia az Azure Recovery Service szolgáltatót az előfizetéssel.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. A Recovery Services-tároló egy ARM-erőforrás, ezért egy erőforráscsoporton belül kell elhelyeznie. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat. Új erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Az új tároló létrehozásához használja a **New-AzRecoveryServicesVault** parancsmag. Ügyeljen arra, hogy adja meg ugyanazt a helyet a tároló, mint az erőforráscsoport használt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Adja meg a használni kívánt tárolási redundancia típusát; [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) vagy [georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md)használható. A következő példa bemutatja a -BackupStorageRedundancy beállítás testVault van beállítva GeoRedundant.

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintése egy előfizetésben

A **Get-AzRecoveryServicesVault** segítségével tekintse meg az aktuális előfizetés összes tárolójának listáját. Ezzel a paranccsal ellenőrizheti, hogy új tárolót hoztak-e létre, vagy megtekintheti, hogy milyen tárolók érhetők el az előfizetésben.

Futtassa a **Get-AzRecoveryServicesVault**parancsot, és az előfizetés összes tárolója megjelenik a listában.

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

## <a name="installing-the-azure-backup-agent"></a>Az Azure Backup ügynök telepítése

Az Azure Backup-ügynök telepítése előtt le kell töltenie a telepítőt, és meg kell jelenjen a Windows Serveren. A telepítő legújabb verzióját a Microsoft [letöltőközpontjából](https://aka.ms/azurebackup_agent) vagy a Helyreállítási szolgáltatások tároló irányítópultja lapjáról szerezheti be. Mentse a telepítőt egy könnyen elérhető helyre, például *C:\Letöltések\*.

Másik lehetőségként használja a PowerShellt a letöltő betöltőjének betöltőjéhez:

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

Ezzel telepíti az ügynököt az összes alapértelmezett beállítással. A telepítés néhány percet vesz igénybe a háttérben. Ha nem adja meg a */nu* kapcsolót, akkor a **Windows Update** ablak a telepítés végén megnyílik, hogy ellenőrizze a frissítéseket. A telepítés után az ügynök megjelenik a telepített programok listájában.

A telepített programok listájának megtekintéséhez nyissa meg a **Vezérlőpult** > **programok** > **és szolgáltatások segédprogramját.**

![Ügynök telepítve](./media/backup-client-automation/installed-agent-listing.png)

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

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Windows Server vagy Windows ügyfélgép regisztrálása helyreállítási szolgáltatások tárolójára

Miután létrehozta a Recovery Services-tárolót, töltse le a legújabb ügynököt és a tároló hitelesítő adatait, és tárolja azt egy kényelmes helyen, például a C:\Letöltések.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Regisztráció a PS Az modul használatával

> [!NOTE]
> A vault tanúsítvány generálásával rendelkező hibát az Az 3.5.0-s kiadás rögzíti. Az Az 3.5.0-s vagy újabb verzió használatával letöltheti a tárolótanúsítványt.

A PowerShell legújabb Az moduljában az alapul szolgáló platformkorlátozások miatt a tároló hitelesítő adatainak letöltéséhez önaláírt tanúsítványszükséges. A következő példa bemutatja, hogyan adhat meg önaláírt tanúsítványt, és töltse le a tároló hitelesítő adatait.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

A Windows Server vagy windows ügyfélgépen futtassa a [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) parancsmabparancs segítségével regisztrálja a gépet a tárolóval.
Ez és a biztonsági mentéshez használt egyéb parancsmagok az MSONLINE modulból származnak, amelyet a Mars AgentInstaller a telepítési folyamat részeként hozzáadott.

Az ügynöktelepítő nem frissíti a $Env:PSModulePath változót. Ez azt jelenti, hogy a modul automatikus betöltése sikertelen. A probléma megoldásához tegye a következőket:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Azt is megteheti, hogy manuálisan betölti a modult a parancsfájlba az alábbiak szerint:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Miután betölti az online biztonsági mentési parancsmagokat, regisztrálja a tároló hitelesítő adatait:

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
> Ne használjon relatív elérési utakat a tároló hitelesítő fájljának megadásához. A parancsmag bemeneteként abszolút elérési utat kell megadnia.
>
>

## <a name="networking-settings"></a>Hálózati beállítások

Ha a Windows-gép internetkapcsolata proxykiszolgálón keresztül történik, a proxybeállítások az ügynök számára is megadhatók. Ebben a példában nincs proxykiszolgáló, ezért kifejezetten törli a proxyval kapcsolatos információkat.

Sávszélesség-használat is vezérelhető a `work hour bandwidth` `non-work hour bandwidth` lehetőségeket, és egy adott sor a hét.

A proxy és a sávszélesség részleteinek beállítása a [Set-OBMachineSetting](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obmachinesetting?view=winserver2012-ps) parancsmag használatával történik:

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

Az Azure Backup-ba küldött biztonsági mentési adatok titkosítva vannak az adatok titkosságának védelme érdekében. A titkosítási jelszó az adatok visszafejtésének "jelszava".

Létre kell hoznia egy biztonsági tűt a **Létrehozás**lehetőséget, **az** > Azure Portal **Helyreállítási szolgáltatások tárolójában** található Beállítások**tulajdonságai** > **biztonsági PIN-kód** alatt. Ezután használja ezt `generatedPIN` a parancsot:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> A jelszó adatainak biztonsága és biztonsága a beállítás után. Ez a jelszó nélkül nem lehet visszaállítani az azure-beli adatokat.
>
>

## <a name="back-up-files-and-folders"></a>Fájlok és mappák biztonsági mentése

A Windows-kiszolgálók és az ügyfelek és az Azure Backup összes biztonsági mentését egy szabályzat szabályozza. A szabályzat három részből áll:

1. Biztonsági **mentési ütemezés,** amely meghatározza, hogy mikor kell biztonsági másolatokat készíteni és szinkronizálni a szolgáltatással.
2. Megőrzési **ütemezés,** amely meghatározza, hogy mennyi ideig kell megőrizni a helyreállítási pontokat az Azure-ban.
3. **Fájlfelvétel/kizárási specifikáció,** amely meghatározza, hogy miről kell biztonsági másolatot kapni.

Ebben a dokumentumban, mivel a biztonsági mentés automatizálása, feltételezzük, hogy semmi sem van konfigurálva. Először hozzon létre egy új biztonsági mentési szabályzatot a [New-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obpolicy?view=winserver2012-ps) parancsmag használatával.

```powershell
$NewPolicy = New-OBPolicy
```

Ebben az időben a házirend üres, és más parancsmagok szükségesek annak meghatározásához, hogy milyen elemeket fog tartalmazni vagy kizárni, mikor futnak a biztonsági mentések, és hol lesznek tárolva a biztonsági mentések.

### <a name="configuring-the-backup-schedule"></a>A biztonsági mentés ütemezésének konfigurálása

A házirend három része közül az első a biztonsági mentésütemezés, amely a [New-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obschedule?view=winserver2012-ps) parancsmag használatával jön létre. A biztonsági mentés ütemezése határozza meg, hogy mikor kell biztonsági mentést készíteni. Ütemezés létrehozásakor két bemeneti paramétert kell megadnia:

* **A hét azon napjai,** amikor a biztonsági mentésnek futnia kell. Futtathatja a biztonsági mentési feladatot csak egy napon, vagy a hét minden napján, vagy bármilyen kombináció között.
* **A nap azon időszakai,** amikor a biztonsági mentésnek futnia kell. Legfeljebb három különböző napszakban, amikor a biztonsági mentés aktiválódik.

Például konfigurálhat egy biztonsági mentési házirendet, amely szombaton és vasárnap 16:00 óráig fut.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

A biztonsági mentésütemezést egy házirendhez kell társozni, és ez a [Set-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obschedule?view=winserver2012-ps) parancsmag használatával érhető el.

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Adatmegőrzési szabály konfigurálása

Az adatmegőrzési szabály határozza meg, hogy a biztonsági mentési feladatokból létrehozott helyreállítási pontok mennyi ideig őrződnek meg. Új adatmegőrzési házirend létrehozásakor a [New-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obretentionpolicy?view=winserver2012-ps) parancsmag használatával megadhatja, hogy a biztonsági mentési helyreállítási pontokat meg kell őrizni az Azure Backup használatával. Az alábbi példa hét napos adatmegőrzési szabályt állít be.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Az adatmegőrzési házirendet a [Set-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obretentionpolicy?view=winserver2012-ps)parancsmag használatával kell társtársosan kezelni a fő házirendhez:

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

### <a name="including-and-excluding-files-to-be-backed-up"></a>A biztonsági másolatot tartalmazó fájlok kivételével és kizárásával

Az `OBFileSpec` objektum határozza meg a biztonsági mentésben szerepeltetni és kizárni a fájlokat. Ez olyan szabálykészlet, amely kifedi a védett fájlokat és mappákat a számítógépen. Annyi fájlfelvételi vagy kizárási szabályt hozhat, amennyi szükséges, és társíthatja őket egy házirendhez. Új OBFileSpec objektum létrehozásakor a következőkre van lehetőség:

* A befoglalandó fájlok és mappák megadása
* A kizárandó fájlok és mappák megadása
* Adja meg a mappában lévő adatok rekurzív biztonsági másolatát (vagy azt, hogy csak a megadott mappában lévő legfelső szintű fájlokról kell-e biztonsági másolatot készíteni.

Ez utóbbi a New-OBFileSpec parancs -NonRecursive jelzőjével érhető el.

Az alábbi példában biztonsági másolatot készítünk a C: és a D kötetről, és kizárjuk az operációs rendszer bináris fájljait a Windows mappában és az ideiglenes mappákban. Ehhez két fájlspecifikációt hozunk létre a [New-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obfilespec?view=winserver2012-ps) parancsmag használatával - egyet a felvételhez és egyet a kizáráshoz. A fájlspecifikációk létrehozása után az [Add-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/add-obfilespec?view=winserver2012-ps) parancsmag használatával lesznek társítva a házirendhez.

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

### <a name="applying-the-policy"></a>A házirend alkalmazása

Most a házirend objektum befejeződött, és egy kapcsolódó biztonsági mentési ütemezés, adatmegőrzési házirend, és egy felvétel / kizárás fájlok listáját. Ez a szabályzat most már véglegesíthető az Azure Backup használatához. Az újonnan létrehozott házirend alkalmazása előtt győződjön meg arról, hogy nincsenek a kiszolgálóhoz társított biztonsági mentési házirendek az [Eltávolítás-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/remove-obpolicy?view=winserver2012-ps) parancsmag használatával. A házirend eltávolítása megerősítést kér. A megerősítés kihagyásához használja a `-Confirm:$false` jelzőt a parancsmaggal.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A házirend-objektum véglegesítése a [Set-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obpolicy?view=winserver2012-ps) parancsmag használatával történik. Ez is megerősítést kér. A megerősítés kihagyásához használja a `-Confirm:$false` jelzőt a parancsmaggal.

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

Megtekintheti a meglévő biztonsági mentési szabályzat részleteit a [Get-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obpolicy?view=winserver2012-ps) parancsmag használatával. A [get-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obschedule?view=winserver2012-ps) parancsmag további leáshatja a biztonsági mentési ütemezés és a [Get-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obretentionpolicy?view=winserver2012-ps) parancsmag az adatmegőrzési házirendek

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

### <a name="performing-an-on-demand-backup"></a>Igény szerinti biztonsági mentés végrehajtása

A biztonsági mentési házirend beállítása után a biztonsági mentések ütemezés szerint történnek. Igény szerinti biztonsági mentés indítása a [Start-OBBackup](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obbackup?view=winserver2012-ps) parancsmag használatával is lehetséges:

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

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>A Windows Server rendszerállapotának biztonsági és biztonsági másolatot kell fonni a MABS-ügynökben

Ez a szakasz a PowerShell-paranccsal a Rendszerállapot MABS-ügynökben való beállításához

### <a name="schedule"></a>Ütemezés

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Megőrzés

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Ütemezés és megőrzés konfigurálása

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>A házirend ellenőrzése

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Adatok visszaállítása az Azure Backup ból

Ez a szakasz végigvezeti az Azure Backupból történő helyreállítás automatizálásának lépéseit. Ez a következő lépéseket foglalja magában:

1. A forráskötet kiválasztása
2. A visszaállítani kívánt biztonsági mentési pont kiválasztása
3. Visszaállítandó elem megadása
4. A visszaállítási folyamat aktiválása

### <a name="picking-the-source-volume"></a>A forráskötet kiválasztása

Annak érdekében, hogy egy elemet az Azure Backup, először azonosítania kell az elem forrását. Mivel a parancsokat egy Windows Server vagy egy Windows-ügyfél környezetében hajtjuk végre, a gép már azonosítható. A forrás azonosításának következő lépése az azt tartalmazó kötet azonosítása. A gépről biztonsági másolatot vagy forrásoklistájáról a [Get-OBRecoverableSource](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverablesource?view=winserver2012-ps) parancsmag végrehajtásával lehet lekérni. Ez a parancs a kiszolgálóról/ügyfélről biztonsági másolatot készítő összes forrás tömbjét adja vissza.

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

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Biztonsági másolat kiválasztása, ahonnan vissza szeretné állítani

A biztonsági másolat pontok listáját a [Get-OBRecoverableItem](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverableitem?view=winserver2012-ps) parancsmag megfelelő paraméterekkel történő végrehajtásával szerezheti be. A példában kiválasztjuk a C forráskötet legújabb biztonsági mentési *pontját,* és egy adott fájl helyreállításához használjuk.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

Az `$Rps` objektum biztonsági mentési pontok tömbje. Az első elem a legújabb pont, az N-edik elem pedig a legrégebbi pont. A legfrissebb pont kiválasztásához `$Rps[0]`a .

### <a name="specifying-an-item-to-restore"></a>Visszaállítandó elem megadása

Adott fájl visszaállításához adja meg a fájl nevét a gyökérkötethez viszonyítva. Ha például a C:\Test\Cat.job fájlbe szeretné beolvasni, hajtsa végre a következő parancsot.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>A visszaállítási folyamat aktiválása

A visszaállítási folyamat elindításához először meg kell adnunk a helyreállítási lehetőségeket. Ezt a [New-OBRecoveryOption](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obrecoveryoption?view=winserver2012-ps) parancsmag használatával teheti meg. Ebben a példában tegyük fel, hogy vissza szeretnénk állítani a fájlokat *C:\temp értékre.* Tegyük fel azt is, hogy ki szeretnénk hagyni a *C:\temp*célmappában már létező fájlokat. Ilyen helyreállítási lehetőség létrehozásához használja a következő parancsot:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Most indítsa el a visszaállítási folyamatot a [Start-OBRecovery](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obrecovery?view=winserver2012-ps) `Get-OBRecoverableItem` paranccsal a parancsmag kimenetéről kiválasztott `$Item` on:

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

## <a name="uninstalling-the-azure-backup-agent"></a>Az Azure Backup ügynök eltávolítása

Az Azure Backup-ügynök eltávolítása a következő paranccsal végezhető el:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Az ügynök bináris fájljának eltávolítása a gépről néhány figyelembe vemandó következménnyel jár:

* Eltávolítja a fájlszűrőt a gépről, és leállítja a változások nyomon követését.
* Minden házirend-információ törlődik a gépről, de a házirend-információk továbbra is a szolgáltatásban tárolódnak.
* Az összes biztonsági mentési ütemezés törlődik, és nem kerül sor további biztonsági mentésre.

Az Azure-ban tárolt adatok azonban megmaradnak, és az Ön által beállított adatmegőrzési házirendnek megegyeznek. A régebbi pontok automatikusan kiesnek.

## <a name="remote-management"></a>Távfelügyelet

Az Azure Backup-ügynök, szabályzatok és adatforrások körüli felügyelet távolról is elvégezhető a PowerShellen keresztül. A távolról felügyelt gépet megfelelően kell elkészíteni.

Alapértelmezés szerint a WinRM szolgáltatás manuális indításra van konfigurálva. Az indítási típust automatikusra kell *állítani,* és a szolgáltatást el kell indítani. A Rendszerfelügyeleti webszolgáltatások szolgáltatás futásának ellenőrzéséhez a Status tulajdonság értékének *futnia*kell.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

A PowerShellt úgy kell konfigurálni, hogy a rendszer előre meghívja.

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

A gép most már távolról is kezelhető - az ügynök telepítésétől kezdve. A következő parancsfájl például átmásolja az ügynököt a távoli számítógépre, és telepíti azt.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>További lépések

További információ az Azure Backup for Windows Server/Client rendszerről:

* [Az Azure Backup bemutatása](backup-introduction-to-azure-backup.md)
* [Windows-kiszolgálók biztonsági és biztonsági másolatot készül](backup-windows-with-mars-agent.md)
