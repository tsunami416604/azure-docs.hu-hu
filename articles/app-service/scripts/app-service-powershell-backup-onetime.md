---
title: "Az Azure PowerShell-parancsfájl minta - webalkalmazás biztonsági mentése |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - webalkalmazás biztonsági mentése"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: fc755f82-ca3e-4532-b251-690b699324d6
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5984a7707552b2740b48e3c9da40a5e96a3a279b
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="back-up-a-web-app"></a>Készítsen biztonsági másolatot a webes alkalmazás

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service azok kapcsolódó erőforrásait, és egyszeri biztonsági másolatot a hozza létre. 

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview), majd futtassa a `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral. 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/app-service/backup-onetime/backup-onetime.ps1?highlight=1-5 "Back up a web app")]

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
| [Új AzureStorageContainerSASToken](/powershell/module/azure.storage/new-azurestoragecontainersastoken) | Az Azure storage-tároló egy SAS-jogkivonat állít elő.  |
| [Új AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service-csomag létrehozása. |
| [Új AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Létrehoz egy webalkalmazást. |
| [Új AzureRmWebAppBackup](/powershell/module/azurerm.websites/new-azurermwebappbackup) | Másolatot hoz létre egy webalkalmazás. |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | A webalkalmazás biztonsági mentések listájának lekérése. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure App Service Web Apps további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../app-service-powershell-samples.md).
