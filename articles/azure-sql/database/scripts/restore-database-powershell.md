---
title: 'PowerShell: adatbázis automatikus biztonsági másolatának visszaállítása SQL Database'
description: Azure PowerShell-parancsfájl használatával visszaállíthat egy adatbázist a SQL Database egy korábbi időpontra az automatikus biztonsági mentésből.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: 321c0ee7448a977366ac727c9435a88685a6a5c7
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196806"
---
# <a name="use-powershell-to-restore-a-database-to-an-earlier-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra a PowerShell használatával

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez a PowerShell-parancsfájl egy adott időpontra visszaállítja SQL Database adatbázisát.  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az az PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Adatbázisokat és rugalmas készleteket futtató kiszolgálót hoz létre. |
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Adatbázist hoz létre egy kiszolgálón. |
| [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Egy önálló vagy készletezett adatbázis geo-redundáns biztonsági mentését kéri le. |
| [Visszaállítás – AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Visszaállítja az adatbázist. |
| [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Eltávolít egy adatbázist. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentáció](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../powershell-script-content-guide.md).
