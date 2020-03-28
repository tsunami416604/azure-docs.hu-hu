---
title: PowerShell-példa-monitor-scale-elastic készlet-Azure SQL-adatbázis
description: Azure PowerShell példaparancsfájl egy rugalmas készlet figyelésére és méretezésére az Azure SQL Database-ben
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 6ab361b67741f2b96f593d04dafbabe355fc9121
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691619"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>Rugalmas készlet figyelése és méretezése a PowerShell használatával az Azure SQL Database-ben

Ez a PowerShell-példaszkript egy rugalmas készlet teljesítmény-mérőszámait monitorozza, egy magasabb számítási mérethez skálázza a készletet, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az AZ PowerShell 1.4.0-s vagy újabb szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoport és a hozzá tartozó összes erőforrás eltávolításához használja a következő parancsot.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új-AzsqlServer](/powershell/module/az.sql/new-azsqlserver) | Egyetlen adatbázist vagy rugalmas készletet tartalmazó SQL Database-kiszolgálót hoz létre. |
| [Új-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas készletet hoz létre. |
| [Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Egyetlen adatbázist vagy adatbázist hoz létre egy rugalmas készletben. |
| [Get-Azmetric](/powershell/module/az.monitor/get-azmetric) | Megjeleníti az adatbázis méretkihasználtsági adatait.|
| [Add-AzmetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Mérőszámalapú riasztási szabályt ad hozzá vagy frissít. |
| [Set-AzSqlelasticpool](/powershell/module/az.sql/set-azsqlelasticpool) | Frissíti a rugalmas készlet tulajdonságait |
| [Add-AzmetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Beállít egy riasztási szabályt a DTU-k jövőbeni automatikus monitorozásához. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
