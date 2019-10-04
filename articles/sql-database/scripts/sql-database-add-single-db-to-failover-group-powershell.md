---
title: PowerShell-példa – feladatátvételi csoport – Azure SQL Database önálló adatbázis | Microsoft Docs
description: Azure PowerShell példa parancsfájlt egy Azure SQL Database önálló adatbázis létrehozásához, a feladatátvételi csoporthoz való hozzáadásához és a feladatátvételi teszthez.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 1f975eb11e546a0ece862ac239ac4321ed8e4110
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570078"
---
# <a name="use-powershell-to-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Azure SQL Database önálló adatbázis hozzáadása feladatátvételi csoporthoz a PowerShell használatával 

Ez a PowerShell-parancsfájl egy önálló adatbázist hoz létre, létrehoz egy feladatátvételi csoportot, hozzáadja az adatbázist, és teszteli a feladatátvételt. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaszkriptek

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása logikai kiszolgálóhoz. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy új Azure SQL Database önálló adatbázist. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Létrehoz egy új feladatátvételi csoportot. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Egy vagy több SQL-adatbázis beolvasása. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis egy feladatátvételi csoportba való hozzáadására szolgál. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy felsorolja Azure SQL Database feladatátvételi csoportokat. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database feladatátvételi csoport feladatátvételét hajtja végre. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása | 

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
