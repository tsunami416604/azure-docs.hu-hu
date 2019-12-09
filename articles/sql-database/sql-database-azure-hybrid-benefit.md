---
title: Azure Hybrid Benefit
description: Meglévő SQL Server licencek használata SQL Database kedvezményekhez.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 394c674da63bbda643246c3d61fb670d6ac8d1f7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928615"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A virtuális mag-alapú vásárlási modell kiépített számítási szintjein a meglévő licenceket a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával SQL Database kedvezményes díjszabásra cserélheti fel. Ez az Azure-kedvezmény lehetővé teszi, hogy Azure SQL Database akár 30%-ot is megtakaríthat a helyszíni SQL Server-licencek frissítési garanciával való használatával. 

> [!NOTE]
> A Azure Hybrid Benefitra való váltás nem igényel állásidőt.

![árképzési](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Licencelési modell kiválasztása

A Azure Hybrid Benefit segítségével csak az alapul szolgáló Azure-infrastruktúráért kell fizetnie, ha az SQL-adatbázis motorjának meglévő SQL Server licencét használja (alapszintű számítási díjszabás), vagy a mögöttes infrastruktúráért és a SQL Serverért is fizethet. licenc (licencbe foglalt díjszabás).

A licencelési modellt a Azure Portal használatával vagy a következő API-k egyikével választhatja ki vagy módosíthatja:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

A licenc típusának beállítása vagy frissítése a PowerShell használatával:

- [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

A licenc típusának beállítása vagy frissítése az Azure CLI használatával:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [az SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[REST API](#tab/rest)

A licenc típusának beállítása vagy frissítése a REST API használatával:

- [Adatbázisok – létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate)
- [Adatbázisok – frissítés](/rest/api/sql/databases/update)
- [Felügyelt példányok – létrehozás vagy frissítés](/rest/api/sql/managedinstances/createorupdate)
- [Felügyelt példányok – frissítés](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Következő lépések

- A SQL Database központi telepítési lehetőségek [közül választhat: válassza a megfelelő központi telepítési lehetőséget az Azure SQL-ben](sql-database-paas-vs-sql-server-iaas.md).
- SQL Database funkciók összehasonlítását lásd: [Azure SQL Database szolgáltatások](sql-database-features.md).
