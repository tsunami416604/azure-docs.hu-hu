---
title: Az Azure PowerShell-Példaszkript – webalkalmazás biztonsági visszaállítása |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – webalkalmazás biztonsági visszaállítása
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6e8d950db6facacd9ae84566c6e85a14657050fe
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292164"
---
# <a name="restore-a-web-app-from-a-backup"></a>Webalkalmazás visszaállítása biztonsági másolatból

Ez a példaszkript egy korábban elvégzett biztonsági mentés beolvassa egy meglévő web Apps, és annak tartalmára felülírásával visszaállítására. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha többé már nincs szüksége a webalkalmazást, a következő paranccsal törölheti az erőforráscsoportot, a webalkalmazás, és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [Visszaállítás – AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | Webalkalmazás visszaállítása egy korábban elvégzett biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
