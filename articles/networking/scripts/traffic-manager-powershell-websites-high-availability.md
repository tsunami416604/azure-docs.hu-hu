---
title: Hálózati forgalom irányítása a magas rendelkezésre állás érdekében – Azure PowerShell
description: Azure PowerShell parancsfájl-minta – az alkalmazások magas rendelkezésre állásának irányítására szolgáló forgalom
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
ms.openlocfilehash: 46d76a25018a68d43d11efcef162e5b4b6801572
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87030453"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-powershell"></a>Forgalom irányítása az alkalmazások magas rendelkezésre állásához – Azure PowerShell

Ez a szkript létrehoz egy erőforráscsoportot, két app Service-csomagot, két webalkalmazást, egy Traffic Manager-profilt és két Traffic Manager-végpontot. Traffic Manager a forgalmat az egyik régióban az elsődleges régióként, a másodlagos régióba irányítja, ha az elsődleges régióban lévő alkalmazás nem érhető el. A szkript végrehajtása előtt módosítania kell a MyWebApp, a MyWebAppL1 és a MyWebAppL2 értékeket az Azure-ban lévő egyedi értékekre. A szkript futtatása után az elsődleges régióban elérheti az alkalmazást az URL-mywebapp.trafficmanager.net.

Szükség esetén telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](https://docs.microsoft.com/powershell/azure/)található utasítással, majd futtassa a parancsot az `Connect-AzAccount` Azure-hoz való kapcsolódáshoz.

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

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. Ez olyan, mint egy kiszolgálófarm az Azure-webalkalmazáshoz. |
| [Új – AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy Azure-webalkalmazást a App Service tervben. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Létrehoz egy Azure-webalkalmazást a App Service tervben. |
| [Új – AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [Új – AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Hozzáad egy végpontot egy Azure Traffic Manager-profilhoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
