---
title: Az Azure Backup – a DPM számítási feladatainak biztonsági mentéséhez használja a Powershellt
description: Ismerje meg, hogyan helyezheti üzembe és felügyelheti az Azure Backup a Data Protection Manager (DPM) PowerShell-lel
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: adigan
ms.openlocfilehash: 5ef9d61e880d3252eae2d8ef924ff39a5d2f6acf
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497910"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Az Azure-ba történő biztonsági mentés üzembe helyezése és kezelése DPM-kiszolgálókon a PowerShell-lel
Ez a cikk bemutatja, hogyan PowerShell használatával a DPM-kiszolgáló Azure Backup telepítőjét, és kezelheti a biztonsági mentés és helyreállítás.

## <a name="setting-up-the-powershell-environment"></a>A PowerShell-környezet beállítása
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

PowerShell használatával a Data Protection Manager biztonsági másolatainak kezelése az Azure-ba, mielőtt szüksége a megfelelő környezet van a PowerShellben. A PowerShell-munkamenet elején győződjön meg arról, hogy importálja a megfelelő modulokat, és megfelelően hivatkoznak a DPM-parancsmagok lehetővé teszik a következő parancsot futtatja:

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

## <a name="setup-and-registration"></a>Beállítása és regisztrálása
A kezdéshez:

1. [Töltse le a legújabb PowerShell](https://github.com/Azure/azure-powershell/releases) (szükséges minimális verziója: 1.0.0)
2. Engedélyezze az Azure Backup parancsmagok átvált a *AzureResourceManager* módból a **Switch-AzureMode** parancsmagot:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

A PowerShell használatával automatizálható a következő beállítása és regisztrálása feladatokat:

* Recovery Services-tároló létrehozása
* Az Azure Backup ügynök telepítése
* Regisztrálás az Azure Backup szolgáltatással
* Hálózati beállítások
* Titkosítási beállítások

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
A következő lépések végigvezetik Önt a Recovery Services-tároló létrehozása. Recovery Services-tároló nem egyezik egy biztonsági mentési tárolót.

1. Az Azure Backup használatakor az első alkalommal kell használnia a **Register-AzureRMResourceProvider** parancsmagot, hogy regisztrálja az Azure Recovery Services-szolgáltatót az előfizetésében.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. A Recovery Services-tároló egy ARM-erőforrás, ezért elhelyezi egy erőforráscsoporton belül kell. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Amikor egy új erőforráscsoportot hoz létre, adja meg a nevét és az erőforráscsoport helyét.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Használja a **New-AzureRmRecoveryServicesVault** parancsmaggal hozzon létre egy új tárolót. Mindenképpen adja meg a tároló ugyanazon a helyen, mint az erőforráscsoport.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Adja meg a használandó; tárhely-redundancia típusát használhat [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) vagy [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md). Az alábbi példa bemutatja, hogy a - BackupStorageRedundancy beállítás a testVault GeoRedundant értékre van állítva.

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintheti az előfizetéshez
Használat **Get-AzureRmRecoveryServicesVault** összes tárolók listájának megtekintéséhez az aktuális előfizetésben. Ezt a parancsot használhatja, ellenőrizze, hogy az új tároló létrejött, vagy tekintse meg, milyen tárolók érhetők el az előfizetésben.

Futtassa a parancsot, a Get-AzureRmRecoveryServicesVault, és az előfizetés összes tárolók fel vannak sorolva.

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


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Az Azure Backup ügynök telepítése a DPM-kiszolgálón
Az Azure Backup ügynök telepítése előtt kell a telepítő letöltött és a jelen van a Windows Server rendszeren. Kérheti, hogy a telepítőt a legújabb verzióját a [Microsoft Download Center](https://aka.ms/azurebackup_agent) vagy a Recovery Services-tároló irányítópult-oldalon. A telepítő például könnyen elérhető helyre mentse * C:\Downloads\*.

Az ügynök telepítéséhez futtassa a következő parancsot egy rendszergazda jogú PowerShell-konzolon **a DPM-kiszolgálón**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Ez telepíti az ügynököt az összes alapértelmezett beállítást. A telepítés néhány percet vesz igénybe a háttérben. Ha nem adja meg a */nu* lehetőség a **Windows Update** ablak nyílik meg minden olyan frissítéseket keresni a telepítés végén.

A telepített programok listájában megjelenik az ügynököt. A telepített programok listájának megtekintéséhez, keresse fel a **Vezérlőpult** > **programok** > **programok és szolgáltatások**.

![Ügynök telepítve](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Telepítési beállítások
A parancssor keresztül elérhető összes beállítások megtekintéséhez használja a következő parancsot:

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Regisztrálása a DPM egy Recovery Services-tároló
A Recovery Services-tároló létrehozása után töltse le a legújabb ügynököt és a tároló hitelesítő adatait, és egy kényelmes helyen, például C:\Downloads tárolja.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

A DPM-kiszolgálón futtassa a [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) parancsmag regisztrálni a gépet a tárolóval.

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
Miután a DPM-kiszolgáló regisztrálva van a Recovery Services-tárolóval, alapértelmezett előfizetési beállítások kezdődik. Ezen előfizetés beállítások közé tartozik a hálózatkezelés, titkosítás és az átmeneti területen. Először kapott egy leírót a meglévő (alapértelmezett) beállítások segítségével az előfizetés beállításainak módosításához a [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) parancsmagot:

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

A helyi PowerShell-objektummá végrehajtott összes módosítás ```$setting``` és majd a teljes objektum elkötelezett a DPM és az Azure Backup pedig mentheti azokat használja a [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmag. Kell használnia a ```–Commit``` jelző annak érdekében, hogy a változtatások megmaradnak. A beállítások nem kell alkalmazni – csak a véglegesített az Azure Backup által használt.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Hálózat
Ha a kapcsolat a DPM-gép az Azure Backup szolgáltatásba, az interneten egy proxykiszolgálón keresztül, majd a proxykiszolgáló beállításait kell adni a sikeres biztonsági mentések. Ennek segítségével történik a ```-ProxyServer```és ```-ProxyPort```, ```-ProxyUsername``` és a ```ProxyPassword``` paramétereket a [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmagot. Ebben a példában nincs proxy-kiszolgáló, hogy explicit módon vannak törlésével bármely proxy kapcsolatos információkat.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Sávszélesség is szabályozható a beállításokkal ```-WorkHourBandwidth``` és ```-NonWorkHourBandwidth``` az adott, a hét napjait. Ebben a példában azt nem állítja bármely szabályozás.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Az előkészítési területet átmeneti konfigurálása
Az Azure Backup ügynököt a DPM-kiszolgálón fut (helyi átmeneti terület) a felhőből helyreállított adatok számára szüksége van ideiglenes tárhelyre. Az előkészítési terület használatával konfigurálja a [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmag és a ```-StagingAreaPath``` paraméter.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

A fenti példában az előkészítési területet átmeneti értékre lesz beállítva *C:\StagingArea* a PowerShell-objektum ```$setting```. Győződjön meg arról, hogy a megadott mappa már létezik, vagy pedig az előfizetés beállításait, a végső véglegesítés sikertelen lesz.

### <a name="encryption-settings"></a>Titkosítási beállítások
Az Azure Backup biztonsági mentési adatforgalom titkosított a bizalmas adatok védelme érdekében. A titkosítási jelszó a "password" a visszaállítás időpontjában az adatok visszafejtéséhez. Fontos, hogy ezeket az információkat megbízható és biztonságos be van állítva.

Az alábbi példában az első parancs konvertálja a ```passphrase123456789``` egy biztonságos karakterláncot és a biztonságos karakterlánc-változóhoz nevű rendel ```$Passphrase```. a második parancs beállítja a biztonságos karakterláncot ```$Passphrase``` biztonsági mentések titkosításához jelszóként.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Biztosíthatja a hozzáférési kódot adatok védelméről be van állítva. Nem lehet visszaállítani az adatok az Azure-ból anélkül, hogy ezt a jelszót.
>
>

Ezen a ponton a szükséges módosításokat a végrehajtott kell a ```$setting``` objektum. Fontos, hogy a módosítások véglegesítéséhez.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Az Azure Backup az adatok védelme
Ebben a szakaszban egy üzemi kiszolgáló hozzáadása a dpm-hez, és majd a helyi DPM-tároló, majd az Azure Backup az adatok védelme. A példák azt mutatják be, hogyan fájlok és mappák biztonsági mentése. A logikai biztonsági másolatot készíteni a DPM által támogatott adatforrások egyszerűen lehet kiterjeszteni. A DPM biztonsági mentések által a védelmi csoport (PG) a négy részből rendelkezik:

1. **Csoport tagjai** összes védhető objektum listáját (más néven *adatforrások* a DPM-ben), amelyek ugyanabba a védelmi csoportba a védeni kívánt. Például előfordulhat, hogy védeni kívánt éles üzemű virtuális gépekre egy védelmi csoport és a egy másik védelmi csoportban található SQL Server-adatbázisok, a különböző biztonsági követelményekkel rendelkeznek. Mielőtt bármilyen adatforrást kell, hogy éles kiszolgálón készíthető a DPM-ügynök telepítve van a kiszolgálón, és a DPM által kezelt. Kövesse a lépéseket [a DPM-ügynök telepítése](https://technet.microsoft.com/library/bb870935.aspx) és csatolta a megfelelő DPM-kiszolgálón.
2. **Adatvédelmi módszer** adja meg a biztonsági mentési célhelyek – szalag, a lemez és a felhőben. Ebben a példában azt védendő adat a helyi lemez és a felhőbe.
3. A **biztonsági mentési ütemezés** , amely meghatározza, hogy ha a biztonsági mentéseket kell elvégezni, és milyen gyakran kell-e a az adatok szinkronizálása a DPM-kiszolgáló és az üzemi kiszolgáló között.
4. A **adatmegőrzési ütemterv** , amely megadja, hogy mennyi ideig megőrizni a helyreállítási pontok az Azure-ban.

### <a name="creating-a-protection-group"></a>Védelmi csoport létrehozása
Először hozzon létre egy új védelmi csoport használatával a [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) parancsmagot.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

A fenti parancsmag létrehoz egy védelmi csoportot, nevű *ProtectGroup01*. Egy meglévő védelmi csoporthoz később is módosítható az Azure felhőbe biztonsági mentés hozzáadása. Azonban ne módosítsa a védelmi csoport – új vagy meglévő - kell egy leírót szerezni a *módosíthatóvá* objektumba a [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) parancsmagot.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Csoporttagok felvétele védelmi csoportba
Minden DPM-ügynök tudja, hogy ez telepítve van a kiszolgálón az adatforrások listáját. Adatforrás hozzáadása a védelmi csoport, a DPM-ügynököt kell először küldi vissza az adatforrások listáját a DPM-kiszolgálón. Egy vagy több adatforrás ezután kiválasztva, és hozzáadja a védelmi csoporthoz. Ennek eléréséhez szükséges PowerShell-lépések a következők:

1. DPM – a DPM-ügynök által felügyelt összes kiszolgáló listáját olvassa be.
2. Válasszon egy adott kiszolgálóra.
3. Beolvassa az összes adatforrás listája a kiszolgálón.
4. Válasszon egy vagy több adatforrás, és hozzáadhatja őket a védelmi csoport

Amelyen a DPM-ügynök telepítve van, és a DPM-kiszolgáló által kezelt kiszolgálók listáját akkor beszerezni, a [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) parancsmagot. Ebben a példában azt fog szűrni, és csak konfigurálása a PS nevű *productionserver01* a biztonsági mentéshez.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Most már beolvasni az adatforrások listáját a ```$server``` használatával a [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) parancsmagot. Ebben a példában azt szűrést végez a kötet * D:\* , amelyet meg szeretnénk biztonsági mentésének konfigurálásához. Ez az adatforrás majd adnak hozzá a védelmi csoport használatával a [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) parancsmagot. Ne feledje, használhatja a *módosíthatóvá* védelmi csoport objektum ```$MPG``` , hogy a kiegészítéseit.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Ismételje meg ezt a lépést, ha szükséges, ahányszor, mindaddig, amíg a védelmi csoporthoz hozzáadott a kiválasztott adatforrások. Indítsa el a csak egy adatforrás, és végezze el a védelmi csoport létrehozásának munkafolyamata a, és később a védelmi csoport további adatforrások hozzáadása.

### <a name="selecting-the-data-protection-method"></a>Az adatvédelmi módszer kiválasztása
Miután az adatforrások védelmi csoporthoz hozzáadott, az a következő lépés, hogy adja meg a védelmi módszer használatával a [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) parancsmagot. Ebben a példában a védelmi csoport be állítva a helyi lemez és a felhőbeli biztonsági mentés. Emellett meg kell adnia az, hogy a felhő használatával védeni kívánt adatforrás a [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) parancsmagot - Online jelző.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>A megőrzési tartomány beállítása
A-megőrzésének beállítása a biztonsági mentési pontok segítségével a [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) parancsmagot. Bár úgy tűnhet páratlan-e az adatmegőrzési beállítása előtt a biztonsági mentési ütemezés definiálva van, használja a ```Set-DPMPolicyObjective``` parancsmag automatikusan beállítja egy alapértelmezett biztonsági mentési ütemezés szerint is módosítható. Minden esetben lehetőség a biztonsági mentés ütemezése először megadása és az adatmegőrzési után.

Az alábbi példában a parancsmag a lemezes biztonsági mentések megőrzési paramétereinek beállítása. Ez lesz biztonsági mentések megőrzési idejét a 10 nap, és szinkronizálják az adatokat az üzemi kiszolgálón és a DPM-kiszolgáló közötti 6 óránként. A ```SynchronizationFrequencyMinutes``` nem adja meg, hogy milyen gyakran egy biztonsági mentési pont jön létre, de hogyan gyakran adatokat másolja a DPM-kiszolgálón.  Ez a beállítás megakadályozza, hogy a biztonsági másolatok túl nagy.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

A biztonsági másolatokat az Azure-bA történik (a DPM jelenti azokat az Online biztonsági mentés másként) állítható be a megőrzési időtartamok [hosszú távú megőrzési Nagyapa-apa-fiú megjelenítve (GFS)](backup-azure-backup-cloud-as-tape.md). Azt jelenti definiálhat egy kombinált adatmegőrzési házirend használata esetén a napi, heti, havi és éves adatmegőrzési szabályoknál. Ebben a példában azt létrehozni az összetett megőrzési séma, amelyet meg szeretnénk képviselő tömböt, és adja meg a megőrzési tartomány használatával a [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) parancsmagot.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>A biztonsági mentési ütemezés beállítása
A DPM automatikusan beállítja, egy alapértelmezett biztonsági mentési ütemezést Ha adja meg a védelmi cél használata a ```Set-DPMPolicyObjective``` parancsmagot. Az alapértelmezett ütemezés módosításához használja a [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) parancsmag követ a [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) parancsmagot.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

A fenti példában ```$onlineSch``` négy elemeket egy tömb, amely tartalmazza a meglévő online védelmi ütemterv GFS fájlcsoportok a védelmi csoport:

1. ```$onlineSch[0]``` napi ütemezés tartalmazza
2. ```$onlineSch[1]``` a heti ütemezés tartalmaz
3. ```$onlineSch[2]``` havi ütemezés tartalmazza
4. ```$onlineSch[3]``` az éves ütemterv tartalmaz

Így a heti ütemezést módosítani kell, ha szükséges tekintse meg a ```$onlineSch[1]```.

### <a name="initial-backup"></a>Kezdeti biztonsági mentés
Amikor egy adatforrást először, a DPM igények biztonsági mentésével hoz létre a kezdeti replika, amely a DPM-replikakötet védeni az adatforrást teljes másolatot készít. Ezt a tevékenységet, vagy egy adott időpontra ütemezett, vagy manuálisan is elindítható, használja a [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) parancsmagot futtathatja a paraméterrel ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>A DPM-replika és a helyreállításipont-kötet méretének módosítása
Módosíthatja a méretét a DPM-replikakötet és árnyékmásolat-kötet használatával is [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) parancsmag az alábbi példában látható módon: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>A változtatások véglegesítése a védelmi csoport
Végül a módosításokat kell a DPM az új védelmi csoport konfigurációja szerint a biztonsági mentés megkezdése előtt is véglegessé válhatnak. Ez tehető a [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) parancsmagot.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>A biztonsági mentési pontok megjelenítéséhez
Használhatja a [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) parancsmagot, hogy egy adatforráshoz tartozó összes helyreállítási pontok listájának beolvasása. Ebben a példában a következő történik:

* az összes PGs beolvasni a DPM-kiszolgálón és a egy tömbben található ```$PG```
* a megfelelő adatforrások beolvasása az ```$PG[0]```
* a helyreállítási pontok beolvasása egy adatforrás esetében.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Állítsa vissza az adatok védelme az Azure-ban
Adatok visszaállítása kombinációja egy ```RecoverableItem``` objektum és a egy ```RecoveryOption``` objektum. Az előző szakaszban a biztonsági mentési pontok listáját egy adatforráshoz tartozó fájt.

Az alábbi példában bemutatjuk, hogyan egy Hyper-V virtuális gépek visszaállítása az Azure Backup biztonsági mentési ponthoz és a helyreállítási cél együttes. Ebben a példában a következőket tartalmazza:

* Egy helyreállítási lehetőség használatával létrehozása a [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) parancsmagot.
* Segítségével biztonsági mentési pontok tömbjének beolvasása a ```Get-DPMRecoveryPoint``` parancsmagot.
* A visszaállítandó egy biztonsági mentési pont kiválasztása.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

A parancsok könnyen bővíthető datasource bármilyen.

## <a name="next-steps"></a>További lépések
* További információ az Azure Backup a DPM: [Bevezetés a DPM biztonsági mentése](backup-azure-dpm-introduction.md)
