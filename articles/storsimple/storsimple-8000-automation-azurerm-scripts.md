---
title: Azure Resource Manager-parancsfájlok használata a StorSimple-eszközök kezeléséhez |} A Microsoft Docs
description: Azure Resource Manager-parancsfájlok használata a StorSimple-feladatok automatizálása
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: d9c428e3fa8d9fe964b83ae345bb70fd49a6ce1a
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215303"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>StorSimple-eszközök kezelése az Azure Resource Manager SDK-alapú parancsprogramok használatával

Ez a cikk azt ismerteti, hogyan Azure Resource Manager SDK-alapú parancsprogramok kezelése a StorSimple 8000 sorozatú eszköz segítségével. Egy mintaszkriptet végigvezeti a lépéseken, ezek a parancsfájlok futtatásához a környezet konfigurálása is tartalmazza.

Ez a cikk csak az Azure Portalon a StorSimple 8000 sorozatú eszközök vonatkozik.

## <a name="sample-scripts"></a>Mintaszkriptek

Az alábbi minta parancsfájlok StorSimple különböző feladatok automatizálására érhetők el.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Azure SDK-t Resource Manager-alapú mintaszkriptek táblája

| Az Azure Resource Manager-szkript                    | Leírás                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Engedélyezés ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Ez a szkript lehetővé teszi, hogy módosítani a szolgáltatásadat-titkosítási kulcsot a StorSimple-eszköz engedélyezése.                                                                                                           |
| [Hozzon létre StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Ez a szkript létrehoz egy 8010-es vagy 8020-as StorSimple Cloud Appliance. A felhőalapú berendezés majd konfigurálhatók és regisztrálta a StorSimple Data Manager szolgáltatásban.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Ez a szkript létrehozza vagy módosítja a StorSimple-köteteket.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Ez a szkript egy eszközt regisztrálta a StorSimple-Eszközkezelő szolgáltatásban az összes biztonsági mentés sorolja fel.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Ez a parancsfájl az összes biztonsági mentési szabályzatok a StorSimple eszköz.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Ez a szkript beolvassa a StorSimple futó összes feladatot a StorSimple-Eszközkezelő szolgáltatásban.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Ez a szkript ellenőrzi a frissítési kiszolgálót, és azt jelzi, ha frissítések érhetők el a StorSimple eszközön való telepítéséről.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Ez a szkript a StorSimple eszközön a rendelkezésre álló frissítéseket telepíti.                                                                                                                                           |
| [CloudSnapshots.ps1 kezelése](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Ez a szkript elindít egy manuális felhőbeli pillanatkép, és törli a felhőbeli pillanatképekkel megadott megőrzési napnál régebbi.                                                                                                   |
| [A figyelő-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Az Azure Automation-forgatókönyv PowerShell-parancsfájl az összes biztonsági mentési feladatok állapotának jelentések.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Ez a szkript egyetlen biztonsági mentési objektum törlése.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Ez a szkript elindítja a StorSimple-eszköz manuális biztonsági mentés.                                                                                                                                       |
| [Frissítés-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Ez a szkript frissíti a szolgáltatásadat-titkosítási kulcs az összes a 8010/8020 StorSimple Cloud Appliance regisztrálta a StorSimple-Eszközkezelő szolgáltatásban.                                     |
| [Győződjön meg arról BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Ez a szkript a hiányzó biztonsági mentések minden biztonsági mentési szabályzatok ütemezés elemzése után emeli ki. Azt is ellenőrzi a biztonságimásolat-katalógus elérhető biztonsági másolatok listáját.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurálása és a egy minta parancsfájl futtatása

Ez a szakasz példa parancsfájl vesz igénybe, és részletesen ismerteti a különféle lépéseit, a parancsfájl futtatásához szükséges.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

*   Az Azure PowerShell telepítve van. Azure PowerShell-modulok telepítéséhez:
    * Windows környezet esetén kövesse a [telepítse és konfigurálja az Azure Powershellt](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0). Telepíthető Azure PowerShell-lel a Windows Server-állomás a storsimple-höz készült egyik használatakor.
    * Linux vagy MacOS rendszeren környezet esetén kövesse a [telepítése és konfigurálása az Azure PowerShell MacOS vagy Linux rendszeren](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux?view=azurermps-4.4.0).

Azure PowerShell-lel kapcsolatos további információkért látogasson el [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.4.0).

### <a name="run-azure-powershell-script"></a>Azure PowerShell-parancsprogram futtatása

Az ebben a példában használt szkriptet egy StorSimple eszközön a feladatok sorolja fel. Ez magában foglalja a feladatok sikeres, sikertelen vagy folyamatban van. Hajtsa végre az alábbi lépéseket, töltse le és futtassa a szkriptet.

1. Indítsa el az Azure PowerShellt. Hozzon létre egy új mappát, és módosítsa a könyvtárat az új mappába.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Töltse le a NuGet CLI](http://www.nuget.org/downloads) az az előző lépésben létrehozott mappa alatt. Különböző verziói _nuget.exe_. Válassza ki a megfelelő az SDK-verziót. Minden egyes letöltési hivatkozást közvetlenül a mutat egy _.exe_ fájlt. Ügyeljen arra, hogy kattintson a jobb gombbal, és mentse a fájlt a számítógépre, hanem a böngészőben történő futtatásával.

    Töltse le és tárolja a parancsfájl ugyanabban a mappában, amelyet korábban hozott létre a következő parancsot is futtathatja.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Töltse le a függő SDK-t.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Töltse le a parancsfájlt a minta GitHub-projekt.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Futtassa a szkriptet. Amikor a hitelesítést kéri, adja meg Azure hitelesítő adatait. Ez a szkript a StorSimple eszközön a feladatok szűrt listáját jeleníti meg.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Példa kimenet

A következő kimenet jelenik meg a minta parancsfájl futtatásakor. A kimenet tartalmazza a feladatok egy regisztrált eszköz, amely 2017. szeptember 25-elkezdődött és befejeződött a 2017. október 2-es verzióján futott.

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

[Használja a StorSimple-Eszközkezelő szolgáltatás kezelése a StorSimple-eszköz](storsimple-8000-manager-service-administration.md).