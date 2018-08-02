---
title: Azure PowerShell-példaszkript – Webalkalmazás csatlakoztatása SQL-adatbázishoz | Microsoft Docs
description: Azure PowerShell-példaszkript – Webalkalmazás csatlakoztatása SQL-adatbázishoz
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
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
ms.openlocfilehash: be9a1f138ce858465ba8ded85365043cd823dac1
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324745"
---
# <a name="connect-a-web-app-to-a-sql-database"></a>Webalkalmazás csatlakoztatása SQL-adatbázishoz

Ebből a forgatókönyvből megtudhatja, hogyan hozhat létre Azure SQL-adatbázist és egy Azure-webalkalmazást. Ezután alkalmazásbeállítások használatával hozzá fogja kapcsolni az SQL-adatbázist a webalkalmazáshoz.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect a web app to a SQL database")]

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
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Létrehoz egy SQL Database-kiszolgálót. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | Létrehoz egy tűzfalszabályt egy SQL Database-kiszolgáló számára. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Létrehoz egy adatbázist vagy egy rugalmas adatbázist. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Módosítja egy webalkalmazás konfigurációját. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
