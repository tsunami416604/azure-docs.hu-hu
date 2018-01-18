---
title: "Készítsen biztonsági másolatot az Azure-verem |} Microsoft Docs"
description: "Egy igény szerinti biztonsági mentés végrehajtásához Azure veremben helyen biztonsági mentéssel."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 955b286967ca2bc8450e8988ec16c6a5c352aa8a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="back-up-azure-stack"></a>Készítsen biztonsági másolatot az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Egy igény szerinti biztonsági mentés végrehajtásához Azure veremben helyen biztonsági mentéssel. Ha az infrastruktúra biztonsági másolat szolgáltatás engedélyezése szüksége, tekintse meg [biztonsági mentés engedélyezése a felügyeleti portál Azure verem](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Azure verem eszközök tartalmazzák a **Start-AzSBackup** parancsmag. Az eszközök telepítése, lásd: [, amelyekből megismerheti a PowerShell használatával a Azure verem](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).

## <a name="start-azure-stack-backup"></a>Azure verem biztonsági mentés indítása

Nyissa meg a Windows PowerShell operátor felügyeleti környezetben egy rendszergazda jogú parancssort, majd futtassa az alábbi parancsokat:

```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1

    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Erősítse meg a biztonsági mentés a felügyeleti portálon

1. Nyissa meg a verem Azure felügyeleti portálon, a [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Válassza ki **további szolgáltatások** > **infrastruktúra biztonsági mentés**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentés** panelen.
3. Keresse a **neve** és **dátuma** a biztonsági másolat **elérhető biztonsági másolatok** lista.
4. Ellenőrizze a **állapot** van **sikeres**.

A biztonságimásolat-készítése befejeződött a felügyeleti portálon is ellenőrizheti. Keresse meg`\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

## <a name="next-steps"></a>További lépések

- További információ a munkafolyamat helyreállítására, adatvesztési eseményhez. Lásd: [végzetes adatvesztés helyreállíthatók](azure-stack-backup-recover-data.md).
