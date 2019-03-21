---
title: Windows Server biztonsági mentése az Azure PowerShell használatával
description: Ismerje meg, hogyan helyezheti üzembe és felügyelheti az Azure Backup PowerShell-lel
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: pvrk
ms.openlocfilehash: 0a7a16a43b208bf2d14b86cd5cb23544ec03f9a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877530"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Az Azure-ba történő biztonsági mentés üzembe helyezése és kezelése Windows Server vagy Windows-ügyfél rendszereken a PowerShell-lel
Ez a cikk bemutatja, hogyan használható a PowerShell beállítása az Azure Backup a Windows Server vagy a Windows ügyfél és a biztonsági mentés és helyreállítás felügyelete.

## <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Első lépésként [a PowerShell legújabb kiadást telepíteni](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
A következő lépések végigvezetik Önt a Recovery Services-tároló létrehozása. Recovery Services-tároló nem egyezik egy biztonsági mentési tárolót.

1. Az Azure Backup használatakor az első alkalommal kell használnia a **Register-AzResourceProvider** parancsmagot, hogy regisztrálja az Azure Recovery Services-szolgáltatót az előfizetésében.

    ```
    PS C:\> Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. A Recovery Services-tároló egy ARM-erőforrás, ezért elhelyezi egy erőforráscsoporton belül kell. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Amikor egy új erőforráscsoportot hoz létre, adja meg a nevét és az erőforráscsoport helyét.  

    ```
    PS C:\> New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```
3. Használja a **New-AzRecoveryServicesVault** parancsmagot az új tároló létrehozásához. Mindenképpen adja meg a tároló ugyanazon a helyen, mint az erőforráscsoport.

    ```
    PS C:\> New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```
4. Adja meg a használandó; tárhely-redundancia típusát használhat [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) vagy [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md). Az alábbi példa bemutatja, hogy a - BackupStorageRedundancy beállítás a testVault GeoRedundant értékre van állítva.

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintheti az előfizetéshez
Használat **Get-AzRecoveryServicesVault** összes tárolók listájának megtekintéséhez az aktuális előfizetésben. Ezt a parancsot használhatja, ellenőrizze, hogy az új tároló létrejött, vagy tekintse meg, milyen tárolók érhetők el az előfizetésben.

Futtassa a parancsot, **Get-AzRecoveryServicesVault**, és az előfizetés összes tárolók fel vannak sorolva.

```
PS C:\> Get-AzRecoveryServicesVault
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
Az Azure Backup ügynök telepítése előtt kell a telepítő letöltött és a jelen van a Windows Server rendszeren. Kérheti, hogy a telepítőt a legújabb verzióját a [Microsoft Download Center](https://aka.ms/azurebackup_agent) vagy a Recovery Services-tároló irányítópult-oldalon. A telepítő például könnyen elérhető helyre mentse * C:\Downloads\*.

Másik megoldásként a PowerShell használatával a letöltési segédprogramja lekérése:
 
 ```
 $MarsAURL = 'Http://Aka.Ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Az ügynök telepítéséhez futtassa a következő parancsot egy rendszergazda jogú PowerShell-konzolon:

```
PS C:\> MARSAgentInstaller.exe /q
```

Ez telepíti az ügynököt az összes alapértelmezett beállítást. A telepítés néhány percet vesz igénybe a háttérben. Ha nem adja meg a */nu* lehetőséget, majd a **Windows Update** ablak nyílik meg minden olyan frissítéseket keresni a telepítés végén. A telepítést követően az ügynököt a telepített programok listájában jelennek meg.

A telepített programok listájának megtekintéséhez, keresse fel a **Vezérlőpult** > **programok** > **programok és szolgáltatások**.

![Ügynök telepítve](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Telepítési beállítások
Az összes rendelkezésre álló parancssori felületen beállítások megtekintéséhez használja a következő parancsot:

```
PS C:\> MARSAgentInstaller.exe /?
```

Az elérhető lehetőségek a következők:

| Beállítás | Részletek | Alapértelmezett |
| --- | --- | --- |
| /q |Csendes telepítés |- |
| p: "hely" |Az Azure Backup-ügynök a telepítési mappa elérési útját. |C:\Program Files\Microsoft Azure Recovery Services-ügynök |
| / "hely" s: |Az Azure Backup ügynököt a gyorsítótár mappájának elérési útja. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Jóváhagyja a Microsoft Update |- |
| /nu |Nem keres frissítéseket telepítésének befejezése után |- |
| /nap |A Microsoft Azure Recovery Services Agent eltávolítása |- |
| /pH |Proxy Host Address |- |
| /po |Gazdagép proxyport száma |- |
| /pu |Proxy állomás felhasználónév |- |
| /pW |Proxy jelszava |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>A Windows Server vagy a Windows ügyfélszámítógépen a Recovery Services-tárolóra regisztrálása
A Recovery Services-tároló létrehozása után töltse le a legújabb ügynököt és a tároló hitelesítő adatait, és egy kényelmes helyen, például C:\Downloads tárolja.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
```

A Windows Server vagy Windows-ügyfélgép, futtassa a [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) parancsmag regisztrálni a gépet a tárolóval.
Ez, és más parancsmagok, a biztonsági, az MSONLINE modul, amely a telepítési folyamat részeként hozzáadja a Mars AgentInstaller származnak. 

Az ügynök telepítőjének nem frissíti a $Env: PSModulePath változó. Ez azt jelenti, hogy modul automatikus – betöltése sikertelen. A probléma megoldásához a következőket teheti:

```
PS C:\>  $Env:psmodulepath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Azt is megteheti manuálisan betöltheti a modult a szkript a következő:

```
PS C:\>  Import-Module  'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'

```

Miután betöltötte az Online biztonsági mentést készítő parancsmagok, regisztrálja a tároló hitelesítő adatait:


```

PS C:\> Start-OBRegistration -VaultCredentials $credsfilename.FilePath -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Adja meg a tároló hitelesítőadat-fájlja ne használjon relatív elérési utakat. A parancsmag bemeneteként abszolút elérési útnak meg kell adni.
>
>

## <a name="networking-settings"></a>Hálózati beállítások
Ha a kapcsolat a Windows-gép az interneten egy proxykiszolgálón keresztül, a proxykiszolgáló beállításait is megadható az ügynököt. Ebben a példában nincs nincs proxykiszolgáló, hogy explicit módon vannak törlésével bármely proxy kapcsolatos információkat.

Lehetőségét is szabályozható a sávszélesség-használat ```work hour bandwidth``` és ```non-work hour bandwidth``` az adott, a hét napjait.

Beállítás a proxy- és sávszélesség részletei történik használatával a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) parancsmagot:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Titkosítási beállítások
Az Azure Backup biztonsági mentési adatforgalom titkosított a bizalmas adatok védelme érdekében. A titkosítási jelszó a "password" a visszaállítás időpontjában az adatok visszafejtéséhez.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
PS C:\> $PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force 
PS C:\> $PassCode   = 'AzureR0ckx'
PS C:\> Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
Server properties updated successfully
```

> [!IMPORTANT]
> Biztosíthatja a hozzáférési kódot adatok védelméről be van állítva. Az Azure-ból adatokat anélkül, hogy ezt a jelszót nem állítható vissza.
>
>

## <a name="back-up-files-and-folders"></a>Fájlok és mappák biztonsági mentése
Minden biztonsági mentés Windows-kiszolgálók és ügyfelek számára az Azure Backup házirend vonatkoznak rájuk. A házirend három részből áll:

1. A **biztonsági mentési ütemezés** , amely meghatározza, mikor biztonsági mentések kell venni és a szolgáltatás szinkronizálva.
2. A **adatmegőrzési ütemterv** , amely megadja, hogy mennyi ideig megőrizni a helyreállítási pontok az Azure-ban.
3. A **belefoglalási/kizárási megadására** , amely előírja, hogy mit kell készíteni.

Ebben a dokumentumban azt Ön automatizálása a biztonsági mentés, mivel fog feltételezzük semmi nem lett konfigurálva. Hozzon létre egy új biztonsági mentési szabályzat az első lépések a [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) parancsmagot.

```
PS C:\> $newpolicy = New-OBPolicy
```

Jelenleg a szabályzat üres, és más parancsmagok a következők szükséges meghatározásához, hogy mi lesz az elemek befoglalt, sem a kizárt, amikor biztonsági mentések fog futni, és ha a biztonsági másolatokat tárolni.

### <a name="configuring-the-backup-schedule"></a>A biztonsági mentési ütemezés konfigurálása
Az első 3 részből álló szabályzat a biztonsági mentési ütemezés, amely használatával hozható létre a [New-OBSchedule](https://technet.microsoft.com/library/hh770401) parancsmagot. A biztonsági mentés ütemezése határozza meg, ha a biztonsági mentéseket kell elvégezni. Meg kell adnia a bemeneti paraméterek 2 ütemezés létrehozásakor:

* **A hét napjait** , amelyeket a biztonsági mentést futtatni. A biztonsági mentési feladat egy napon, vagy a hét minden napján, vagy a köztes bármelyikét futtathatja.
* **Napszakokban** a biztonsági mentést kell futtatásakor. Megadhatja, hogy akár 3 különböző időpontokban, a nap, amikor a biztonsági mentés akkor aktiválódik.

Beállíthat például, hogy egy biztonsági mentési szabályzatot, amely du. 4: futtat minden szombat és vasárnap.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

A biztonsági mentési ütemezés kell lennie a szabályzathoz társított, és ez használatával érhető el a [Set-OBSchedule](https://technet.microsoft.com/library/hh770407) parancsmagot.

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>A megőrzési házirend konfigurálása
A megőrzési házirend határozza meg, mennyi ideig maradnak a biztonsági mentési feladatok a létrehozott helyreállítási pontokat. Egy új megtartási házirendet a létrehozásakor a [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) parancsmaggal, megadhatja az, hogy hány napig őrzi meg az Azure Backup kell a biztonsági mentések helyreállítási pontjait. Az alábbi példa egy 7 napos adatmegőrzési állítja be.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

Az adatmegőrzési szabályt hozzá kell rendelni a fő házirend-parancsmag segítségével [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

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
### <a name="including-and-excluding-files-to-be-backed-up"></a>Belefoglalásához és kizárásához fájlok biztonsági mentése
Egy ```OBFileSpec``` objektuma határozza meg a fájlokat és a biztonsági mentést nem lehet. Ez a szabályok, amelyek ki a védett fájlokat és mappákat egy gépen hatókörét. Akkor is, számos befoglalási vagy kizárási szabály szükség szerint fájlt, és társítsa a szabályzatot. Új OBFileSpec objektum létrehozásakor a következőket teheti:

* Adja meg a fájlok és mappák része
* Adja meg a fájlokat és mappákat, amelyet ki szeretne
* Adjon meg egy mappában (vagy) hogy a megadott mappában csak a legfelső szintű fájlokat kell készíteni az adatokról biztonsági másolatot készíteni a rekurzív fel.

Ez utóbbi használatával érhető el a nem rekurzív - jelző a New-OBFileSpec parancsban.

Az alábbi példában azt fog készíteni a kötet C: és D: és az operációs rendszer bináris fájljainak a Windows mappában található ideiglenes mappák kizárása. Ehhez létrehozunk két fájlt specifikációk segítségével a [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) parancsmag - belefoglalási és kizárási egyet. Létrehozása után a fájl leírások, azok a szabályzat használatához a [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) parancsmagot.

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

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


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

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
A csoportházirend-objektum már befejeződött, és egy társított biztonsági mentési ütemezés, a megőrzési házirend és a fájlok belefoglalási/kizárási lista. Ez a szabályzat most már lehet véglegesíteni az Azure Backup használatához. Alkalmazása előtt az újonnan létrehozott házirend biztosítása érdekében, hogy nincsenek-e a kiszolgálóhoz társított használatával meglévő biztonsági mentési házirendek a [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) parancsmagot. A házirend eltávolítása megerősítő fogja kérni. Kihagyandó megerősítő használatát a ```-Confirm:$false``` jelzőt mellékel a parancsmagot.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A csoportházirend-objektum véglegesítése történik használatával a [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) parancsmagot. Ez is kér megerősítést. Kihagyandó megerősítő használatát a ```-Confirm:$false``` jelzőt mellékel a parancsmagot.

```
PS C:> Set-OBPolicy -Policy $newpolicy
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

A meglévő biztonsági mentési házirend használatával részleteit is megtekintheti a [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) parancsmagot. Részletezésével további használatával a [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) parancsmag a biztonsági mentési ütemezés és a [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) parancsmag az adatmegőrzési házirendek

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
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

### <a name="performing-an-ad-hoc-backup"></a>Egy ad-hoc biztonsági mentés
Miután a biztonsági mentési szabályzat van beállítva. a biztonsági másolatok az ütemezés szerint fog előfordulni. Egy ad-hoc biztonsági mentésének elindítása az is lehetséges használatával a [Start-OBBackup](https://technet.microsoft.com/library/hh770426) parancsmagot:

```
PS C:> Get-OBPolicy | Start-OBBackup
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

## <a name="restore-data-from-azure-backup"></a>Adatok visszaállítása az Azure Backup
Ez a szakasz végigvezeti a lépések automatizálásához adatok helyreállítását az Azure Backup. Ez az alábbi lépésekből áll:

1. Válassza ki az adatforrás kötetét
2. Válassza ki egy biztonsági mentési ponthoz
3. Válassza ki az elemek visszaállítása
4. A visszaállítási folyamat aktiválása

### <a name="picking-the-source-volume"></a>Az adatforrás kötetét kiadási
Annak érdekében, hogy egy elem a az Azure biztonsági másolatból, először azonosítania kell az elem forrása. Mivel azt még végrehajtása a parancsokat egy Windows Server vagy a Windows ügyfél környezetében, a gép már azonosítja. A következő lépése a forrás azonosítása, hogy azonosítsa az azt tartalmazó kötet. Kötetek vagy úgy, hogy végrehajtja ezt a gépet a biztonsági mentés alatt álló kérhető adatforrások listáját a [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) parancsmagot. Ezzel a paranccsal az összes adatforrás biztonsági másolatot készített a kiszolgáló vagy ügyfél tömbjét adja vissza.

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>A visszaállításhoz egy biztonsági mentési pont kiválasztása
Kérje le a biztonsági mentési pontok listájának végrehajtásával a [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) parancsmagot a megfelelő paraméterekkel. Ebben a példában azt fogja választani az adatforrás kötetét a legújabb biztonsági mentési pontok *D:* , amellyel egy adott fájl helyreállításához.

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
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
Az objektum ```$rps``` biztonsági mentési pontok tömbje. Az első elemét a legutóbbi időpontra, az n-edik elemének pedig a legrégebbi pont. Válassza ki a legutóbbi pontnak, hogy ezzel ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Az elemek visszaállítása kiválasztása
Azonosíthatja a pontos fájl vagy mappa visszaállításához rekurzív módon használja a [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) parancsmagot. Ezzel a módszerrel a mappahierarchiában tallózható kizárólag használatával a ```Get-OBRecoverableItem```.

Ebben a példában, ha a fájl visszaállítása *finances.xls* is hivatkozik, amely az objektum ```$filesFolders[1]```.

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
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

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
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

Elemek segítségével történő visszaállításhoz is kereshet a ```Get-OBRecoverableItem``` parancsmagot. Ebben a példában kereséséhez *finances.xls* azt sikerült egy leírót szerezni a fájlon Ez a parancs futtatásával:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>A visszaállítási folyamat elindítása
A visszaállítási folyamat indításához, először létre kell a helyreállítási beállítások megadása. Az ehhez a [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) parancsmagot. Ebben a példában feltételezzük, hogy a fájlok visszaállítása szeretnénk *C:\temp*. Azt is feltételezzük, hogy kívánja-e már megtalálható fájlok kihagyása a rendeltetési mappára *C:\temp*. Hozzon létre egy helyreállítási lehetőség, használja a következő parancsot:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Aktiválja a visszaállítási folyamat használatával a [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) parancsot a kiválasztott ```$item``` kimenetéből származó a ```Get-OBRecoverableItem``` parancsmagot:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Az Azure Backup-ügynök eltávolítása
Az Azure Backup ügynök eltávolításának hajtható végre a következő paranccsal:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

Az ügynök bináris fájljainak eltávolítása a gépről következménnyel néhány kell figyelembe venni:

* A fájl-szűrő eltávolítja a számítógépről, és a változások követését le van állítva.
* Minden törlődik a számítógépről, de továbbra is a service szolgáltatásban tárolni kívánt házirend-információit.
* A mentési ütemezések el lesznek távolítva, és nincs további biztonsági másolatokat készít.

Azonban az adatok Azure marad tárolja, és megőrzi a megőrzési házirend-beállításnak. Régebbi pontok automatikusan vannak elavult.

## <a name="remote-management"></a>Távfelügyelet
Az Azure Backup ügynök, a házirendek és az adatforrások körül a felügyeleti távolról Powershellen keresztül is elvégezhető. A távoli felügyelt gépnél megfelelően elő kell készíteni.

A WinRM szolgáltatás alapértelmezés szerint kézi indítási van konfigurálva. Az indítási típusát állítsa *automatikus* és el kell indítani a szolgáltatást. Annak ellenőrzéséhez, hogy a WinRM szolgáltatás fut, az állapot tulajdonság értéke legyen *futó*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell távoli eljáráshívás kell konfigurálni.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

A gép mostantól kezelhető távolról - kezdve az ügynök telepítése. A következő parancsfájl például másolja át az ügynököt a távoli számítógépre, és telepíti azt.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>További lépések
További információ az Azure biztonsági mentés a Windows Server vagy Windows-ügyfél lásd:

* [Az Azure Backup bemutatása](backup-introduction-to-azure-backup.md)
* [Windows-kiszolgálók biztonsági mentése](backup-configure-vault.md)
