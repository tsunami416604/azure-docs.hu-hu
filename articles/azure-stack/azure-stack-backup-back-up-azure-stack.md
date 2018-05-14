---
title: Készítsen biztonsági másolatot az Azure-verem |} Microsoft Docs
description: Egy igény szerinti biztonsági mentés végrehajtásához Azure veremben helyen biztonsági mentéssel.
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
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-azure-stack"></a>Készítsen biztonsági másolatot az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Egy igény szerinti biztonsági mentés végrehajtásához Azure veremben helyen biztonsági mentéssel. Ha az infrastruktúra biztonsági másolat szolgáltatás engedélyezése szüksége, tekintse meg [biztonsági mentés engedélyezése a felügyeleti portál Azure verem](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  A PowerShell-környezet konfigurálása, lásd: [verem Azure PowerShell telepítése ](azure-stack-powershell-install.md).

## <a name="start-azure-stack-backup"></a>Azure verem biztonsági mentés indítása

Nyissa meg a Windows PowerShell operátor felügyeleti környezetben egy rendszergazda jogú parancssort, majd futtassa az alábbi parancsokat:

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Erősítse meg a biztonsági mentés a felügyeleti portálon

1. Nyissa meg a verem Azure felügyeleti portálon, a [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Válassza ki **további szolgáltatások** > **infrastruktúra biztonsági mentés**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentés** panelen.
3. Keresse a **neve** és **dátuma** a biztonsági másolat **elérhető biztonsági másolatok** lista.
4. Ellenőrizze a **állapot** van **sikeres**.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>További lépések

- További információ a munkafolyamat helyreállítására, adatvesztési eseményhez. Lásd: [végzetes adatvesztés helyreállíthatók](azure-stack-backup-recover-data.md).
