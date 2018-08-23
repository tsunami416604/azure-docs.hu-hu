---
title: PowerShell-példaszkript Azure SQL-adatbázis biztonsági másolatból való visszaállításához | Microsoft Docs
description: Azure PowerShell-példaszkript Azure SQL-adatbázis georedundáns biztonsági másolatokból történő visszaállításához
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: business continuity, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e93ef3f837d29204c6710684d4dfc6f359f572cd
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "41919390"
---
# <a name="use-powershell-to-restore-an-azure-sql-database-from-backups"></a>Azure SQL-adatbázis biztonsági másolatokból történő visszaállítása a PowerShell használatával

Ez a PowerShell-példaszkript visszaállít egy Azure SQL-adatbázist egy georedundáns biztonsági másolatból, és visszaállít egy törölt Azure SQL-adatbázist a legutóbbi biztonsági másolatra, vagy adott időpontra.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. | [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Olyan logikai kiszolgálót hoz létre, amely egy adatbázist vagy rugalmas készletet üzemeltet. | 
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Létrehoz egy adatbázist egy logikai kiszolgálón egyetlen vagy készletezett adatbázisként. |
[Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) | Lekéri egy adatbázis georedundáns biztonsági másolatát. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) | Visszaállít egy SQL-adatbázist. |
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase) | Eltávolít egy Azure SQL-adatbázist. |
| [Get-AzureRmSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
