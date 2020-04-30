---
title: 'PowerShell: a TLS/SSL feltöltése és kötése'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta bemutatja, hogyan köthető egy egyéni TLS/SSL-tanúsítvány egy alkalmazáshoz.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: 5116585b701717a82b757cae70f938c321a1f7d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81532544"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>Egyéni TLS/SSL-tanúsítvány kötése egy webalkalmazáshoz a PowerShell használatával

Ez a minta parancsfájl egy webalkalmazást hoz létre App Service a kapcsolódó erőforrásokkal együtt, majd egy egyéni tartománynév TLS/SSL-tanúsítványát köti hozzá. 

Szükség esetén telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/overview)található utasítással, majd futtassa a parancsot `Connect-AzAccount` az Azure-hoz való kapcsolódáshoz. Emellett győződjön meg a következőkről is:

- Ki lett építve kapcsolat az Azure-ral az `az login` parancs használatával.
- Hozzáfér a tartományregisztrálója DNS-konfigurációs oldalához.
- Érvényes. PFX-fájl és a feltölteni és kötni kívánt TLS/SSL-tanúsítvány jelszava.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

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
| [Új – AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [Új – AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazást hoz létre. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Egy App Service-csomag tarifacsomagját módosítja. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy webalkalmazás konfigurációját. |
| [Új – AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Létrehoz egy TLS/SSL-tanúsítvány kötését egy webalkalmazáshoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
