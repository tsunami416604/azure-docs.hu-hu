---
title: PowerShell példa – georeplikációs feladatátvételi csoport – egyetlen Azure SQL-adatbázis | Microsoft Docs
description: Azure PowerShell példaszkript az aktív georeplikációs feladatátvételi csoport beállításához egyetlen Azure SQL-adatbázishoz és a feladatai átvételéhez.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: d8ec80f417883874796d25c2c1a427d03073080b
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390764"
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-azure-sql-database"></a>Aktív georeplikációs feladatátvételi csoport konfigurálása egyetlen Azure SQL-adatbázishoz a PowerShell használatával

Ez a PowerShell szkriptminta aktív georeplikációs feladatátvételi csoportot állít be egyetlen Azure SQL-adatbázishoz, és átviszi a feladatait az Azure SQL-adatbázis egy másodlagos replikációjára.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaszkriptek

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-database-failover-group.ps1?highlight=19-22 "Set up failover group for single database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzureRmResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Olyan logikai kiszolgálót hoz létre, amely egy adatbázist vagy rugalmas készletet üzemeltet. |
| [New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | Rugalmas készletet hoz létre egy logikai kiszolgálón. |
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Frissíti az adatbázis tulajdonságait vagy az adatbázist egy rugalmas készletbe, egy rugalmas készletből vagy rugalmas készletek között helyezi át. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary)| Létrehoz egy másodlagos adatbázist egy meglévő adatbázishoz, és elkezdi az adatok replikálását. |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)| Egy vagy több adatbázist kér le. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary)| Egy másodlagos adatbázist elsődlegessé tesz, hogy feladatátvételt kezdeményezzen.|
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) | Lekéri az Azure SQL Database és egy erőforráscsoport vagy SQL-kiszolgáló közötti georeplikációs hivatkozásokat. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) | Leállítja az adatreplikációt egy SQL-adatbázis és a megadott másodlagos adatbázis között. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/new-azurermsqldatabasefailovergroup) | Új Azure SQL Database feladatátvételi csoportot hoz létre a megadott kiszolgálókhoz. |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Kicseréli a kiszolgálók szerepköreit a feladatátvételi csoportban, és az összes másodlagos adatbázist az elsődleges szerepkörre váltja. |
| [Get-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Lekér egy adott Azure SQL Database feladatátvételi csoportot, vagy listázza a kiszolgálón lévő feladatátvételi csoportokat. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
