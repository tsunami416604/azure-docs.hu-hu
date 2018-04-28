---
title: Azure PowerShell-példaszkript – Webalkalmazások globális skálázása magas rendelkezésre állású architektúrával | Microsoft Docs
description: Azure PowerShell-példaszkript – Webalkalmazások globális skálázása magas rendelkezésre állású architektúrával
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 470f0129-1efe-462c-a029-5c66e04158a8
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: eb6354c4c0842045a11a6073e3d5ccd166de54be
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>Webalkalmazások globális skálázása magas rendelkezésre állású architektúrával

Ebben a forgatókönyvben egy erőforráscsoportot, két App Service-csomagot, két webalkalmazást, egy Traffic Manager-profilt és két Traffic Manager-végpontot fog létrehozni. A gyakorlat végrehajtása után egy magas rendelkezésre állású architektúrával rendelkezik majd, amely a legkisebb hálózati késéssel biztosítja a webalkalmazás globális rendelkezésre állását.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/app-service/scale-geographic/scale-geographic.ps1 "Scale a web app worldwide with a high-availability architecture")]

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
| [New-AzureRmTrafficManagerProfile](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile) | Létrehoz egy Traffic Manager-profilt. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Létrehoz egy App Service-csomagot. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Webalkalmazást hoz létre. |
| [New-AzureRmTrafficManagerEndpoint](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerendpoint) | Létrehoz egy végpontot egy Traffic Manager-profilban. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
