---
title: Azure CLI-példaszkriptek az SQL Database-hez | Microsoft Docs
description: Az Azure CLI-példaszkriptek bemutatják Azure SQL Database-kiszolgálók, rugalmas készletek, adatbázisok és tűzfalak létrehozásának és felügyeletének folyamatát.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/03/2019
ms.openlocfilehash: 3b98214da259e9e429c938f8ca7963c9f525e862
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560821"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-minták az Azure SQL Database-hez

Az Azure SQL Database használatával konfigurálható <a href="/cli/azure">Azure CLI-vel</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="single-database--elastic-pools"></a>Önálló adatbázis és rugalmas készletek

A következő táblázat hivatkozásokat tartalmaz az Azure SQL Database szolgáltatással kapcsolatos Azure CLI-példaszkriptekre.

| |  |
|---|---|
|**Önálló adatbázis és rugalmas készlet létrehozása**||
| [Önálló adatbázis létrehozása és egy tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ez a CLI-példaszkript egyetlen Azure SQL-adatbázist hoz létre, és konfigurál egy kiszolgálószintű tűzfalszabályt. |
| [Rugalmas készletek létrehozása és készletezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ez a CLI-példaszkript rugalmas SQL-készleteket hoz létre, áthelyezi a készletezett Azure SQL-adatbázisokat, és módosítja a számítási méreteket.|
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ez a CLI-példaszkript egyetlen Azure SQL-adatbázist méretez más számítási méretre az adatbázis méretadatainak lekérdezése után. |
| [Rugalmas készlet méretezése](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ez a CLI-példaszkript egy rugalmas SQL-készletet méretez más számítási méretre.  |
|||

Tudjon meg többet a [egy adatbázist az Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Felügyelt példány

Az alábbi táblázat az Azure SQL Database – felügyelt példány az Azure CLI-példaszkriptek hivatkozásokat tartalmaz.

| |  |
|---|---|
| [Felügyelt példány létrehozása](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/) | Ez a CLI-példaszkript bemutatja, hogyan hozhat létre egy felügyelt példányt. |
| [Felügyelt példány frissítése](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/modify-azure-sql-database-managed-instance-using-azure-cli/) | Ez a CLI-példaszkript bemutatja, hogyan frissítse a felügyelt példány. |
| [-Adatbázis áthelyezése egy másik, a felügyelt példány](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Ez a CLI-példaszkript bemutatja, hogyan állíthatja vissza egy adatbázis biztonsági egy példányról a másikra. |
|||

Tudjon meg többet a [felügyelt példány az Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) , és keresse meg [további példákat Itt](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
