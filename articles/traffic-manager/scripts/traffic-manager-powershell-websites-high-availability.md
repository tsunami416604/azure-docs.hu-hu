---
title: Az alkalmazások HA-jának útvonalforgalma - Azure PowerShell - Traffic Manager
description: Azure PowerShell-parancsfájl-minta – Forgalom irányítása az alkalmazások magas rendelkezésre állása érdekében
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: kumudD
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: rohink
ms.openlocfilehash: 3f448683dbb210ab196e27cfd98a376429d03aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934735"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Az Azure PowerShell használatával az alkalmazások magas rendelkezésre állású forgalmat bonyolítle

Ez a parancsfájl létrehoz egy erőforráscsoportot, két alkalmazásszolgáltatási tervet, két webalkalmazást, egy forgalomkezelő profilt és két forgalomkezelő végpontot. Traffic Manager irányítja a forgalmat az alkalmazás egy régióban, mint az elsődleges régióban, és a másodlagos régióban, ha az alkalmazás az elsődleges régióban nem érhető el. A parancsfájl végrehajtása előtt a MyWebApp, a MyWebAppL1 és a MyWebAppL2 értékeket egyedi értékekre kell módosítania az Azure-ban. A parancsfájl futtatása után az elsődleges régióban lévő alkalmazást az URL-mywebapp.trafficmanager.net érheti el.

Ha szükséges, telepítse az Azure PowerShell-t az [Azure PowerShell-útmutatóban](/powershell/azure)található utasítás használatával, majd futtassa `Connect-AzAccount` a kapcsolatot az Azure-ral.

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
| [Új-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. Ez olyan, mint egy kiszolgálófarm az Azure-webalkalmazáshoz. |
| [Új-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy Azure-webalkalmazást az App Service-csomagon belül. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Létrehoz egy Azure-webalkalmazást az App Service-csomagon belül. |
| [Új-AzTrafficManagerProfil](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [Új-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Hozzáad egy végpontot egy Azure Traffic Manager-profilhoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
