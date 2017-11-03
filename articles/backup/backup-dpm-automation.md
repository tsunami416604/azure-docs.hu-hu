---
title: "Az Azure Backup - a PowerShell szolgáltatás használatával a DPM-munkaterhelések biztonsági mentése |} Microsoft Docs"
description: "Megtudhatja, hogyan telepíthetnek és kezelhetnek az Azure Backup a Data Protection Manager (DPM) PowerShell használatával"
services: backup
documentationcenter: 
author: NKolli1
manager: shreeshd
editor: 
ms.assetid: e9bd223c-2398-4eb1-9bf3-50e08970fea7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: adigan;anuragm;trinadhk;markgal
ms.openlocfilehash: 6e88e8f5d385d63d491415583e1d8c7f89324cc1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Az Azure-ba történő biztonsági mentés üzembe helyezése és kezelése DPM-kiszolgálókon a PowerShell-lel
> [!div class="op_single_selector"]
> * [ARM](backup-dpm-automation.md)
> * [Klasszikus](backup-dpm-automation-classic.md)
>
>

Ez a cikk bemutatja, hogyan PowerShell használatával történő telepítés Azure biztonsági mentés DPM-kiszolgálón, és kezelheti a biztonsági mentés és helyreállítás.

## <a name="setting-up-the-powershell-environment"></a>A PowerShell-környezet létrehozása
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Mielőtt a PowerShell használatával a Data Protection Manager biztonsági mentések kezelése az Azure-ba, szüksége a megfelelő környezete van, a PowerShell. A PowerShell-munkamenet elején győződjön meg arról, hogy futtatja a következő parancsot a megfelelő modul importálása és engedélyezi, hogy helyesen hivatkozik a DPM-parancsmagokat:

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Telepítését és regisztrálását
Megkezdéséhez:

1. [Töltse le a legfrissebb PowerShell](https://github.com/Azure/azure-powershell/releases) (szükséges minimális verziója: 1.0.0)
2. Engedélyezze az Azure Backup szolgáltatás parancsmagjaival átváltás *AzureResourceManager* mód használatával a **Switch-AzureMode** parancsmagot:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

A következő telepítését és regisztrálását feladatok automatizálhatók a PowerShell használatával:

* Recovery Services-tároló létrehozása
* Az Azure Backup szolgáltatás ügynökének telepítése
* Az Azure Backup szolgáltatás regisztrálása
* Hálózati beállítások
* Titkosítási beállítások

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
A következő lépések alapján a Recovery Services-tároló létrehozása. Recovery Services-tároló nem egyezik egy biztonsági mentési tárolót.

1. Ha az Azure biztonsági mentés először használ, kell használnia a **Register-AzureRMResourceProvider** parancsmag futtatásával regisztrálja az Azure Recovery szolgáltató az előfizetéshez.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. A Recovery Services-tároló egy ARM-erőforrás, ezért el kell helyezni az erőforráscsoporton belül. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Új erőforráscsoport létrehozása esetén adja meg a nevét és helyét, ahhoz az erőforráscsoporthoz.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Használja a **New-AzureRmRecoveryServicesVault** parancsmag segítségével hozzon létre egy új tárolót. Ne felejtse el ugyanazon a helyen, a tároló adja meg, mint az erőforráscsoport használt.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
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

Futtassa a parancsot, a Get-AzureRmRecoveryServicesVault, és az előfizetés összes tárolók találhatók.

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


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Az Azure Backup szolgáltatás ügynökének telepítése a DPM-kiszolgálón
Az Azure Backup szolgáltatás ügynökének telepítése előtt kell rendelkeznie a telepítő letöltött és található a Windows Server. Kaphat, hogy a telepítő a legújabb verzióját a [Microsoft Download Center](http://aka.ms/azurebackup_agent) vagy a Recovery Services-tároló irányítópult-oldalon. A telepítő menteni egy könnyen elérhető helyre, például a * C:\Downloads\*.

Az ügynök telepítéséhez futtassa a következő parancsot egy rendszergazda jogú PowerShell-konzolban **a DPM-kiszolgálón**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Ezzel telepíti az ügynököt az összes alapértelmezett beállítást. A telepítés néhány percet vesz igénybe a háttérben. Ha nem adja meg a */nu* lehetőséget a **Windows Update** ablak nyílik meg a frissítések keresését a telepítés végén.

Az ügynök megjelennek a telepített programok listájában. A telepített programok listájának megtekintéséhez keresse fel **Vezérlőpult** > **programok** > **programok és szolgáltatások**.

![Az ügynök telepítve](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Telepítési beállítások
A parancssor keresztül elérhető lehetőségekről, használja a következő parancsot:

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Egy helyreállítási regisztrálásakor DPM szolgáltatások tároló
A Recovery Services-tároló létrehozását követően töltse le a legújabb ügynököt és a tárolói hitelesítő adatokat, és C:\Downloads például egy tetszőleges helyen tárolja.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

A DPM-kiszolgálón futtassa a [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) parancsmag regisztrálni a gépet a tárolóban.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Kezdeti konfigurációs beállításai
Miután a DPM-kiszolgáló regisztrálva van a Recovery Services-tároló, alapértelmezett előfizetési beállítások kezdődik. Ezek előfizetési beállítások közé tartozik a hálózatkezelés, a titkosítás és az átmeneti területre. Először kapott egy leíró használata (alapértelmezett) beállítást az előfizetési beállítások módosítása a [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) parancsmagot:

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Minden módosítás a helyi PowerShell objektumhoz ```$setting``` majd a teljes objektum elkötelezte magát a DPM és az Azure Backup mentse őket, és használja a [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmag. Kell használnia a ```–Commit``` beállítás segítségével győződjön meg arról, hogy a változtatások megmaradnak. A beállítások nem is alkalmazott és Azure Backup szolgáltatás csak a véglegesített használni.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Hálózat
Ha a DPM-számítógépen az Azure Backup szolgáltatás az interneten való csatlakoztatásához proxykiszolgálón keresztül, majd a proxykiszolgáló beállításait kell rendelkezni a sikeres biztonsági mentések. Ehhez használja a ```-ProxyServer```és ```-ProxyPort```, ```-ProxyUsername``` és a ```ProxyPassword``` paramétereket a [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmag. Ebben a példában nincs proxy kiszolgáló, explicit módon azt vannak törlésével bármely proxy kapcsolatos információkat.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Sávszélesség is szabályozható a beállítások a ```-WorkHourBandwidth``` és ```-NonWorkHourBandwidth``` napokat a hét adott számú. Ebben a példában a Microsoft nem állítja a sávszélesség-szabályozás.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Az átmeneti területre konfigurálása
A DPM-kiszolgálón futó Azure Backup ügynöknek szüksége van ideiglenes tárhelyre, (helyi átmeneti terület) a felhőből helyreállított adatok számára. Konfigurálhatja az átmeneti területre segítségével a [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmag és a ```-StagingAreaPath``` paraméter.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

A fenti példában az átmeneti területre úgy lesz beállítva, *C:\StagingArea* a PowerShell objektumban ```$setting```. Győződjön meg arról, hogy a megadott mappa már létezik, vagy pedig az előfizetési beállítások végső véglegesítése sikertelen lesz.

### <a name="encryption-settings"></a>Titkosítási beállítások
A biztonsági mentési Azure biztonsági mentési küldött adatok védelmét az adatok bizalmas mivoltát titkosított. A titkosítás jelszava a "password" vissza kell fejtenie az adatokat a visszaállítás időpontjában. Fontos tartani ezeket az információkat megbízható és biztonságos be van állítva.

Az alábbi példában a karakterlánc az első parancs konvertálása ```passphrase123456789``` egy biztonságos karakterláncot, és a biztonságos karakterláncot kell megadnia a változóhoz nevű rendel ```$Passphrase```. a második parancs beállítja a biztonságos karakterláncot kell megadnia ```$Passphrase``` és a biztonsági másolatok titkosításához.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Jelszó információinak megtartása biztonságos be van állítva. Nem lehet visszaállítani az adatokat az Azure-ból nélkül ezt a jelszót.
>
>

Ezen a ponton a szükséges módosításokat, hogy el kell a ```$setting``` objektum. Ne felejtse el a változtatások véglegesítése a határidő.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Az Azure Backup szolgáltatásba adatok védelme
Ez a szakasz az üzemi kiszolgáló hozzáadása a DPM, és majd adatvédelemben a helyi DPM-tároló, majd az Azure Backup szolgáltatásba. A példákban a fájlok és mappák biztonsági bemutatjuk. A logikai könnyen bővíthető minden DPM által támogatott adatforrás biztonsági mentéséhez. A DPM biztonsági mentések által a védelmi csoport (PG) négy alkotórészek vonatkoznak:

1. **Csoport tagjai** a védhető objektumok listáját (más néven *adatforrások* a DPM-ben), amelyek ugyanabba a védelmi csoportba a védeni kívánt. Például előfordulhat, hogy védeni kívánt virtuális gépek éles egy védelmi csoport és egy másik védelmi csoportban található SQL Server-adatbázisok különböző biztonsági követelményeket is rendelkeznek. Előtt biztonsági másolatot készíthet a datasource egy üzemi kiszolgálón meg kell győződnie, hogy a DPM-ügynök telepítve van a kiszolgálón, és a DPM által kezelt. Kövesse a lépéseket [a DPM-ügynök telepítéséhez](https://technet.microsoft.com/library/bb870935.aspx) és kapcsolásával végezze el a megfelelő DPM-kiszolgálóhoz.
2. **Adatvédelmi módszer** határozza meg a biztonsági mentési célhelyek - szalag, a lemez és a felhőben. Ebben a példában azt fogja védeni az adatokat a helyi lemezek és a felhő.
3. A **biztonsági mentés ütemezése** , amely megadja, hogy ha a biztonsági mentést kell tenni, és milyen gyakran a szinkronizálja az adatokat a DPM-kiszolgálón és az üzemi kiszolgáló között.
4. A **adatmegőrzési ütemterv** , amely megadja, mennyi ideig szeretné megőrizni a helyreállítási pontok az Azure-ban.

### <a name="creating-a-protection-group"></a>Védelmi csoport létrehozásakor
Először hozzon létre egy új védelmi csoport használatával a [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) parancsmag.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

A fenti parancsmag hozza létre a védelmi csoport nevű *ProtectGroup01*. Egy meglévő védelmi csoport is módosíthatja később a biztonsági mentés vegye fel az Azure felhőbe. Azonban ne módosítsa a védelmi csoport - új vagy meglévő - kell a leírót szerezni a *módosíthatóvá* objektumba a [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) parancsmag.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Csoport tagok hozzáadása védelmi csoporthoz
Minden DPM-ügynök tudja azon a kiszolgálón, amelyre telepítve van, az adatforrások listája. Egy adatforrás hozzáadása a védelmi csoport, a DPM-ügynököt kell először elküldi a DPM-kiszolgálón az adatforrások listáját. Egy vagy több adatforrás majd kiválasztva, és hozzáad a védelmi csoporthoz. Ennek eléréséhez szükséges PowerShell lépéseket a következők:

1. A DPM-ügynököt a DPM által kezelt összes kiszolgálók listájának beolvasása.
2. Válasszon egy adott kiszolgálóra.
3. Az összes adatforrás listájának beolvasása a kiszolgálón.
4. Válasszon egy vagy több adatforrást, majd adja hozzá a védelmi csoport

A DPM-ügynök telepítve van és a DPM-kiszolgáló által kezelt kiszolgálók listája szerzett a [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) parancsmag. Ebben a példában azt szűrésére és konfigurálását elvégzi, csak PS nevű *productionserver01* a biztonsági mentéshez.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Most beolvasni az adatforrások listája ```$server``` használatával a [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) parancsmag. Ebben a példában a Microsoft jelenleg korlátozza a kötetek * D:\* kell konfigurálni a biztonsági mentéshez. Ez az adatforrás kerül a védelmi csoport használatával a [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) parancsmag. Fontos, hogy a *módosíthatóvá* védelmi csoport objektum ```$MPG``` , hogy a kiegészítsék.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Ismételje meg ezt a lépést, ha szükséges, ahányszor, amíg az összes kiválasztott adatforrás a védelmi csoporthoz hozzáadott. Start van egy DataSource elemmel, és végezze el a védelmi csoport létrehozása a munkafolyamatot, majd később adja további adatforrások védelmi csoporthoz.

### <a name="selecting-the-data-protection-method"></a>Az adatvédelmi módszer kiválasztása
Miután az adatforrások érhetőek el a védelmi csoport, a következő lépéssel adhatja meg a védelmi módszer használatával-e a [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) parancsmag. Ebben a példában a védelmi csoport működik a helyi lemezek és felhőbeli biztonsági mentését. Is meg kell adnia a felhőbe használatával védeni kívánt adatforrást a [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) jelölővel - Online parancsmag.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>A megőrzési tartomány beállítása
A biztonsági mentési pontok használatával, állítsa be a megőrzési a [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) parancsmag. Amíg tűnhet páratlan beállítani a megőrzési előtt a biztonsági mentés ütemezése definiálva van, használja a ```Set-DPMPolicyObjective``` parancsmag automatikusan úgy állítja be a alapértelmezett biztonsági mentés ütemezését, majd módosítható. Mindig lehetőség a biztonsági mentés ütemezése először megadása és az adatmegőrzési után.

Az alábbi példában a parancsmag a lemezes biztonsági mentések megőrzési paramétereinek beállítása. Ez fogja megőrizni biztonsági másolatokat 10 nap, és szinkronizálja az adatokat az üzemi kiszolgálón és a DPM-kiszolgáló közötti 6 óránként. A ```SynchronizationFrequencyMinutes``` nem adja meg, milyen gyakran egy biztonsági mentési pont jön létre, de milyen gyakran adatokat a DPM-kiszolgálóra másolja.  Ez a beállítás megakadályozza, hogy a biztonsági mentések túl nagy.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Ugrás az Azure biztonsági mentés (DPM hivatkozik azokat az Online biztonsági mentés másként) állítható be a megőrzési időtartamokat [hosszú távon szerzett-Édesapja-fia megjelenítve (GFS) megőrzési](backup-azure-backup-cloud-as-tape.md). Ez azt jelenti, hogy napi, heti, havi és éves adatmegőrzési szabályoknál érintő kombinált adatmegőrzési adhat meg. Ebben a példában azt az összetett megőrzési séma, amely azt szeretnénk, ha képviselő tömböt létrehozása, és adja meg a megőrzési tartomány használata a [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) parancsmag.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>A biztonsági mentési ütemezést beállítani.
A DPM automatikusan beállítja, egy alapértelmezett biztonsági mentés ütemezése ha megadja, hogy a védelmi cél használata a ```Set-DPMPolicyObjective``` parancsmag. Az alapértelmezett ütemezés módosításához használja a [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) parancsmag követi a [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) parancsmag.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

A fenti példában ```$onlineSch``` tömb négy elemekkel, amely tartalmazza a meglévő online védelmi ütemterv a védelmi csoport GFS sémában:

1. ```$onlineSch[0]```napi ütemezés tartalmazza
2. ```$onlineSch[1]```a heti ütemezés tartalmazza
3. ```$onlineSch[2]```a havi ütemezések tartalmazza
4. ```$onlineSch[3]```éves ütemezés tartalmazza

Így ha módosítania kell a heti ütemezés, akkor tekintse meg a ```$onlineSch[1]```.

### <a name="initial-backup"></a>Kezdeti biztonsági mentés
Ha egy adatforrás először, DPM rendszernek végig kell biztonsági mentéséről hoz létre a kezdeti replika, amely az adatforrás védelemre a DPM-replikakötet teljes másolatot készít. Ez a tevékenység vagy egy adott időpont ütemezhetők, vagy manuálisan is elindítható, használja a [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) parancsmag és paraméter ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>DPM-replika & a helyreállításipont-kötet méretének módosítása
Módosíthatja a méretét a DPM-replikakötet és árnyékmásolat-kötet használatával is [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) parancsmag az alábbi példában látható módon: Get-DatasourceDiskAllocation - Datasource $DS Set-datasourcediskallocation segédprogram - DataSource $DS - ProtectionGroup $MPG-manuális - ReplicaArea (2 gb) – ShadowCopyArea (2 gb)

### <a name="committing-the-changes-to-the-protection-group"></a>A módosítások végrehajtása a védelmi csoporthoz
Végül a módosításokat kell hajtható végre, a DPM a biztonsági mentés az új védelmi csoport konfigurációja / megkezdése előtt. Ez érhető el, használja a [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) parancsmag.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>A biztonsági mentési pontok megtekintése
Használhatja a [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) parancsmag listájának összes helyreállítási pont egy adatforrás. Ebben a példában a következő történik:

* a PGs beolvasni a DPM-kiszolgálón, és egy tömbben található```$PG```
* a megfelelő adatforrások beolvasása az```$PG[0]```
* a helyreállítási pontok lekérése egy adatforrás.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Az Azure-on védett adatok visszaállítása
Adat-visszaállítást a rendszer kombinációja egy ```RecoverableItem``` objektum és a ```RecoveryOption``` objektum. Az előző szakaszban egy adatforrás azt kapott a biztonsági mentési pontok listáját.

Az alábbi példában bemutatjuk, hogyan visszaállíthatók Hyper-V rendszerű virtuális gép Azure Backup szolgáltatás biztonsági mentési pontok kombinálásával a Helyreállítás célhelye. Ebben a példában a következőket tartalmazza:

* Egy helyreállítási lehetőség segítségével létrehozása a [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) parancsmag.
* Segítségével biztonsági mentési pontok tömbjének beolvasása a ```Get-DPMRecoveryPoint``` parancsmag.
* A visszaállítandó biztonsági mentési pontok kiválasztásához.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

A parancsok könnyen bővíthető bármely adatforrástípusnál.

## <a name="next-steps"></a>Következő lépések
* További információ a DPM az Azure Backup szolgáltatásba: [Bevezetés a DPM biztonsági mentése](backup-azure-dpm-introduction.md)
