---
title: "Az Azure PowerShell-parancsfájl minta - webalkalmazás az ütemezett biztonsági mentés létrehozása |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - webalkalmazás az ütemezett biztonsági mentés létrehozása"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: bfc9dc5defa5644c5ede0acab1d4eae4d93edc94
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-scheduled-backup-for-a-web-app"></a>Ütemezett biztonsági mentés egy webalkalmazás létrehozása

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service azok kapcsolódó erőforrásait, és létrehozza az ütemezett biztonsági mentés. 

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview), majd futtassa a `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral. 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot, a web app és az összes kapcsolódó erőforrások.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Új-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Létrehoz egy tárfiókot. |
| [Új AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) | Létrehoz egy Azure storage-tárolót. |
| [Új AzureStorageContainerSASToken](/powershell/module/azure.storage/new-azurestoragecontainersastoken) | Az Azure storage-tároló egy SAS-jogkivonat állít elő. |
| [Új AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service-csomag létrehozása. |
| [Új AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Létrehoz egy webalkalmazást. |
| [Szerkesztés-AzureRmWebAppBackupConfiguration](/powershell/module/azurerm.websites/edit-azurermwebappbackupconfiguration) | A biztonsági mentési konfigurációt webalkalmazás szerkesztése. |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | A webalkalmazás biztonsági mentések listájának lekérése. |
| [Get-AzureRmWebAppBackupConfiguration](/powershell/module/azurerm.websites/get-azurermwebappbackupconfiguration) | Lekérdezi a webalkalmazáshoz tartozó biztonsági mentési konfigurációt. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure App Service Web Apps további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../app-service-powershell-samples.md).
