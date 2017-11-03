---
title: "Az Azure PowerShell-parancsfájl minta - kötés egy egyéni az SSL-tanúsítványt egy |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - kötés egy egyéni SSL-tanúsítvány"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 851b172cd9218c9ade692e4c9e50a59b4b677ac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Egyéni SSL-tanúsítvány kötése a webes alkalmazás

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service azok kapcsolódó erőforrásait, majd az SSL-tanúsítvány egy egyéni tartománynév kötődik. 

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview), majd futtassa a `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral. Emellett győződjön meg arról, hogy:

- A kapcsolat az Azure használatával lett létrehozva a `az login` parancsot.
- Rendelkezik hozzáféréssel a tartományregisztráló DNS-konfiguráció lapon.
- Lehetősége van egy érvényes. PFX-fájlt és a jelszót, az SSL-tanúsítvány feltöltése és kötni kívánt.

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

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
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | A webalkalmazás konfigurációs módosítja. |
| [Új AzureRmWebAppSSLBinding](/powershell/module/azurerm.websites/new-azurermwebappsslbinding) | Létrehoz egy SSL-tanúsítvány kötésének egy webalkalmazást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure App Service Web Apps további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../app-service-powershell-samples.md).
