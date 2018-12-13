---
title: Azure PowerShell-példaszkript – Egyéni SSL-tanúsítvány kötése egy webalkalmazáshoz | Microsoft Docs
description: Azure PowerShell-példaszkript – Egyéni SSL-tanúsítvány kötése egy webalkalmazáshoz
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e55d22d8a61f6830f09262445fe008a0e8ec5b9d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194096"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app-using-powershell"></a>Egyéni SSL-tanúsítvány kötése egy webalkalmazáshoz, PowerShell-lel

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd hozzáköti egy egyéni tartománynév SSL-tanúsítványát. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. Emellett győződjön meg a következőkről is:

- Ki lett építve kapcsolat az Azure-ral az `az login` parancs használatával.
- Hozzáfér a tartományregisztrálója DNS-konfigurációs oldalához.
- Rendelkezik egy érvényes .PFX-fájllal, valamint a kapcsolódó jelszóval a feltölteni és hozzákötni kívánt SSL-tanúsítványhoz.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

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
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Létrehoz egy App Service-csomagot. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Webalkalmazást hoz létre. |
| [Set-AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Egy App Service-csomag tarifacsomagját módosítja. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Módosítja egy webalkalmazás konfigurációját. |
| [New-AzureRmWebAppSSLBinding](/powershell/module/azurerm.websites/new-azurermwebappsslbinding) | Létrehoz egy SSL-tanúsítványkötést egy webalkalmazáshoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
