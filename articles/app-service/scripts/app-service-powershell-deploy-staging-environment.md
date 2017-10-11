---
title: "Az Azure PowerShell-parancsfájl minta - webalkalmazás létrehozása és kód telepítése az átmeneti környezetben való használatra |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - webalkalmazás létrehozása és kód telepítése az átmeneti környezetben való használatra"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 55adc13350eb0f4711efa3c901f6e4e7755dfb27
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Webalkalmazás létrehozása, majd kód telepítése az átmeneti környezetben való használatra

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service egy további üzembe helyezési pont "tesztelés" nevű, és ezután telepíti egy mintaalkalmazást az "előkészítési" pont.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview), majd futtassa a `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[fő](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "webalkalmazás létrehozása, majd kód telepítése az átmeneti környezetben való használatra")]

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
| [Új AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service-csomag létrehozása. |
| [Új AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Létrehoz egy webalkalmazást. |
| [Set-AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Módosítja egy App Service-csomag a tarifacsomag-váltáshoz. |
| [Új AzureRmWebAppSlot](/powershell/module/azurerm.websites/new-azurermwebappslot) | Létrehoz egy üzembe helyezési pont egy webalkalmazás. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Módosítja egy erőforrás egy erőforráscsoportban. |
| [Lapozófájl-kapacitás-AzureRmWebAppSlot](/powershell/module/azurerm.websites/swap-azurermwebappslot) | A webes alkalmazás üzembe helyezési pont cseréje éles környezetben. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure App Service Web Apps további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../app-service-powershell-samples.md).
