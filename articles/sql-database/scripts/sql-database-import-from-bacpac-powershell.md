---
title: PowerShell-példa BACPAC-fájl importálására Azure SQL-adatbázisba | Microsoft Docs
description: Azure PowerShell-példaszkript – BACPAC-fájl importálása SQL-adatbázisba
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: load & move data
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 05/24/2019
ms.openlocfilehash: 3c90cd19c5da3d98e894105e21b5e20cf6376c21
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569959"
---
# <a name="use-powershell-to-import-a-bacpac-file-into-an-azure-sql-database"></a>BACPAC-fájl importálása egy Azure SQL-adatbázisba a PowerShell használatával

Ez a PowerShell-példaszkript egy BACPAC-fájlban lévő adatbázist importál egy Azure SQL-adatbázisba.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1?highlight=20-21 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Létrehoz egy SQL Database kiszolgáló tűzfalszabály-szabályt, amely lehetővé teszi a hozzáférést a SQL Database-kiszolgálón lévő összes önálló adatbázishoz és készletezett adatbázishoz a megadott IP-címtartomány alapján. |
| [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) | Importál egy BACPAC-fájlt, és létrehoz egy új adatbázist a kiszolgálón. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
