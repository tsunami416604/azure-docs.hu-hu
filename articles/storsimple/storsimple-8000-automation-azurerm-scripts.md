---
title: "Azure Resource Manager parancsfájlok segítségével kezelheti a StorSimple eszközökhöz |} Microsoft Docs"
description: "Azure Resource Manager-parancsfájlok használata a StorSimple feladatok automatizálásához"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 4bb6becd0b664b9287a1973d5221cff46dca57da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Azure Resource Manager SDK-alapú parancsprogramok segítségével kezelheti a StorSimple eszközökhöz

Ez a cikk ismerteti, hogyan Azure Resource Manager SDK-alapú parancsprogramok segítségével kezelheti a StorSimple 8000 series eszköz. Egy minta parancsfájlt is megtalálhatók lépésről-lépésre a parancsfájlok futtatásához a környezet konfigurálásának lépésein.

Ez a cikk a StorSimple 8000 sorozat eszközeire csak az Azure portálon futó vonatkozik.

## <a name="sample-scripts"></a>Mintaszkriptek

A következő minta parancsfájlokat StorSimple különböző feladatok automatizálására érhetők el.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Az Azure Resource Manager SDK-alapú mintaparancsfájlok tábla

| Az Azure erőforrás-kezelő parancsfájl                    | Leírás                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Engedélyezi ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | A parancsfájl lehetővé teszi a szolgáltatásadat-titkosítási kulcs módosítása a StorSimple eszköz hitelesítése.                                                                                                           |
| [Hozzon létre StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Ez a parancsfájl egy 8010 vagy 8020-as modellt StorSimple felhő készüléknek hoz létre. A felhő készülék majd konfigurálhatók és regisztrálni a StorSimple adatokat kezelő szolgáltatással.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Ezt a parancsfájlt hoz létre, vagy módosítja a StorSimple-köteteket.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Ez a parancsfájl egy eszköz számára a StorSimple Device Manager szolgáltatásban regisztrált összes biztonsági mentés sorolja fel.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Ez a parancsfájl a biztonsági mentési házirendek beállítása a StorSimple eszközhöz.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Ez a parancsfájl lekérdezi a StorSimple futó összes feladatot a StorSimple Device Manager szolgáltatásban.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Ez a parancsfájl megvizsgálja a frissítési kiszolgálót, és lehetővé teszi, hogy tudja, ha frissítések érhetők el a StorSimple eszköz telepítése.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Ezt a parancsfájlt a StorSimple eszközt a rendelkezésre álló frissítéseket telepíti.                                                                                                                                           |
| [Kezelése CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Ezt a parancsfájlt egy manuális felhő-pillanatfelvételt elindul, és törli a megadott megőrzési napnál régebbi felhőbeli pillanatképeket.                                                                                                   |
| [A figyelő-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Az Azure Automation-Runbook PowerShell-parancsfájl a jelentés az összes biztonsági mentési feladat állapota.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Ez a parancsfájl egy adott biztonsági mentési objektum törlése.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Ezt a parancsfájlt a StorSimple eszköz manuális biztonsági mentés elindul.                                                                                                                                       |
| [Frissítés-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | A parancsfájl a szolgáltatásadat-titkosítási kulcs a 8010-es/8020-as modellt StorSimple felhő készülékek a StorSimple eszköz Manager szolgáltatásban regisztrált összes frissíti.                                     |
| [Győződjön meg arról BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Ezt a parancsfájlt a hiányzó biztonsági mentések kiemeli a biztonsági mentési házirendek társított összes ütemezést elemzése után. Azt is ellenőrzi a biztonságimásolat-katalógus elérhető biztonsági másolatok listáját.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurálni és futtatni egy parancsfájlt

Ez a szakasz példa parancsfájl vesz igénybe, és a különböző, a parancsfájl futtatásához szükséges lépéseket részletezi.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

*   Az Azure PowerShell telepítése. Azure PowerShell-modulok telepítése:
    * Egy Windows-környezetben, kövesse a [telepítse és konfigurálja az Azure Powershellt](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0). Telepíthető Azure PowerShell a Windows Server-állomáson a StorSimple Ha egyikének használatával.
    * Linux- vagy MacOS környezetben, kövesse a [telepítése és konfigurálása az Azure PowerShell MacOS vagy Linux](https://docs.microsoft.com/en-us/powershell/azure/install-azurermps-maclinux?view=azurermps-4.4.0).

Azure PowerShell használatával kapcsolatos további információkért látogasson el [Ismerkedés az Azure PowerShell használatával](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps?view=azurermps-4.4.0).

### <a name="run-azure-powershell-script"></a>Azure PowerShell-parancsfájl futtatása

Ebben a példában használt parancsfájl felsorolja a StorSimple eszköz futó összes feladatot. Ez magában foglalja a feladatok sikeres, sikertelen, és jelenleg folyamatban vannak. A következő lépésekkel töltse le és futtassa a parancsfájlt.

1. Indítsa el az Azure PowerShellt. Hozzon létre egy új mappát, és módosítsa a könyvtárat az új mappába.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Töltse le a NuGet CLI](http://www.nuget.org/downloads) alapján az előző lépésben létrehozott mappába. Különböző verziói _nuget.exe_. Válassza ki a megfelelő az SDK verziót. Minden egyes letöltési hivatkozás mutat közvetlenül egy _.exe_ fájlt. Ügyeljen arra, hogy kattintson a jobb gombbal, és mentse a fájlt a számítógépre, hanem a böngésző történő futtatásával.

    Töltse le, és tárolja a parancsfájl ugyanabban a mappában, amelyet korábban hozott létre a következő parancsot is futtathatja.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. A függő SDK letöltése.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. A parancsprogram letölthető a GitHub mintaprojektet.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Futtassa a szkriptet. Amikor a rendszer kéri, hitelesítéshez, az Azure hitelesítő adatok megadása. Ez a parancsfájl kimeneti kell a StorSimple eszköz futó összes feladatot szűrt listája.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Példa kimenet

A következő kimeneti számára jelenik meg a minta-parancsprogram futtatásakor. A kimenet tartalmazza a regisztrált eszközön, amely a 2017. szeptember 25 elkezdődött és befejeződött 2017. október 2 által futtatott feladatok.

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


## <a name="next-steps"></a>Következő lépések

[Kezelheti a StorSimple eszköz StorSimple az Eszközkezelő szolgáltatás](storsimple-8000-manager-service-administration.md).