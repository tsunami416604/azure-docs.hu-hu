---
title: 'PowerShell: az adatbázis áthelyezése rugalmas készletek között'
description: Azure PowerShell példa szkriptet arra, hogy a PowerShell használatával két rugalmas készlet között helyezze át az adatbázist a SQL Databaseba.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.reviewer: ''
ms.author: sstein
ms.date: 03/12/2019
ms.openlocfilehash: f2811fd16dbda13a98ae7e7cb3de0671b278d698
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053704"
---
# <a name="use-powershell-to-create-elastic-pools-and-move-databases-between-elastic-pools"></a>Rugalmas készletek létrehozása és adatbázisok áthelyezése rugalmas készletek között a PowerShell használatával
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez a PowerShell-parancsfájl két rugalmas készletet hoz létre, és áthelyezi az SQL Database adatbázist egy rugalmas készletből egy másik rugalmas készletbe, majd áthelyezi az adatbázist egy rugalmas készletből egyetlen adatbázis-számítási méretre.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/move-database-between-pools-and-standalone/move-database-between-pools-and-standalone.ps1?highlight=18-19 "Move database between pools")]

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
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Adatbázisokat és rugalmas készleteket futtató kiszolgálót hoz létre. |
| [Új – AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas készletet hoz létre. |
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Adatbázist hoz létre egy kiszolgálón. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Frissíti az adatbázis tulajdonságait vagy az adatbázist egy rugalmas készletbe, egy rugalmas készletből vagy rugalmas készletek között helyezi át. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../powershell-script-content-guide.md).
