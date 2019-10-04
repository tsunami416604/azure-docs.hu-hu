---
title: Azure SQL Database DTU-alapú erőforrás-korlátok önálló adatbázisok | Microsoft Docs
description: Ez a lap a Azure SQL Database önálló adatbázisaihoz tartozó általános DTU-alapú erőforrás-korlátokat ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 84e39a727d5be842b356e5bd30333cb154df86aa
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568786"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Az DTU-alapú vásárlási modellt használó önálló adatbázisok erőforrás-korlátai

Ez a cikk a DTU-alapú vásárlási modellt használó, Azure SQL Database önálló adatbázisok részletes erőforrás-korlátozásait ismerteti.

A rugalmas készletek DTU-alapú beszerzési modellre vonatkozó korlátairól lásd: [DTU-alapú erőforrás-korlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md). A virtuális mag-alapú erőforrások korlátaival kapcsolatban lásd: [virtuális mag-alapú erőforrások korlátai – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [virtuális mag-alapú erőforrás-korlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md). A különböző vásárlási modellekkel kapcsolatos további információkért lásd: [modellek és szolgáltatási szintek](sql-database-purchase-models.md)beszerzése.

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Önálló adatbázis: Tárolási méretek és számítási méretek

A következő táblázatok az egyes szolgáltatási rétegekben és számítási méretekben elérhető erőforrásokat jelenítik meg az egyes adatbázisokhoz. Az [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), a [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), a [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), az [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)vagy a [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)használatával megadhatja a szolgáltatási szintet, a számítási méretet és a tárterületet egyetlen adatbázishoz.

> [!IMPORTANT]
> Az útmutatás és a megfontolások méretezésével kapcsolatban lásd: [önálló adatbázis skálázása](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Alapszintű szolgáltatási szint

| **Számítási méret** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Belefoglalt tárterület (GB) | 2 |
| Maximális tárolási lehetőségek (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |– |
| Egyidejű feldolgozók maximális száma (kérelem) | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

### <a name="standard-service-tier"></a>Standard szolgáltatási szint

| **Számítási méret** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| DTU-k maximális száma | 10 | 20 | 50 | 100 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | – | N/A | N/A | – |
| Egyidejű feldolgozók maximális száma (kérelem)| 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard szintű szolgáltatási szint (folytatás)

| **Számítási méret** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| DTU-k maximális száma | 200 | 400 | 800 | 1600 | 3000 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | – | N/A | N/A | N/A |– |
| Egyidejű feldolgozók maximális száma (kérelem)| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Prémium szolgáltatási szint

| **Számítási méret** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Belefoglalt tárterület (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maximális tárolási lehetőségek (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Egyidejű feldolgozók maximális száma (kérelem)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*1024 GB-ig 4096 GB-onként 256 GB-os növekményekben

> [!IMPORTANT]
> A prémium szinten több mint 1 TB tárterület érhető el az összes régióban, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország, Északkelet-Németország, USA nyugati középső régiója, US DoD régiók és USA kormányzati központja. Ezekben a régiókban a prémium szinthez tartozó tárterület maximuma 1 TB.  További információ: [P11-P15 current korlátozások](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> A `tempdb` korlátokat lásd: [tempdb korlátok](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis virtuális mag erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a virtuális mag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: Azure-előfizetések [és-szolgáltatások korlátai, kvótái és](../azure-subscription-service-limits.md)megkötései.
- Az adatbázis-kiszolgálók erőforrás-korlátaival kapcsolatos információkért tekintse meg a kiszolgáló és az előfizetési szint korlátaival kapcsolatos információkat a [SQL Database kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md) című témakörben.
