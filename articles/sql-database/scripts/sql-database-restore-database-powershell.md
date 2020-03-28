---
title: PowerShell-példa-visszaállítás-backup-Azure SQL-adatbázis
description: Azure PowerShell példaparancsfájl az Azure SQL egyetlen adatbázisának egy korábbi időpontra történő visszaállításához az automatikus biztonsági mentések ből
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: da4236e138bd75237ca10b85dc1586fecd1cece4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691558"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-to-an-earlier-point-in-time"></a>Az Azure SQL egyetlen adatbázisának visszaállítása a PowerShell használatával egy korábbi időpontra

Ez a PowerShell-parancsfájl-példa visszaállítja az Azure SQL-adatbázist egy adott időpontra.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az AZ PowerShell 1.4.0-s vagy újabb szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoport és a hozzá tartozó összes erőforrás eltávolításához használja a következő parancsot.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új-AzsqlServer](/powershell/module/az.sql/new-azsqlserver) | Egyetlen adatbázist vagy rugalmas készletet tartalmazó SQL Database-kiszolgálót hoz létre. |
| [Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Adatbázist hoz létre az SQL Database-kiszolgálón önálló vagy készletbe adott adatbázisként. |
[Get-AzSqlDatabaseGeobackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Egy önálló vagy készletezésű adatbázis georedundáns biztonsági mentésének leése. |
| [Visszaállítás-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Egy önálló VAGY készletbe adott SQL-adatbázis visszaállítása. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Eltávolítja az Azure SQL önálló vagy készletbe adott adatbázis. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Leállít egy törölt önálló vagy készletezetlen adatbázist. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
