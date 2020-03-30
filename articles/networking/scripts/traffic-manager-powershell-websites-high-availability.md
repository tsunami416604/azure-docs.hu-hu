---
title: Az alkalmazások HA-jának útvonalforgalma - Azure PowerShell - Traffic Manager
description: Azure PowerShell-parancsfájlminta – Forgalom irányítása az alkalmazások magas rendelkezésre állása esetén
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: allensu
ms.openlocfilehash: 183599fccfad1806faae3cb90de225d388b77da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74049238"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-powershell"></a>Forgalom átirányítása az alkalmazások magas rendelkezésre állása érdekében – Azure PowerShell

Ez a parancsfájl létrehoz egy erőforráscsoportot, két alkalmazásszolgáltatási tervet, két webalkalmazást, egy forgalomkezelő profilt és két forgalomkezelő végpontot. Traffic Manager irányítja a forgalmat az alkalmazás egy régióban, mint az elsődleges régióban, és a másodlagos régióban, ha az alkalmazás az elsődleges régióban nem érhető el. A parancsfájl végrehajtása előtt a MyWebApp, a MyWebAppL1 és a MyWebAppL2 értékeket egyedi értékekre kell módosítania az Azure-ban. A parancsfájl futtatása után az elsődleges régióban lévő alkalmazást az URL-mywebapp.trafficmanager.net érheti el.

Ha szükséges, telepítse az Azure PowerShell-t az [Azure PowerShell-útmutatóban](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)található utasítás használatával, majd futtassa `Connect-AzAccount` a kapcsolatot az Azure-ral.

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
