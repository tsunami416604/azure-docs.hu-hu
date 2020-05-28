---
title: 'PowerShell: adatbázis másolása új kiszolgálóra'
description: Azure PowerShell példa egy adatbázis új kiszolgálóra való másolásához
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: a361ce058c9bc54ab1e669faf1ca8c9870b54969
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053740"
---
# <a name="use-powershell-to-copy-a-database-to-a-new-server"></a>Adatbázis másolása új kiszolgálóra a PowerShell használatával
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez a PowerShell-példaszkript másolatot készít egy meglévő adatbázisról egy új kiszolgálón.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="copy-a-database-to-a-new-server"></a>Adatbázis másolása új kiszolgálóra

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.ps1?highlight=20-23 "Copy database to new server")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```powershell
Remove-AzResourceGroup -ResourceGroupName $sourceresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $targetresourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Adatbázisokat és rugalmas készleteket futtató kiszolgálót hoz létre. |
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy adatbázist vagy egy rugalmas készletet. |
| [Új – AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) | Másolatot készít egy adatbázisról, amely az aktuális időpontban készített pillanatképet használja. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../powershell-script-content-guide.md).
