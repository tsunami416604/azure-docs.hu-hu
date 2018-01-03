---
title: "Windows Server mentésére az Azure PowerShell használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan telepíthetnek és kezelhetnek az Azure Backup szolgáltatáshoz a PowerShell használatával"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 65218095-2996-44d9-917b-8c84fc9ac415
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: saurse;markgal;jimpark;nkolli;trinadhk
ms.openlocfilehash: 5a7189d9ccc8ab7aee61cd32e465b2c9b63680d2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Az Azure-ba történő biztonsági mentés üzembe helyezése és kezelése Windows Server vagy Windows-ügyfél rendszereken a PowerShell-lel
Ez a cikk bemutatja, hogyan használja a Powershellt beállítása az Azure Backup szolgáltatás a Windows Server vagy egy Windows ügyfél és a biztonsági mentés és helyreállítás felügyelete.

## <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Ez a cikk az Azure Resource Managerrel (ARM) és a MS Online biztonsági mentés PowerShell-parancsmagokat, amelyek lehetővé teszik egy erőforráscsoportot a Recovery Services-tároló használatára szolgál.

2015 október, az Azure PowerShell 1.0-s verziójában jelent meg. Ebben a kiadásban a 0.9.8-as sikeres szabadítsa fel, és néhány jelentős módosításokat, különösen a parancsmagok elnevezési mintát állapotba. Az 1.0-ás parancsmagok az {ige}-AzureRm{főnév} elnevezési mintát használják; a 0.9.8-as nevek viszont nem tartalmazzák az **Rm** tagot (például New-AzureRmResourceGroup New-AzureResourceGroup helyett). Ha az Azure PowerShell 0.9.8-as verzióját használja, először engedélyeznie kell az erőforrás-kezelői módot a **Switch-AzureMode AzureResourceManager** parancs futtatásával. Ez a parancs nincs szükség az 1.0-s vagy újabb.

Ha a parancsfájlok a 0.9.8-as írt használni kívánt környezet, 1.0-s vagy újabb környezetben kell gondosan frissíti, és a parancsfájlok tesztelése egy üzem előtti környezetben váratlan hatás elkerülése érdekében az éles használat előtt.

[Töltse le a legfrissebb verzióját a PowerShell](https://github.com/Azure/azure-powershell/releases) (szükséges minimális verziója: 1.0.0)

[!INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
A következő lépések alapján a Recovery Services-tároló létrehozása. Recovery Services-tároló nem egyezik egy biztonsági mentési tárolót.

1. Ha az Azure biztonsági mentés először használ, kell használnia a **Register-AzureRMResourceProvider** parancsmag futtatásával regisztrálja az Azure Recovery szolgáltató az előfizetéshez.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. A Recovery Services-tároló egy ARM-erőforrás, ezért el kell helyezni az erőforráscsoporton belül. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Új erőforráscsoport létrehozása esetén adja meg a nevét és helyét, ahhoz az erőforráscsoporthoz.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "WestUS"
    ```
3. Használja a **New-AzureRmRecoveryServicesVault** parancsmaggal hozhat létre az új tárolóba. Ne felejtse el ugyanazon a helyen, a tároló adja meg, mint az erőforráscsoport használt.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```
4. Megadhatja a használandó; adattároló redundanciája, amely használhat [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) vagy [földrajzi redundáns tárolás (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). A következő példa bemutatja a - BackupStorageRedundancy beállítás a testVault GeoRedundant értékre van állítva.

   > [!TIP]
   > Sok Azure biztonsági mentést készítő parancsmagok bemeneti adatokként a Recovery Services-tároló objektum szükséges. Emiatt célszerű a Recovery Services biztonsági másolat tároló objektum tárolható egy változóban.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók előfizetés megtekintése
Használjon **Get-AzureRmRecoveryServicesVault** megtekintéséhez az összes tárolók listája az aktuális előfizetésben. Ezt a parancsot használhatja, ellenőrizze, hogy létrejött-e egy új tárolót, vagy, hogy milyen tárolók-előfizetésben elérhető.

Futtassa a parancsot, **Get-AzureRmRecoveryServicesVault**, és az előfizetés összes tárolók vannak felsorolva.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent"></a>Az Azure Backup szolgáltatás ügynökének telepítése
Az Azure Backup szolgáltatás ügynökének telepítése előtt kell rendelkeznie a telepítő letöltött és található a Windows Server. Kaphat, hogy a telepítő a legújabb verzióját a [Microsoft Download Center](http://aka.ms/azurebackup_agent) vagy a Recovery Services-tároló irányítópult-oldalon. A telepítő menteni egy könnyen elérhető helyre, például a * C:\Downloads\*.

Azt is megteheti a PowerShell használatával a letöltési segédprogramját beolvasása:
 
 ```
 $MarsAURL = 'Http://Aka.Ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Az ügynök telepítéséhez futtassa a következő parancsot egy emelt szintű PowerShell-konzolon:

```
PS C:\> MARSAgentInstaller.exe /q
```

Ezzel telepíti az ügynököt az összes alapértelmezett beállítást. A telepítés néhány percet vesz igénybe a háttérben. Ha nem adja meg a */nu* lehetőséget, majd a **Windows Update** ablak nyílik meg a frissítések keresését a telepítés végén. A telepítést követően az ügynök a telepített programok listájában jelennek meg.

A telepített programok listájának megtekintéséhez keresse fel **Vezérlőpult** > **programok** > **programok és szolgáltatások**.

![Ügynök telepítve](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Telepítési beállítások
A parancssori keresztül elérhető lehetőségekről, használja a következő parancsot:

```
PS C:\> MARSAgentInstaller.exe /?
```

Az elérhető lehetőségek a következők:

| Beállítás | Részletek | Alapértelmezett |
| --- | --- | --- |
| /q |Csendes telepítés |- |
| / p: "hely" |Az Azure Backup szolgáltatás ügynökének a telepítési mappa elérési útja. |C:\Program Files\Microsoft Azure Recovery Services Agent ügynök |
| / s: "hely" |Az Azure Backup szolgáltatás ügynökének a gyorsítótár mappájának elérési útja. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Részvétel a Microsoft Update |- |
| /Nu |Ne keressen frissítéseket telepítésének befejezése után |- |
| /d |Eltávolítja a Microsoft Azure Recovery Services Agent ügynök |- |
| /pH |Állomás proxycím |- |
| /po |Proxy Host Port száma |- |
| /Pu |Proxy-állomás felhasználónév |- |
| /pW |Proxy jelszava |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Windows Server vagy a Windows ügyfél gép Recovery Services-tároló regisztrálása
A Recovery Services-tároló létrehozását követően töltse le a legújabb ügynököt és a tárolói hitelesítő adatokat, és C:\Downloads például egy tetszőleges helyen tárolja.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
```

A Windows Server vagy a Windows ügyfél gépén, futtassa a [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) parancsmag regisztrálni a gépet a tárolóban.
Ezzel és más parancsmagok a biztonsági, amelyek a MSONLINE modulból, amely a Mars AgentInstaller hozzá a telepítési folyamat részeként. 

Az ügynök telepítőprogramját nem frissíti a $Env: PSModulePath változó. Ez azt jelenti, hogy a modul automatikus-betöltése sikertelen. A probléma megoldásához a következőket teheti:

```
PS C:\>  $Env:psmodulepath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules
```

Másik lehetőségként manuálisan betöltése a modul a parancsfájlban az alábbiak szerint:

```
PS C:\>  Import-Module  'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'

```

Az Online biztonsági mentést készítő parancsmagok tölthető be, ha regisztrál a tárolói hitelesítő adatokat:


```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Ne használjon relatív elérési utak adhatja meg a tároló hitelesítési adatait tartalmazó fájlt. A parancsmag bemenetként abszolút elérési utat kell megadnia.
>
>

## <a name="networking-settings"></a>Hálózati beállítások
Ha a Windows számítógép az internethez csatlakoztatásához proxykiszolgálón keresztül, a proxykiszolgáló beállításait is megadható az ügynököt. Ebben a példában nincs nincs proxykiszolgáló, explicit módon azt vannak törlésével bármely proxy kapcsolatos információkat.

Sávszélesség is szabályozható lehetőségét ```work hour bandwidth``` és ```non-work hour bandwidth``` napokat a hét adott számú.

Beállítás a proxy- és sávszélesség részletei történik használatával a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) parancsmagot:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Titkosítási beállítások
A biztonsági mentési Azure biztonsági mentési küldött adatok védelmét az adatok bizalmas mivoltát titkosított. A titkosítás jelszava a "password" vissza kell fejtenie az adatokat a visszaállítás időpontjában.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
PS C:\> $PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force 
PS C:\> $PassCode   = 'AzureR0ckx'
PS C:\> Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
Server properties updated successfully
```

> [!IMPORTANT]
> Jelszó információinak megtartása biztonságos be van állítva. Az Azure-ból adatokat nem tudja visszaállítani ezt a jelszót nélkül.
>
>

## <a name="back-up-files-and-folders"></a>Fájlok és mappák biztonsági mentése
Az Azure Backup szolgáltatásba a Windows-kiszolgálók és ügyfelek összes biztonsági mentés a házirend vonatkozik. A házirend három részből áll:

1. A **biztonsági mentés ütemezése** , amely megadja, amikor biztonsági mentést kell venni és szinkronizálása a szolgáltatással.
2. A **adatmegőrzési ütemterv** , amely megadja, mennyi ideig szeretné megőrizni a helyreállítási pontok az Azure-ban.
3. A **belefoglalási/kizárási megadására** , amely határozzák meg, hogy mi készüljön biztonsági másolat.

Ebben a dokumentumban mivel azt még automatizálása a biztonsági mentés, feltételezzük, semmi nem konfiguráltak. Hozzon létre egy új biztonsági mentési házirend használatával megkezdjük a [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) parancsmag.

```
PS C:\> $newpolicy = New-OBPolicy
```

Jelenleg a házirend üres, és más parancsmagok olyan szükséges meghatározásához, hogy mely elemeket kell befoglalt, sem a kizárt, amikor biztonsági mentést fog futni, és egyes esetekben a biztonsági mentések tárolódik.

### <a name="configuring-the-backup-schedule"></a>A biztonsági mentési ütemezés konfigurálása
Az első 3 részből álló házirend a biztonsági mentési ütemezést, és létre kell hozni a [New-OBSchedule](https://technet.microsoft.com/library/hh770401) parancsmag. A biztonsági mentés ütemezése határozza meg, amikor biztonsági mentést kell tenni. Ütemezés létrehozásakor meg kell adnia 2 bemeneti paraméterek:

* **A hét napjai** , amely a biztonsági mentést kell futtatnia. A biztonsági mentési feladat csak egy napon, vagy a hét minden napján, vagy tetszőleges kombinációját a közötti futtatása.
* **A nap** mikor fusson a biztonsági mentés. Ha a biztonsági mentés akkor is kiváltódik 3 különböző napszakokban adhat meg.

Például konfigurálhatja a biztonsági mentési házirend du. 4: futtat minden szombat és vasárnap.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

A biztonsági mentés ütemezése kell lennie a házirendhez társított, és ez használatával érhető el a [Set-OBSchedule](https://technet.microsoft.com/library/hh770407) parancsmag.

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Egy megőrzési házirend konfigurálása
A megőrzési házirend határozza meg, mennyi ideig megőrzi a biztonsági mentési feladatok létrehozott helyreállítási pontok. Egy új megőrzési házirend használatával létrehozásakor a [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) parancsmaggal, megadhatja az, hogy hány napig őrzi meg az Azure biztonsági mentési kell a biztonsági mentések helyreállítási pontjait. Az alábbi példában 7 napos adatmegőrzési állítja be.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

Az adatmegőrzési hozzá kell rendelni a fő házirend-parancsmag segítségével [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

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
### <a name="including-and-excluding-files-to-be-backed-up"></a>Többek között, és a fájlok biztonsági mentése nélkül
Egy ```OBFileSpec``` objektum meghatározása és a biztonsági másolat nem lehet a fájlokat. Ez a szabálykészlet, amely a védett fájlok és mappák gépen kimenő hatókör. Akkor is, számos befoglalási vagy kizárási szabály szükség szerint fájlt, és rendelje hozzá őket egy házirendet. Új OBFileSpec objektum létrehozásakor meg a következőket teheti:

* Adja meg a fájlok és mappák része
* Adja meg a fájlok és mappák ki lesznek zárva
* Adja meg a rekurzív az adatok biztonsági mentése egy mappában (vagy) e a megadott mappában csak a legfelső szintű fájlok biztonsági másolatot kell fel.

Ez utóbbi használatával érhető el a nem rekurzív - jelzőt a New-OBFileSpec parancsban.

Az alábbi példa azt fogja biztonsági mentése kötet C: és D: és a az operációs rendszer bináris ideiglenes mappákat a Windows mappában található fájlok kizárása. Ennek érdekében hozzon létre két fájl használatával specifikációk a [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) parancsmag - befoglalási és kizárási egyet. A fájl specifikációk létrehozása után, fontosságúak a szabályzat használatához a [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) parancsmag.

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
A csoportházirend-objektum most már befejeződött, és egy társított biztonsági mentés ütemezése, a megőrzési házirend és a fájlok belefoglalási/kizárási lista. Ez a házirend most lehet véglegesíteni az Azure Backup használatához. Alkalmazása előtt az újonnan létrehozott házirend gondoskodjon arról, hogy nincsenek-e a kiszolgálóhoz társított használatával meglévő biztonsági mentési házirendek a [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) parancsmag. A házirend eltávolítása megerősítő fogja kérni. Kihagyja a megerősítő használja a ```-Confirm:$false``` jelzőt mellékel a parancsmagot.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A csoportházirend-objektum életbe léptetése történik használatával a [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) parancsmag. Ez is megerősítést kér. Kihagyja a megerősítő használja a ```-Confirm:$false``` jelzőt mellékel a parancsmagot.

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

A részleteket a meglévő biztonsági mentési házirend használatával megtekintheti a [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) parancsmag. Részletezve használatával a [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) parancsmag a biztonsági mentési ütemezés és a [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) parancsmag az adatmegőrzési házirendek

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

### <a name="performing-an-ad-hoc-backup"></a>Az ad hoc biztonsági mentés
Miután a biztonsági mentési házirend van beállítva a biztonsági mentések száma az ütemezésben fog létrejönni. Az ad hoc biztonsági mentés indítására az is lehetséges használatával a [Start-OBBackup](https://technet.microsoft.com/library/hh770426) parancsmagot:

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

## <a name="restore-data-from-azure-backup"></a>Állítsa vissza az adatokat az Azure Backup
Ez a szakasz végigvezeti az Azure biztonsági mentés az adatok helyreállítás automatizálása lépéseit. Ennek során a következő lépésekből áll:

1. Válassza ki a forráskötet
2. Válassza ki a visszaállítandó biztonsági mentési pontok
3. Válasszon ki egy elemet visszaállítása
4. A visszaállítási folyamat eseményindító

### <a name="picking-the-source-volume"></a>A forráskötet válassza háttérszínnek.
Ahhoz, hogy az Azure Backup elem visszaállításához először kell azonosítani a cikk forrását. Azt éppen végrehajtás alatt a parancsokat egy Windows Server vagy egy Windows ügyfél környezetben, mert a gép már azonosítja. A következő lépése a forrás azonosítása, hogy az azt tartalmazó kötet azonosításához. Erről a gépről biztonsági mentés alatt lekérhetők végrehajtása adatforrásokat, illetve kötetek listáját a [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) parancsmag. Ez a parancs minden, a biztonsági másolatot készített a kiszolgáló ügyfél források tömbjét adja vissza.

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

### <a name="choosing-a-backup-point-from-which-to-restore"></a>A visszaállítandó biztonsági mentési pont kiválasztása
Egy biztonsági mentési pontok listájának lekérése a következő futtatásával a [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) parancsmag megfelelő paraméterekkel. A jelen példában mutatjuk be a legújabb biztonsági mentési pont a forráskötet *D:* , és visszaállít egy adott fájlt.

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
Az objektum ```$rps``` biztonsági mentési pontok tömbje. Az első elem a legutóbbi pontnak, az n-edik elemének pedig a legrégebbi pont. Válassza ki a legutóbbi pontnak, hogy használjuk ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Visszaállítás elem kiválasztása
A pontos fájl vagy mappa visszaállításához rekurzív módon használja azonosítására a [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) parancsmag. A mappahierarchia kizárólag használatával tallózható így a ```Get-OBRecoverableItem```.

Ebben a példában, ha azt szeretné, hogy állítsa vissza a fájlt *finances.xls* azt hivatkozhat, amely az objektum ```$filesFolders[1]```.

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

Az elemek visszaállítás segítségével is kereshet a ```Get-OBRecoverableItem``` parancsmag. A jelen példában keressen rá a *finances.xls* azt sikerült leírót a fájl a következő parancs futtatásával:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>A visszaállítási folyamat időt.
A visszaállítási folyamat indításához először kell adja meg a helyreállítási beállításokat. Ezt megteheti a a [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) parancsmag. Ehhez a példához tegyük fel, hogy a fájlok visszaállítására szeretnénk *C:\temp*. Tegyük is fel, hogy szeretnénk már megtalálható fájlok kihagyása a rendeltetési mappára *C:\temp*. Hozzon létre egy helyreállítási lehetőség, használja a következő parancsot:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Most elindítani a visszaállítási folyamat használatával a [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) parancs a kiválasztott ```$item``` kimenetében a ```Get-OBRecoverableItem``` parancsmagot:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Az Azure Backup szolgáltatás ügynökének eltávolítása
Az Azure Backup-ügynök eltávolítása végezhető a következő paranccsal:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

Az ügynök bináris fájlok eltávolítása a számítógépről következménnyel néhány kell figyelembe venni:

* A fájl-szűrő eltávolítja a gépet, és a változások követését le van állítva.
* A gép eltávolítja az összes házirend az adatokat, de a házirenddel kapcsolatos információk továbbra is a szolgáltatásban kell tárolni.
* A mentési ütemezések törlődnek, és nincs további biztonsági mentés készül.

Azonban az adatok Azure marad tárolja, és megőrzi a megőrzési házirend beállítása adott meg. Régebbi pontok automatikusan van elavult.

## <a name="remote-management"></a>Távfelügyelet
Az Azure Backup agent, a házirendek és a adatforrások minden felügyeleti távolról végezhető el a PowerShell. A távolról felügyelt számítógép kell megfelelően elő kell készíteni.

Alapértelmezés szerint a WinRM szolgáltatás kézi indításra van konfigurálva. Az indítási típust kell beállítani *automatikus* és kell indítani a szolgáltatást. Győződjön meg arról, hogy a WinRM szolgáltatás fut, az állapot tulajdonság értékének kell *futtató*.

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

A számítógép most távolról felügyelhetők - az ügynök telepítésének indítása. A következő parancsfájl például másolja át az ügynököt a távoli számítógépre, és telepíti azt.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>További lépések
További információ az Azure biztonsági mentés a Windows Server vagy Windows-ügyfélen lásd:

* [Az Azure Backup bemutatása](backup-introduction-to-azure-backup.md)
* [Windows-kiszolgálók biztonsági mentését](backup-configure-vault.md)
