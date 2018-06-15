---
title: Azure PowerShell-példaszkript – Webalkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben | Microsoft Docs
description: Azure PowerShell példaszkript – Webalkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 179e4afec2250da76fa0e7554e804d290d28c785
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31587503"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Webalkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben egy további, staging nevű üzembehelyezési ponttal együtt, majd egy mintaalkalmazást telepít a staging pontba.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

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
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Létrehoz egy App Service-csomagot. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Webalkalmazást hoz létre. |
| [Set-AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Egy App Service-csomag tarifacsomagját módosítja. |
| [New-AzureRmWebAppSlot](/powershell/module/azurerm.websites/new-azurermwebappslot) | Üzembehelyezési pontot hoz létre egy webalkalmazáshoz. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Módosít egy erőforrást egy erőforráscsoportban. |
| [Switch-AzureRmWebAppSlot](/powershell/module/azurerm.websites/switch-azurermwebappslot) | Egy webalkalmazás üzembehelyezési pontját éles környezetbe helyezi át. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
