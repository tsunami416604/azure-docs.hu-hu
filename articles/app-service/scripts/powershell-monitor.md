---
title: Azure PowerShell szkript minta-webalkalmazások figyelése webkiszolgáló-naplókkal | Microsoft Docs
description: Azure PowerShell parancsfájl-minta – webalkalmazás figyelése webkiszolgáló-naplókkal
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5805d7cd-9e56-4eba-bd85-75b013690ff5
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: e9e376de3356a23df648f77738fd2c84cb26d97f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098300"
---
# <a name="monitor-a-web-appwith-web-server-logs"></a>Webalkalmazás figyelése webkiszolgáló-naplókkal

Ebben a forgatókönyvben létrehoz egy erőforráscsoportot, egy App Service-csomagot és egy webalkalmazást, továbbá a webalkalmazást a webkiszolgáló-naplók engedélyezésére konfigurálja. Ezután letölti a naplófájlokat áttekintésre.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1 "Monitor a web app with web server logs")]

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
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazást hoz létre. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy webalkalmazás konfigurációját. |
| [Get-AzWebAppMetric](/powershell/module/az.websites/get-azwebappmetric) | Lekéri egy webalkalmazás metrikáit. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
