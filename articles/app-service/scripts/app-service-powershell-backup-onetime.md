---
title: Azure PowerShell-példaszkript – Webalkalmazás biztonsági mentése | Microsoft Docs
description: Azure PowerShell-példaszkript – Webalkalmazás biztonsági mentése
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: fc755f82-ca3e-4532-b251-690b699324d6
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 1b10de7b102ea2e8e55d48fa9753dbe115e2016b
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325914"
---
# <a name="back-up-a-web-app"></a>Webalkalmazás biztonsági mentése

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd egyszeri biztonsági mentést hajt végre. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-onetime/backup-onetime.ps1?highlight=1-5 "Back up a web app")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a webalkalmazás és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Létrehoz egy tárfiókot. |
| [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) | Létrehoz egy Azure Storage-tárolót. |
| [New-AzureStorageContainerSASToken](/powershell/module/azure.storage/new-azurestoragecontainersastoken) | Létrehoz egy SAS-tokent egy Azure Storage-tárolóhoz.  |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Létrehoz egy App Service-csomagot. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Webalkalmazást hoz létre. |
| [New-AzureRmWebAppBackup](/powershell/module/azurerm.websites/new-azurermwebappbackup) | Biztonsági másolatot készít egy webalkalmazásról. |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
