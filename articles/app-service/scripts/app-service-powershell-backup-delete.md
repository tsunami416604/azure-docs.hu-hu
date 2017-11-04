---
title: "Az Azure PowerShell-parancsfájl minta - webalkalmazás biztonsági mentése törlése |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - webalkalmazás biztonsági mentése törlése"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f4204cbb4aefe590b87d0a72675823321f280f33
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="delete-a-backup-for-a-web-app"></a>A webalkalmazás biztonsági

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service azok kapcsolódó erőforrásait, és egyszeri biztonsági másolatot a hozza létre. 

A parancsfájl futtatásával szüksége meglévő biztonsági mentésből egy webalkalmazás. Szeretne létrehozni egyet, lásd: [kész a webalkalmazás biztonsági mentési](app-service-powershell-backup-onetime.md) vagy [ütemezett biztonsági mentés egy webalkalmazás létrehozása](app-service-powershell-backup-scheduled.md).

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot, a web app és az összes kapcsolódó erőforrások.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | A webalkalmazás biztonsági mentések listájának lekérése. |
| [Remove-AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Eltávolítja a megadott biztonsági egy webalkalmazás. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure App Service Web Apps további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../app-service-powershell-samples.md).
