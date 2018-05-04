---
title: Az Azure PowerShell parancsfájl minta - irányíthatja a forgalmat a magas rendelkezésre állású alkalmazások |} Microsoft Docs
description: Az Azure PowerShell parancsfájl minta - irányíthatja a forgalmat a magas rendelkezésre állású alkalmazások
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 13c24c31606d99f27ed2607fec71b381160624dd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Irányíthatja a forgalmat a magas rendelkezésre állás, az alkalmazások Azure PowerShell használatával

Ezt a parancsfájlt hoz létre egy erőforráscsoportot, két app service-csomagokról, két webes alkalmazásokat, egy traffic manager-profil és két traffic manager-végpont. A TRAFFIC Manager irányítja a forgalmat a alkalmazását egy régió tartozik, az elsődleges régióban, és a másodlagos régióban, ha az elsődleges régióban az alkalmazás nem érhető el. A parancsprogram végrehajtása előtt módosítania kell a MyWebApp, MyWebAppL1 és MyWebAppL2 értékek egyedi értékeket az egész Azure. A parancsfájl futtatása után érheti el az alkalmazást az URL-cím mywebapp.trafficmanager.net az elsődleges régióban.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup1
Remove-AzureRmResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy webalkalmazás, egy Traffic Manager-profil és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service-csomag létrehozása. Ez olyan, mintha egy kiszolgálófarmon, az Azure webalkalmazás számára. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Létrehoz egy Azure webalkalmazás az App Service-csomag belül. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Létrehoz egy Azure webalkalmazás az App Service-csomag belül. |
| [Új AzureRmTrafficManagerProfile](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [Új AzureRmTrafficManagerEndpoint](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerendpoint) | Az Azure Traffic Manager-profilt ad hozzá egy végpontot. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózati PowerShell parancsfájl-példák találhatók a [Azure hálózati áttekintés dokumentáció](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
