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
ms.openlocfilehash: daea97c0f5ee6ef855dc50c1ed6c7934aa85a1c4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="back-up-azure-stack"></a>Készítsen biztonsági másolatot az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Egy igény szerinti biztonsági mentés végrehajtásához Azure veremben helyen biztonsági mentéssel. Ha az infrastruktúra biztonsági másolat szolgáltatás engedélyezése szüksége, tekintse meg [biztonsági mentés engedélyezése a felügyeleti portál Azure verem](azure-stack-backup-enable-backup-console.md).

## <a name="start-azure-stack-backup"></a>Azure verem biztonsági mentés indítása

Nyissa meg a Windows PowerShell egy rendszergazdai jogú parancssorba, majd futtassa a következő parancsokat:

   ```powershell
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
