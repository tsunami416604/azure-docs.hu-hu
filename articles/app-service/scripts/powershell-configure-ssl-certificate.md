---
title: 'PowerShell: SSL feltöltése és kötése'
description: Megtudhatja, hogyan automatizálhatja az Azure PowerShellt az App Service üzembe helyezésének és felügyeletének automatizálására. Ez a minta bemutatja, hogyan köthető egy egyéni SSL-tanúsítvány egy alkalmazáshoz.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: 8422c4ef29366888a2bdd53c18462e6cfb680c78
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80044569"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app-using-powershell"></a>Egyéni SSL-tanúsítvány kötése webalkalmazáshoz a PowerShell használatával

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd hozzáköti egy egyéni tartománynév SSL-tanúsítványát. 

Ha szükséges, telepítse az Azure PowerShell-t az [Azure PowerShell-útmutatóban](/powershell/azure/overview)található utasítás használatával, majd futtassa `Connect-AzAccount` a kapcsolatot az Azure-ral. Emellett győződjön meg a következőkről is:

- Ki lett építve kapcsolat az Azure-ral az `az login` parancs használatával.
- Hozzáfér a tartományregisztrálója DNS-konfigurációs oldalához.
- Rendelkezik egy érvényes .PFX-fájllal, valamint a kapcsolódó jelszóval a feltölteni és hozzákötni kívánt SSL-tanúsítványhoz.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

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
| [Új-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [Új-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazást hoz létre. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Egy App Service-csomag tarifacsomagját módosítja. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy webalkalmazás konfigurációját. |
| [Új-AzWebAppSSL Kötés](/powershell/module/az.websites/new-azwebappsslbinding) | Létrehoz egy SSL-tanúsítványkötést egy webalkalmazáshoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
