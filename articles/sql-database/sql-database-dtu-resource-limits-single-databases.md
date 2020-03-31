---
title: A DTU-erőforrás egyetlen adatbázist korlátoz
description: Ez a lap néhány gyakori DTU erőforrás-korlátok at az Azure SQL Database-ben az egyes adatbázisok.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: a4c435b4874301fe6fb804085c5b265954cd4f5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256314"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Erőforráskorlátok önálló adatbázisokhoz a DTU-s vásárlási modell használatával

Ez a cikk az Azure SQL Database egyetlen adatbázisok részletes erőforrás-korlátokat tartalmaz a DTU vásárlási modell használatával.

A DTU beszerzési modell erőforráskorlátok rugalmas készletek, lásd: [DTU erőforrás korlátok - rugalmas készletek.](sql-database-dtu-resource-limits-elastic-pools.md) A virtuálismag-erőforráskorlátokról a [virtuálismag-erőforráskorlátok – egyetlen adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [virtuálismag-erőforráskorlátok – rugalmas készletek című témakörben található.](sql-database-vcore-resource-limits-elastic-pools.md) A különböző beszerzési modellekről további információt a Modellek és szolgáltatási szintek vásárlása című [témakörben talál.](sql-database-purchase-models.md)

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Egyetlen adatbázis: Tárolóméretek és számítási méretek

Az alábbi táblázatok az egyes szolgáltatási szinteken és számítási szinteken egyetlen adatbázishoz rendelkezésre álló erőforrásokat mutatják be. Egyetlen adatbázis szolgáltatási szintjét, számítási méretét és tárolási mennyiségét az [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), a [Transact-SQL,](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)a [PowerShell,](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)az [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)vagy a [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)használatával állíthatja be.

> [!IMPORTANT]
> A méretezéssel kapcsolatos útmutatást és szempontokat az [Egyetlen adatbázis méretezése című témakörben](sql-database-single-database-scale.md) talál.

### <a name="basic-service-tier"></a>Alapszintű szolgáltatási szint

| **Számítási méret** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Mellékelt tárhely (GB) | 2 |
| Maximális tárolási lehetőségek (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |N/A |
| Egyidejű dolgozók maximális alkalmazása (kérések) | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

> [!IMPORTANT]
> Az alapszintű szolgáltatásszint kevesebb, mint egy virtuális mag (CPU).  Cpu-igényes számítási feladatok esetén az S3 vagy nagyobb szolgáltatási szint ajánlott. 
>
>Az adattárolás t illeti, az alapszintű szolgáltatási szint a szabványos lapblobokon van elhelyezve. A szabványos lapblobok merevlemez-alapú (HDD)alapú adathordozót használnak, és a legalkalmasabbak a fejlesztésre, tesztelésre és más, ritkán használt számítási feladatokra, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

### <a name="standard-service-tier"></a>Standard szolgáltatási szint

| **Számítási méret** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| DTU-k maximális száma | 10 | 20 | 50 | 100 |
| Mellékelt tárhely (GB) | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | N/A | N/A | N/A | N/A |
| Egyidejű dolgozók maximális alkalmazása (kérések)| 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> A standard S0, S1 és S2 szintek kevesebb, mint egy virtuális mag (CPU).  Cpu-igényes számítási feladatok esetén az S3 vagy nagyobb szolgáltatási szint ajánlott. 
>
>Az adattárolás t illeti, a standard S0 és S1 szolgáltatási szintek a szabványos lapblobokon vannak elhelyezve. A szabványos lapblobok merevlemez-alapú (HDD)alapú adathordozót használnak, és a legalkalmasabbak a fejlesztésre, tesztelésre és más, ritkán használt számítási feladatokra, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

### <a name="standard-service-tier-continued"></a>Szabványos szolgáltatási szint (folytatás)

| **Számítási méret** | **S4** | **S6** | **S7** | **S9 között** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| DTU-k maximális száma | 200 | 400 | 800 | 1600 | 3000 |
| Mellékelt tárhely (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | N/A | N/A | N/A | N/A |N/A |
| Egyidejű dolgozók maximális alkalmazása (kérések)| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Prémium szolgáltatási szint

| **Számítási méret** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Mellékelt tárhely (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maximális tárolási lehetőségek (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Egyidejű dolgozók maximális alkalmazása (kérések)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*1024 GB-tól 4096 GB-ig 256 GB-os lépésekben

> [!IMPORTANT]
> A prémium szint több mint 1 TB tárhelye jelenleg minden régióban elérhető, kivéve: Kína keleti, Észak-Kína, Németország központi, északkeleti régiója, USA nyugati középső régiói, az USA DoD régiói és az Egyesült Államok központi kormánya. Ezekben a régiókban a prémium szint maximális tárháza 1 TB-ra korlátozódik.  További információ: [P11-P15 jelenlegi korlátozások](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> A `tempdb` korlátokat lásd: [tempdb korlátok](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis virtuálismag-erőforrás-korlátaiért tekintse meg [az egyes adatbázisok erőforráskorlátait a virtuálismag-vásárlási modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- A rugalmas készletek virtuálismag-erőforrás-korlátairól lásd: [a virtuálismag-vásárlási modellt használó rugalmas készletek erőforráskorlátai](sql-database-vcore-resource-limits-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátjaihoz tekintse meg [a DTU beszerzési modellt használó rugalmas készletek erőforráskorlátait](sql-database-dtu-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforráskorlátairól a [Felügyelt példány erőforráskorlátai](sql-database-managed-instance-resource-limits.md)t.
- Az általános Azure-korlátokról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál.
- Az adatbázis-kiszolgálón lévő erőforráskorlátokról a kiszolgálón és az előfizetési szinteken lévő korlátozásokról az [SQL Database-kiszolgálóerőforrás-korlátozások áttekintése című témakörben](sql-database-resource-limits-database-server.md) olvashat.
