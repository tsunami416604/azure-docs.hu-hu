---
title: Azure PowerShell parancsfájl-minta – az alkalmazás biztonsági másolatának visszaállítása egy másik előfizetésre | Microsoft Docs
description: Azure PowerShell szkript minta – webalkalmazás visszaállítása egy másik előfizetésben lévő biztonsági másolatból
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 5f293f290bd0c8b6c5546d37a43dde64a5af4f82
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098405"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Webalkalmazás visszaállítása egy másik előfizetésben lévő biztonsági másolatból a PowerShell használatával

Ez a parancsfájl egy korábban befejezett biztonsági mentést kérdez le egy meglévő webalkalmazásból, és visszaállítja egy másik előfizetésben lévő webalkalmazásba. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha már nincs szüksége a webalkalmazásra, az alábbi paranccsal távolíthatja el az erőforráscsoportot, a webalkalmazást és az összes kapcsolódó erőforrást.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Felvesz egy hitelesített fiókot Azure Resource Manager parancsmag-kérelmekhez.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy webalkalmazást |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Visszaállítja egy webalkalmazást egy korábban befejezett biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
