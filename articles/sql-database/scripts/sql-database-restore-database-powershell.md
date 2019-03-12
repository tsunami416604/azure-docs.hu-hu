---
title: PowerShell-példaszkript Azure SQL-adatbázis biztonsági másolatból való visszaállításához | Microsoft Docs
description: Azure PowerShell példaszkript egyetlen Azure SQL-adatbázis visszaállítása a georedundáns biztonsági másolatokból
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/07/2019
ms.openlocfilehash: ce52f677504556164f87a5b2999444e10b8abefc
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574550"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-from-backups"></a>Visszaállítás biztonsági mentésből egy önálló Azure SQL-adatbázist a PowerShell használatával

Ez a PowerShell-példaszkript visszaállít egy Azure SQL-adatbázist egy georedundáns biztonsági másolatból, és visszaállít egy törölt Azure SQL-adatbázist a legutóbbi biztonsági másolatra, vagy adott időpontra.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz AZ PowerShell 1.4.0-s vagy újabb. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. | 
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Egy SQL Database-kiszolgálót, amelyen egy önálló adatbázist vagy rugalmas készletet hoz létre. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy adatbázist egy SQL Database-kiszolgáló önálló vagy készletezett adatbázisként. |
[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Önálló vagy készletezett adatbázis georedundáns biztonsági másolatának beolvasása. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | SQL önálló vagy készletezett adatbázis visszaállítása. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Egy Azure SQL önálló vagy készletezett adatbázis eltávolítása. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Lekéri a törölt önálló vagy készletezett adatbázis, amelyet visszaállíthat. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
