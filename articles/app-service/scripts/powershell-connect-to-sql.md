---
title: 'PowerShell: Kapcsolódás SQL Databasehoz'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta bemutatja, hogyan csatlakoztatható egy alkalmazás egy SQL Databasehoz.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: bb5fc8bcc99a4439276f53325c029635143c86c5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685385"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>App Service-alkalmazás összekötése egy SQL-adatbázissal

Ebből a forgatókönyvből megtudhatja, hogyan hozhat létre egy Azure SQL Database-adatbázist és egy App Service alkalmazást. Ezután az alkalmazás-beállítások használatával összekapcsolja az SQL-adatbázist az alkalmazással.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

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
| [Új – AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL-adatbáziskiszolgálót. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Létrehoz egy tűzfalszabályt egy SQL-adatbáziskiszolgáló számára. |
| [Új – AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy adatbázist vagy egy rugalmas adatbázist. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja egy App Service alkalmazás konfigurációját. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A Azure App Service további Azure PowerShell-mintákat talál a [Azure PowerShell mintákban](../samples-powershell.md).
