---
title: Azure Stack biztonsági mentése |} A Microsoft Docs
description: Hajtsa végre az Azure Stacken egy igény szerinti biztonsági mentést a biztonsági mentési helyen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242886"
---
# <a name="back-up-azure-stack"></a>Készítsen biztonsági másolatot az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Hajtsa végre az Azure Stacken egy igény szerinti biztonsági mentést a biztonsági mentési helyen. A PowerShell-környezet konfigurálása, lásd: [Azure Stack PowerShell telepítése ](azure-stack-powershell-install.md). Jelentkezzen be az Azure Stack, lásd: [konfigurálja az operátor környezetet, és jelentkezzen be az Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="start-azure-stack-backup"></a>Azure Stack biztonsági mentés indítása

-AsJob változót, nyomon követheti az új biztonsági mentést elindítani a Start-AzSBackup használatával. 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>Erősítse meg a biztonsági mentés PowerShell-lel

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- Az eredmény a következő kimenet hasonlóan kell kinéznie:

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Erősítse meg a biztonsági mentés a felügyeleti portál

1. Nyissa meg az Azure Stack felügyeleti portálon található [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Válassza ki **további szolgáltatások** > **infrastruktúra biztonsági mentését**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentését** panelen.
3. Keresse meg a **neve** és **dátuma** a biztonsági mentési **elérhető biztonsági másolatok** listája.
4. Ellenőrizze a **állapot** van **sikeres**.

## <a name="next-steps"></a>További lépések

- További információ a munkafolyamat adatvesztési esemény utáni helyreállításhoz. Lásd: [végzetes adatvesztés utáni helyreállítás](azure-stack-backup-recover-data.md).