---
title: Azure Stack biztonsági mentése |} A Microsoft Docs
description: Hajtsa végre az Azure Stacken egy igény szerinti biztonsági mentést a biztonsági mentési helyen.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055292"
---
# <a name="back-up-azure-stack"></a>Készítsen biztonsági másolatot az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Hajtsa végre az Azure Stacken egy igény szerinti biztonsági mentést a biztonsági mentési helyen. A PowerShell-környezet konfigurálása, lásd: [Azure Stack PowerShell telepítése ](azure-stack-powershell-install.md). Jelentkezzen be az Azure Stack, lásd: [a felügyeleti portál használatával az Azure Stackben](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Azure Stack biztonsági mentés indítása

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Egy új biztonsági mentés indítása nélkül a feladat előrehaladásának nyomon követése
Használja a Start-AzSBackup azonnal elindul egy új biztonsági másolatot a nem a feladat előrehaladásának nyomon követése.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>A feladat előrehaladásának nyomon követése az Azure Stack biztonsági mentés indítása
Start-AzSBackup segítségével új biztonsági mentést elindítani a biztonsági mentési feladatok előrehaladásának nyomon követéséhez az - AsJob változóhoz.

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>Erősítse meg a biztonsági mentés befejeződött

### <a name="confirm-backup-has-completed-using-powershell"></a>Erősítse meg a biztonsági mentés befejeződött, PowerShell-lel
A következő PowerShell-parancsok segítségével győződjön meg arról, hogy a biztonsági mentés sikeresen befejeződött:

```powershell
   Get-AzsBackup
```

Az eredmény a következő kimenet hasonlóan kell kinéznie:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Erősítse meg a biztonsági mentés befejeződött, a felügyeleti portálon
Az Azure Stack felügyeleti portál használatával győződjön meg arról, hogy a biztonsági mentés sikeresen befejeződött a következő lépésekkel:

1. Nyissa meg a [Azure Stack felügyeleti portálon](azure-stack-manage-portals.md).
2. Válassza ki **további szolgáltatások** > **infrastruktúra biztonsági mentését**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentését** panelen.
3. Keresse meg a **neve** és **dátuma** a biztonsági mentési **elérhető biztonsági másolatok** listája.
4. Ellenőrizze a **állapot** van **sikeres**.

## <a name="next-steps"></a>További lépések

További információ a munkafolyamat adatvesztési esemény utáni helyreállításhoz. Lásd: [végzetes adatvesztés utáni helyreállítás](azure-stack-backup-recover-data.md).
