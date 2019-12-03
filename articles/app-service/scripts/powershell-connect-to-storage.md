---
title: 'PowerShell: Kapcsolódás egy Storage-fiókhoz'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta bemutatja, hogyan csatlakoztatható egy alkalmazás egy Storage-fiókhoz.
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: d1c8951445c0be6a3d4b9c4763d719fc7d79d2d5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685331"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>App Service-alkalmazás összekötése egy Storage-fiókkal

Ebből a forgatókönyvből megtudhatja, hogyan hozhat létre egy Azure Storage-fiókot és egy App Service alkalmazást. Ezután az alkalmazás beállításainak használatával összekapcsolja a Storage-fiókot az alkalmazással.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect an app to a storage account")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után a következő paranccsal távolíthatja el az erőforráscsoportot, App Service alkalmazást és az összes kapcsolódó erőforrást.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [Új – AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy App Service alkalmazást. |
| [Új – AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Létrehoz egy tárfiókot. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Beszerzi egy Azure Storage-fiók hozzáférési kulcsait. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy App Service alkalmazás konfigurációját. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A Azure App Service további Azure PowerShell-mintákat talál a [Azure PowerShell mintákban](../samples-powershell.md).
