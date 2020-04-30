---
title: 'PowerShell: méretezés Traffic Manager'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a példa azt mutatja be, hogyan méretezhető egy alkalmazás világszerte a Traffic Manager használatával.
tags: azure-service-management
ms.assetid: 470f0129-1efe-462c-a029-5c66e04158a8
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 6ffcf22b640d6393ed48d803f6f52bac5334d4b7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74684327"
---
# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>Webalkalmazások globális skálázása magas rendelkezésre állású architektúrával

Ebben a forgatókönyvben egy erőforráscsoportot, két App Service-csomagot, két webalkalmazást, egy Traffic Manager-profilt és két Traffic Manager-végpontot fog létrehozni. A gyakorlat végrehajtása után egy magas rendelkezésre állású architektúrával rendelkezik majd, amely a legkisebb hálózati késéssel biztosítja a webalkalmazás globális rendelkezésre állását.

Szükség esetén telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/overview)található utasítással, majd futtassa a parancsot `Connect-AzAccount` az Azure-hoz való kapcsolódáshoz.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/scale-geographic/scale-geographic.ps1 "Scale a web app worldwide with a high-availability architecture")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a webalkalmazás és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Létrehoz egy Traffic Manager-profilt. |
| [Új – AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [Új – AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazást hoz létre. |
| [Új – AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Létrehoz egy végpontot egy Traffic Manager-profilban. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
