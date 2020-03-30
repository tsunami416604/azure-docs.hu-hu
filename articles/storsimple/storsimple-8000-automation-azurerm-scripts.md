---
title: StorSimple-eszközök kezelése AzureRM PowerShell-parancsfájlokkal
description: Ismerje meg, hogyan automatizálhatja az Azure Resource Manager parancsfájljait a StorSimple-feladatok automatizálására
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 03a5ef49b2d58d351d882b30b5d11e4a5ba90264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471958"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>StorSimple-eszközök kezeléséhez használja az Azure Resource Manager SDK-alapú parancsfájljait

Ez a cikk azt ismerteti, hogy az Azure Resource Manager SDK-alapú parancsfájlok hogyan használhatók a StorSimple 8000 sorozatú eszköz kezeléséhez. Egy mintaparancsfájl is található, amely végigvezeti a környezet konfigurálásának lépéseit a parancsfájlok futtatásához.

Ez a cikk csak az Azure Portalon futó StorSimple 8000 sorozatú eszközökre vonatkozik.

## <a name="sample-scripts"></a>Mintaszkriptek

A következő mintaparancsfájlok állnak rendelkezésre a különböző StorSimple feladatok automatizálásához.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Az Azure Resource Manager SDK-alapú mintaparancsfájljainak táblázata

| Azure Resource Manager parancsfájl                    | Leírás                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Ez a parancsfájl lehetővé teszi, hogy engedélyezze a StorSimple-eszköz a szolgáltatás adattitkosítási kulcsának módosításához.                                                                                                           |
| [Létrehozás-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Ez a parancsfájl létrehoz egy 8010 vagy egy 8020 StorSimple cloud appliance. A felhőalapú készülék ezután konfigurálható és regisztrálható a StorSimple Data Manager szolgáltatással.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Ez a parancsfájl létrehozza vagy módosítja a StorSimple köteteket.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Ez a parancsfájl felsorolja a StorSimple Eszközkezelő szolgáltatásban regisztrált eszköz összes biztonsági mentését.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Ez a parancsfájl a StorSimple-eszköz biztonsági mentési szabályzatait.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Ez a parancsfájl leadja az összes StorSimple-feladatot a StorSimple Eszközkezelő szolgáltatáson futó összes.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Ez a parancsfájl ellenőrzi a frissítési kiszolgálót, és jelzi, hogy a frissítések telepíthetők-e a StorSimple-eszközre.                                                                                          |
| [Telepítés-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Ez a parancsfájl telepíti a rendelkezésre álló frissítéseket a StorSimple-eszközön.                                                                                                                                           |
| [Kezelés-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Ez a parancsfájl elindítja a manuális felhőbeli pillanatképet, és törli a megadott megőrzési napoknál régebbi felhőbeli pillanatképeket.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Ez az Azure Automation Runbook PowerShell-parancsfájl az összes biztonsági mentési feladat állapotát jelenti.                                                                                                              |
| [Eltávolítás-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Ez a parancsfájl egyetlen biztonsági másolat objektumot töröl.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Ez a parancsfájl elindítja a manuális biztonsági mentést a StorSimple eszközön.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Ez a parancsfájl frissíti a szolgáltatás adattitkosítási kulcsát a StorSimple Eszközkezelő szolgáltatásban regisztrált összes 8010/8020 StorSimple felhőalapú készülékhez.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Ez a parancsfájl kiemeli a hiányzó biztonsági mentéseket a biztonsági mentési házirendekhez társított összes ütemezés elemzése után. Azt is ellenőrzi a biztonsági mentési katalógus a rendelkezésre álló biztonsági mentések listáját.             |




## <a name="configure-and-run-a-sample-script"></a>Mintaparancsfájl konfigurálása és futtatása

Ez a szakasz egy példa parancsfájlt vesz fel, és részletezi a parancsfájl futtatásához szükséges különböző lépéseket.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

*   Telepítve van az Azure PowerShell. Az Azure PowerShell-modulok telepítése:
    * Windows-környezetben kövesse az [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)című témakör lépéseit. Az Azure PowerShell t telepítheti a Windows Server-gazdagépen a StorSimple számára, ha használ egyet.
    * Linux vagy MacOS környezetben kövesse az [Azure PowerShell telepítése és konfigurálása MacOS vagy Linux rendszeren](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux)című témakör lépéseit.

Az Azure PowerShell használatáról az [Azure PowerShell használatának első lépései](https://docs.microsoft.com/powershell/azure/get-started-azureps)című webhelyen talál további információt.

### <a name="run-azure-powershell-script"></a>Azure PowerShell-parancsfájl futtatása

Ebben a példában használt parancsfájl felsorolja a StorSimple-eszközön lévő összes feladatot. Ez magában foglalja a sikeres, sikertelen vagy folyamatban lévő feladatokat is. Hajtsa végre az alábbi lépéseket a parancsfájl letöltéséhez és futtatásához.

1. Indítsa el az Azure PowerShellt. Hozzon létre egy új mappát, és módosítsa a könyvtárat az új mappára.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. Töltse le a [NuGet CLI-t](https://www.nuget.org/downloads) az előző lépésben létrehozott mappában. Vannak különböző változatai _nuget.exe_. Válassza ki az SDK-nak megfelelő verziót. Minden letöltési hivatkozás közvetlenül egy _.exe_ fájlra mutat. Ne a jobb gombbal kattintson a jobb gombbal, és mentse a fájlt a számítógépre, és ne futtassa a böngészőből.

    A parancsfájl letöltéséhez és tárolásához a parancsfájl t is futtatható, amelykorábban a korábban létrehozott mappában volt.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Töltse le a függő SDK-t.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Töltse le a parancsfájlt a minta GitHub-projektből.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Futtassa a szkriptet. Amikor a rendszer hitelesítésre kéri, adja meg az Azure-hitelesítő adatokat. Ez a parancsfájl a StorSimple-eszközön lévő összes feladat szűrt listáját kell kiadnia.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Példa kimenet

A következő kimenet jelenik meg, amikor a minta parancsfájl fut. A kimenet tartalmazza az összes olyan feladatot, amely egy regisztrált eszközön futott, amely 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>További lépések

[A StorSimple Eszközkezelő szolgáltatás segítségével kezelheti a StorSimple-eszközt.](storsimple-8000-manager-service-administration.md)
