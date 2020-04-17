---
title: 'PowerShell: TLS/SSL feltöltése és kötése'
description: Megtudhatja, hogyan automatizálhatja az Azure PowerShellt az App Service üzembe helyezésének és felügyeletének automatizálására. Ez a minta bemutatja, hogyan köthető egy egyéni TLS/SSL-tanúsítvány egy alkalmazáshoz.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: 5116585b701717a82b757cae70f938c321a1f7d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532544"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>Egyéni TLS-/SSL-tanúsítvány kötése webalkalmazáshoz a PowerShell használatával

Ez a mintaparancsfájl létrehoz egy webalkalmazást az App Service-ben a kapcsolódó erőforrásokkal, majd hozzáköti az egyéni tartománynév TLS/SSL tanúsítványát. 

Ha szükséges, telepítse az Azure PowerShell-t az [Azure PowerShell-útmutatóban](/powershell/azure/overview)található utasítás használatával, majd futtassa `Connect-AzAccount` a kapcsolatot az Azure-ral. Emellett győződjön meg a következőkről is:

- Ki lett építve kapcsolat az Azure-ral az `az login` parancs használatával.
- Hozzáfér a tartományregisztrálója DNS-konfigurációs oldalához.
- Van egy érvényes . PFX fájlt és annak jelszavát a feltölteni és kötni kívánt TLS/SSL tanúsítványhoz.

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
| [Új-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [Új-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazást hoz létre. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Egy App Service-csomag tarifacsomagját módosítja. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy webalkalmazás konfigurációját. |
| [Új-AzWebAppSSL Kötés](/powershell/module/az.websites/new-azwebappsslbinding) | TLS/SSL-tanúsítványkötést hoz létre egy webalkalmazáshoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
