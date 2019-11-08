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
ms.date: 10/08/2019
ms.openlocfilehash: 17252f6544ee56647315dc44ace4db3e3abe9726
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821874"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A virtuális mag-alapú vásárlási modell kiépített számítási szintjein a meglévő licenceket a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával SQL Database kedvezményes díjszabásra cserélheti fel. Ez az Azure-kedvezmény lehetővé teszi, hogy Azure SQL Database akár 30%-ot is megtakaríthat a helyszíni SQL Server-licencek frissítési garanciával való használatával.

![árképzési](./media/sql-database-service-tiers/pricing.png)


## <a name="choose-a-license-model"></a>Licencelési modell kiválasztása

A Azure Hybrid Benefit segítségével csak az alapul szolgáló Azure-infrastruktúráért kell fizetnie, ha az SQL-adatbázis motorjának meglévő SQL Server licencét használja (alapszintű számítási díjszabás), vagy a mögöttes infrastruktúráért és a SQL Serverért is fizethet. licenc (licencbe foglalt díjszabás).

A licencelési modellt a Azure Portal használatával vagy a következő API-k egyikével választhatja ki vagy módosíthatja:

- A licenc típusának beállítása vagy frissítése a PowerShell használatával:

  - [Új – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [Új – AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- A licenc típusának beállítása vagy frissítése az Azure CLI használatával:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- A licenc típusának beállítása vagy frissítése a REST API használatával:

  - [Adatbázisok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Adatbázisok – frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Felügyelt példányok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Felügyelt példányok – frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/update)



## <a name="next-steps"></a>További lépések

- A SQL Database központi telepítési lehetőségek [közül választhat: válassza a megfelelő központi telepítési lehetőséget az Azure SQL-ben](sql-database-paas-vs-sql-server-iaas.md).
- SQL Database funkciók összehasonlítását lásd: [Azure SQL Database szolgáltatások](sql-database-features.md).
