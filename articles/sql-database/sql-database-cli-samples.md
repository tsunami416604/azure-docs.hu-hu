---
title: Azure CLI-példaszkriptek az SQL Database-hez | Microsoft Docs
description: Az Azure CLI-példaszkriptek bemutatják Azure SQL-adatbáziskiszolgálók, rugalmas készletek, adatbázisok és tűzfalak létrehozásának és felügyeletének folyamatát.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 5366fb1d32020bfbcfaba36c60c0eb5441e92070
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71055248"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-minták az Azure SQL Database-hez

Azure SQL Database konfigurálható az <a href="/cli/azure">Azure CLI</a>-vel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="single-database--elastic-poolstabsingle-database"></a>[Önálló adatbázis & rugalmas készletek](#tab/single-database)

A következő táblázat hivatkozásokat tartalmaz az Azure SQL Database szolgáltatással kapcsolatos Azure CLI-példaszkriptekre.

| |  |
|---|---|
|**Önálló adatbázis és rugalmas készlet létrehozása**||
| [Önálló adatbázis létrehozása és egy tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ez a CLI-példaszkript egyetlen Azure SQL-adatbázist hoz létre, és konfigurál egy kiszolgálószintű tűzfalszabályt. |
| [Rugalmas készletek létrehozása és rugalmas készletbe helyezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ez a CLI-példaszkript rugalmas SQL-készleteket hoz létre, áthelyezi a készletezett Azure SQL-adatbázisokat, és módosítja a számítási méreteket.|
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ez a CLI-példaszkript egyetlen Azure SQL-adatbázist méretez más számítási méretre az adatbázis méretadatainak lekérdezése után. |
| [Rugalmas készlet méretezése](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ez a CLI-példaszkript egy rugalmas SQL-készletet méretez más számítási méretre.  |
|**Feladatátvételi csoportok**||
| [Önálló adatbázis hozzáadása a feladatátvételi csoporthoz](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Ez a CLI-szkript létrehoz egy adatbázist és egy feladatátvételi csoportot, hozzáadja az adatbázist a feladatátvételi csoporthoz, és teszteli a feladatátvételt a másodlagos kiszolgálóra.|
|||

További információ az [önálló adatbázis Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)-ról.

## <a name="managed-instancetabmanaged-instance"></a>[Felügyelt példány](#tab/managed-instance)

Az alábbi táblázat az Azure CLI parancsfájlokra mutató hivatkozásokat tartalmaz Azure SQL Database által felügyelt példányra.

| |  |
|---|---|
| [Felügyelt példány létrehozása](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Ez a CLI-szkript bemutatja, hogyan hozható létre felügyelt példány. |
| [Felügyelt példány frissítése](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Ez a CLI-szkript bemutatja, hogyan frissíthető egy felügyelt példány. |
| [Adatbázis áthelyezése másik felügyelt példányra](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Ez a CLI-szkript bemutatja, hogyan állíthatja vissza egy adatbázis biztonsági másolatát az egyik példányról a másikra. |
|||

Tudjon meg többet a [felügyelt példány Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) -ról, és keressen [további példákat itt](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

---
