---
title: "Az Azure PowerShell-parancsfájl minta - webes alkalmazás csatlakoztatása SQL-adatbázishoz |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - webes alkalmazás csatlakoztatása SQL-adatbázishoz"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 5312bf6b81d1cc48490b71c3f77323cca23e1559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-web-app-to-a-sql-database"></a>Webes alkalmazás csatlakoztatása SQL-adatbázishoz

Ebben a forgatókönyvben, megtudhatja, hogyan Azure SQL-adatbázis és az Azure-webalkalmazás létrehozása. Az SQL-adatbázis majd összekapcsolja a webes alkalmazás alkalmazás-beállításokkal.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview), majd futtassa a `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect a web app to a SQL database")]

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
| [Új AzureRMSQLServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Létrehoz egy SQL Database-kiszolgálóhoz. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | SQL adatbázis-kiszolgálót egy tűzfalszabályt hoz létre. |
| [Új-AzureRMSQLDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Létrehoz egy adatbázist vagy egy olyan rugalmas adatbázis. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | A webalkalmazás konfigurációs módosítja. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure App Service Web Apps további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../app-service-powershell-samples.md).
