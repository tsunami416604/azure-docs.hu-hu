---
title: PowerShell-példaszkript egyetlen Azure SQL-adatbázis monitorozásához és skálázásához | Microsoft Docs
description: Azure PowerShell-példaszkript – Egyetlen Azure SQL-adatbázis monitorozása és skálázása
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: 28ab02521077224d3c938bb7e222047f91782409
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390955"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-sql-database"></a>Egyetlen SQL-adatbázis monitorozása és skálázása a PowerShell használatával

Ez a PowerShell-példaszkript egy adatbázis teljesítmény-mérőszámait monitorozza, egy magasabb számítási mérethez skálázza az adatbázist, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=13-14 "Monitor and scale single SQL Database")]

> [!TIP]
> Használja a [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) parancsot az adatbázis-műveletek állapotának lekéréséhez, valamint a [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity) parancsot az adatbázis frissítési műveletének visszavonásához.

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Olyan logikai kiszolgálót hoz létre, amely egy adatbázist vagy rugalmas készletet üzemeltet. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | Megjeleníti az adatbázis méretkihasználtsági adatait.|
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Frissíti az adatbázis tulajdonságait vagy az adatbázist egy rugalmas készletbe, egy rugalmas készletből vagy rugalmas készletek között helyezi át. |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Beállít egy riasztási szabályt a DTU-k jövőbeni automatikus monitorozásához. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
