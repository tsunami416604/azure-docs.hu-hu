---
title: Az Azure SQL Database DTU-alapú erőforrás korlátozza az önálló adatbázisok |} A Microsoft Docs
description: Ezen a lapon azt ismerteti, hogy néhány gyakori DTU-alapú erőforráskorlátok az Azure SQL Database önálló adatbázisok számára.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: 7c35c1f3568ab98bdd3c7fba8acd3aa958aa795b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285133"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>A DTU-alapú vásárlási modell segítségével az önálló adatbázisok számára erőforráskorlátok

Ez a cikk a részletes erőforráskorlátok a DTU-alapú vásárlási modell használatával Azure SQL Database önálló adatbázisok számára.

DTU-alapú vásárlási modell erőforráskorlátok a rugalmas készletek, lásd: [DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md). Virtuálismag-alapú erőforráskorlátok, lásd: [Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md). A különböző vásárlási modell kapcsolatos további információkért lásd: [vásárlási modellt és a szolgáltatási szintekről](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Önálló adatbázis: Tárterületet és számítási méretek

Az alábbi táblázatok az önálló adatbázis rendelkezésre álló erőforrások megjelenítése az egyes szolgáltatásszinteken, és a számítási méret. A szolgáltatási rétegben, a számítási méretét és a egy önálló adatbázis a tárolókapacitás is megadhatja a [az Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), a [ Az Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), vagy a [REST API-val](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Skálázás útmutatást és szempontokat, lásd: [önálló adatbázis méretezése](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Alapszintű szolgáltatásszint

| **Számítási mérete** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Belefoglalt tárterület (GB) | 2 |
| Maximális tárolási lehetőségeket (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |– |
| Egyidejű feldolgozók (kérelmek) maximális | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

### <a name="standard-service-tier"></a>Standard szolgáltatásszint

| **Számítási mérete** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| DTU-k maximális száma | 10 | 20 | 50 | 100 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségeket (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | – | N/A | N/A | – |
| Egyidejű feldolgozók (kérelmek) maximális| 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard szintű szolgáltatáscsomagban (Folytatás)

| **Számítási mérete** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| DTU-k maximális száma | 200 | 400 | 800 | 1600 | 3000 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségeket (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | – | N/A | N/A | N/A |– |
| Egyidejű feldolgozók (kérelmek) maximális| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Prémium szolgáltatásszint

| **Számítási mérete** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Belefoglalt tárterület (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maximális tárolási lehetőségeket (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Egyidejű feldolgozók (kérelmek) maximális| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* 1024 GB-tól 256 GB-os lépésekben maximum 4096 GB

> [!IMPORTANT]
> Jelenleg több mint 1 TB tárterület egységára prémium szinten érhető el minden régióban, kivéve: Kelet-Kína, Észak-Kína, közép-Németország, Északkelet-Németország, USA nyugati középső Régiója, USA védelmi Minisztériuma régiók és US Government központi. Ezekben a régiókban a prémium szinthez tartozó tárterület maximuma 1 TB.  További információkért lásd: P11 – P15 – aktuális korlátozások.  
> [!NOTE]
> A `tempdb` korlátozásairól lásd [tempdb korlátok](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>További lépések

- Önálló adatbázis erőforráskorlátok virtuális mag, lásd: [erőforráskorlátok és önálló adatbázisokat a Virtuálismag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- Rugalmas készletek erőforráskorlátok virtuális mag, lásd: [erőforráskorlátok a rugalmas készletek a Virtuálismag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-elastic-pools.md)
- Dtu-k erőforráskorlátok a rugalmas készletek, lásd: [erőforráskorlátok a rugalmas készletek a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- Erőforráskorlátok a felügyelt példányok, lásd: [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md).
- Azure – általános korlátozások kapcsolatos információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
- Adatbázis-kiszolgáló erőforráskorlátok kapcsolatos információkért lásd: [az SQL Database-kiszolgálóhoz erőforráskorlátok áttekintése](sql-database-resource-limits-database-server.md) kapcsolatos korlátozásokat a kiszolgálók és az előfizetés szintjén.
