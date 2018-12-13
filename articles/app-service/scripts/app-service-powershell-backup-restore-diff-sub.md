---
title: Az Azure PowerShell-Példaszkript – egy másik előfizetéshez visszaállítási alkalmazás biztonsági másolatának |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – webalkalmazás egy másik előfizetésben biztonsági visszaállítása
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6e06ddf5f8cc814fe2fa5fa48105cf6bf976d63c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53307852"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Webalkalmazás visszaállítása biztonsági másolatból egy másik előfizetésben, PowerShell-lel

Ez a példaszkript egy korábban elvégzett biztonsági mentés beolvassa egy meglévő web Apps, és annak visszaállítására egy másik előfizetésben található webalkalmazás. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha többé már nincs szüksége a webalkalmazást, a következő paranccsal törölheti az erőforráscsoportot, a webalkalmazás, és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) | Az Azure Resource Manager parancsmag-kérelmek egy hitelesített fiók hozzáadása.  |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Egy webalkalmazást hoz létre |
| [Visszaállítás – AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | Webalkalmazás visszaállítása egy korábban elvégzett biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
