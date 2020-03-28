---
title: 'PowerShell: Csatlakozás az SQL-adatbázishoz'
description: Megtudhatja, hogyan automatizálhatja az Azure PowerShellt az App Service üzembe helyezésének és felügyeletének automatizálására. Ez a minta bemutatja, hogyan csatlakoztathat egy alkalmazást egy SQL-adatbázishoz.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: bb5fc8bcc99a4439276f53325c029635143c86c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74685385"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>App Service-alkalmazás csatlakoztatása SQL-adatbázishoz

Ebben a forgatókönyvben megtudhatja, hogyan hozhat létre egy Azure SQL-adatbázist és egy App Service-alkalmazást. Ezután az ALKALMAZÁS beállításaival csatolja az SQL-adatbázist az alkalmazáshoz.

Ha szükséges, telepítse az Azure PowerShell-t az [Azure PowerShell-útmutatóban](/powershell/azure/overview)található utasítás használatával, majd futtassa `Connect-AzAccount` a kapcsolatot az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájlminta futtatása után a következő parancs használható az erőforráscsoport, az App Service-alkalmazás és az összes kapcsolódó erőforrás eltávolítására.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [Új-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy App Service-alkalmazást. |
| [Új-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL-adatbáziskiszolgálót. |
| [Új-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Létrehoz egy tűzfalszabályt egy SQL-adatbáziskiszolgáló számára. |
| [Új-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy adatbázist vagy egy rugalmas adatbázist. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Módosítja az App Service-alkalmazás konfigurációját. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure PowerShell-minták az Azure App Service az [Azure PowerShell-mintákban](../samples-powershell.md)találhatók.
