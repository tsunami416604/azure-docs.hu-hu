---
title: DTU erőforrás-korlátok önálló adatbázisok
description: Ez a lap a Azure SQL Database önálló adatbázisaihoz tartozó általános DTU-erőforrás-korlátokat ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 099094338deba63f678337b7ea13dd9ce9800084
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517688"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>Önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell használatával – Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk részletes erőforrás-korlátozásokat tartalmaz Azure SQL Database önálló adatbázisok számára a DTU beszerzési modell használatával.

A rugalmas készletek DTU megvásárlásához lásd: [DTU-erőforrások korlátai – rugalmas készletek](resource-limits-dtu-elastic-pools.md). A virtuális mag erőforrás-korlátaival kapcsolatban lásd: [virtuális mag-erőforrások korlátai – önálló adatbázisok](resource-limits-vcore-single-databases.md) és [virtuális mag-erőforrások korlátai – rugalmas készletek](resource-limits-vcore-elastic-pools.md). A különböző vásárlási modellekkel kapcsolatos további információkért lásd: [modellek és szolgáltatási szintek beszerzése](purchasing-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Önálló adatbázis: tárolási méretek és számítási méretek

A következő táblázatok az egyes szolgáltatási rétegekben és számítási méretekben elérhető erőforrásokat jelenítik meg az egyes adatbázisokhoz. Az [Azure Portal](single-database-manage.md#the-azure-portal), a [Transact-SQL](single-database-manage.md#transact-sql-t-sql), a [PowerShell](single-database-manage.md#powershell), az [Azure CLI](single-database-manage.md#the-azure-cli)vagy a [REST API](single-database-manage.md#rest-api)használatával megadhatja a szolgáltatási szintet, a számítási méretet és a tárterületet egyetlen adatbázishoz.

> [!IMPORTANT]
> Az útmutatás és a megfontolások méretezésével kapcsolatban lásd: [önálló adatbázis skálázása](single-database-scale.md)

### <a name="basic-service-tier"></a>Alapszintű szolgáltatási szint

| **Számítási méret** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Belefoglalt tárterület (GB) | 2 |
| Maximális tárolási lehetőségek (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |n.a. |
| Egyidejű feldolgozók maximális száma (kérelem) | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

> [!IMPORTANT]
> Az alapszintű szolgáltatási csomag kevesebb mint egy virtuális mag (CPU) biztosít.  A CPU-igényes számítási feladatokhoz az S3 vagy nagyobb szolgáltatási réteg ajánlott. 
>
>Az adattárolásra vonatkozó alapszintű szolgáltatási szintet a standard oldal Blobokra helyezi. A standard oldal Blobok a merevlemezes (HDD-) alapú tárolóeszközöket használják, és a leghatékonyabb fejlesztéshez, teszteléshez és más, ritkán használt számítási feladatokhoz, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

### <a name="standard-service-tier"></a>Standard szolgáltatási szint

| **Számítási méret** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| DTU-k maximális száma | 10 | 20 | 50 | 100 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | n.a. | n.a. | n.a. | n.a. |
| Egyidejű feldolgozók maximális száma (kérelem)| 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> A standard S0, S1 és S2 szintjei kevesebb mint egy virtuális mag (CPU) biztosítanak.  A CPU-igényes számítási feladatokhoz az S3 vagy nagyobb szolgáltatási réteg ajánlott. 
>
>Az adattárolással kapcsolatban a standard szintű S0 és az S1 szolgáltatási szint a standard oldal Blobokra kerül. A standard oldal Blobok a merevlemezes (HDD-) alapú tárolóeszközöket használják, és a leghatékonyabb fejlesztéshez, teszteléshez és más, ritkán használt számítási feladatokhoz, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

### <a name="standard-service-tier-continued"></a>Standard szintű szolgáltatási szint (folytatás)

| **Számítási méret** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| DTU-k maximális száma | 200 | 400 | 800 | 1600 | 3000 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | n.a. | n.a. | n.a. | n.a. |n.a. |
| Egyidejű feldolgozók maximális száma (kérelem)| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Prémium szolgáltatási szint

| **Számítási méret** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Belefoglalt tárterület (GB) | 500 | 500 | 500 | 500 | 4096 * | 4096 * |
| Maximális tárolási lehetőségek (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 * | 4096 * |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Egyidejű feldolgozók maximális száma (kérelem)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*1024 GB-ig 4096 GB-onként 256 GB-os növekményekben

> [!IMPORTANT]
> A prémium szinten több mint 1 TB tárterület érhető el az összes régióban, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország, Németország északkeleti régiója, az USA nyugati középső régiója, US DoD régiók és az USA kormányzati központja. Ezekben a régiókban a prémium szintű Storage Max 1 TB-ra van korlátozva.  További információ: [P11-P15 current korlátozások](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> A `tempdb` korlátokat lásd: [tempdb korlátok](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Következő lépések

- Egyetlen adatbázis virtuális mag erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-single-databases.md)
- A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU beszerzési modell használatával](resource-limits-dtu-elastic-pools.md)
- Az Azure SQL felügyelt példányain felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [SQL felügyelt példányok erőforrás-korlátai](../managed-instance/resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- A logikai SQL Server erőforrás-korlátaival kapcsolatos információkért lásd: a [logikai SQL Server erőforrás-korlátainak áttekintése](resource-limits-logical-server.md) a kiszolgálók és az előfizetési szintek korlátaival kapcsolatos információkért.
