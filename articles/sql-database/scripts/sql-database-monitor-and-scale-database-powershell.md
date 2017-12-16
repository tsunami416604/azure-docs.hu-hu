---
title: "PowerShell-példa-figyelő-skálázási-egyetlen Azure SQL-adatbázis |} Microsoft Docs"
description: "Az Azure PowerShell-példa parancsfájl figyelését, valamint egy Azure SQL-adatbázis méretezése"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 12/14/2017
ms.author: janeng
ms.openlocfilehash: 70dcac2449b18e7afef3f96817593128ca1ab315
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="use-powershell-to-monitor-and-scale-a-single-sql-database"></a>PowerShell segítségével figyelheti, és egy SQL-adatbázis méretezése

A PowerShell-mintaparancsfájl figyeli a metrikák egy adatbázis méretezi a magasabb teljesítményszintre és egy riasztási szabályt hoz létre a Teljesítményelemzési értékek egyike. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=13-14 "Monitor and scale single SQL Database")]

> [!TIP]
> Használjon [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) adatbázis műveleti állapotának beolvasása és használata [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity) való visszavonja az adatbázis frissítési művelet.

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
 [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Új AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Adatbázis vagy a rugalmas készlet tároló logikai kiszolgáló létrehozása. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | Az az adatbázis méretének használati információit jeleníti meg.|
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Frissíti az adatbázis-tulajdonságai, vagy azokat, kívüli vagy rugalmas készletek között adatbázis helyezi. |
| [Adja hozzá AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Beállítja az automatikusan figyelése a jövőben a dtu-k riasztási szabályt. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További SQL Database PowerShell parancsfájl minták megtalálhatók a [Azure SQL Database PowerShell-parancsfájlok](../sql-database-powershell-samples.md).
