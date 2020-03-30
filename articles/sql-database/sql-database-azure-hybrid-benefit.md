---
title: Azure Hybrid Benefit
description: Az SQL Database-engedményekhez használja a meglévő SQL Server licenceket.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945620"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A virtuálismag-alapú vásárlási modell kiépített számítási rétegében az Azure Hybrid Benefit for SQL Server használatával kicserélheti a meglévő licenceket az SQL Database kedvezményes [díjaira.](https://azure.microsoft.com/pricing/hybrid-benefit/) Ez az Azure-előny lehetővé teszi, hogy akár 30 százalékkal vagy még magasabbra mentsen az Azure SQL Database-ben a helyszíni SQL Server-licencek frissítési garanciával történő használatával. Kérjük, használja az Azure Hybrid Benefit Kalkulátor a fent említett hivatkozás használatával a helyes értékeket. 

> [!NOTE]
> Az Azure Hybrid Benefit szolgáltatásra való váltáshoz nincs szükség állásidőre.

![Árképzés](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Licencmodell kiválasztása

Az Azure Hybrid Benefit segítségével dönthet úgy, hogy csak az alapul szolgáló Azure-infrastruktúráért fizet magának az SQL-adatbázismotornak a meglévő SQL Server licencével (Base Compute díjszabás), vagy fizethet az alapul szolgáló infrastruktúráért és az SQL Serverért is. licenc (licenccel együtt adott árképzés).

Az Azure Portalon vagy az alábbi API-k egyikén választhatja ki vagy módosíthatja a licencelési modellt:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A licenctípus beállítása vagy frissítése a PowerShell használatával:

- [Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase Adatbázis](/powershell/module/az.sql/set-azsqldatabase)
- [Új-Azsqlinstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A licenctípus beállítása vagy frissítése az Azure CLI használatával:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi létrehozása](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi frissítés](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

A licenctípus beállítása vagy frissítése a REST API használatával:

- [Adatbázisok – létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate)
- [Adatbázisok - Frissítés](/rest/api/sql/databases/update)
- [Felügyelt példányok – létrehozás vagy frissítés](/rest/api/sql/managedinstances/createorupdate)
- [Felügyelt példányok – frissítés](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>További lépések

- Az SQL Database telepítési beállításai közül a [Megfelelő telepítési lehetőség kiválasztása az Azure SQL-ben című témakörben](sql-database-paas-vs-sql-server-iaas.md)talál.
- Az SQL Database szolgáltatásainak összehasonlítását az [Azure SQL Database Features című témakörben található.](sql-database-features.md)
