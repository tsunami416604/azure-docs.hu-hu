---
title: Az Azure PowerShell-Példaszkript – forgalom irányítása az alkalmazások magas rendelkezésre állású |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – forgalom irányítása az alkalmazások magas rendelkezésre állás
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: georgewallace
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 1086fe6d656db9450d84fd6971a271775f54687d
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202826"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Forgalom irányítása az alkalmazások magas rendelkezésre állás

Ez a szkript létrehoz egy erőforráscsoportot, két app service-csomagot, két webalkalmazást, egy traffic manager-profilt és két traffic manager-végpontot. A TRAFFIC Manager irányítja a forgalmat az alkalmazás egy adott régióban, az elsődleges régióban, és a másodlagos régióba, amikor az alkalmazás az elsődleges régióban nem érhető el. Előtt hajtsa végre a parancsprogramot, módosítania kell a MyWebApp, MyWebAppL1 és MyWebAppL2 értékek egyedi értékeket az Azure-ban. A szkript futtatása után az alkalmazás az elsődleges régióban, az az URL-cím mywebapp.trafficmanager.net érheti el.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy webalkalmazás, egy Traffic Manager-profil és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. Ez olyan, mint az Azure-alapú webes alkalmazás kiszolgálófarmot. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy Azure-webalkalmazást az App Service-csomag belül. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Létrehoz egy Azure-webalkalmazást az App Service-csomag belül. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Hozzáad egy végpontot egy Azure Traffic Manager-profilhoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
