---
title: Azure SQL Database DTU-alapú erőforrás-korlátok rugalmas készletek | Microsoft Docs
description: Ez az oldal a Azure SQL Database rugalmas készletekre vonatkozó gyakori DTU-alapú erőforrás-korlátozásokat ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 2bd193c53ee85a1423dc63bf669021de954231c8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568810"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-based-purchasing-model"></a>A rugalmas készletek erőforrásokra vonatkozó korlátai a DTU-alapú vásárlási modell használatával

Ez a cikk részletes erőforrás-korlátokat biztosít a rugalmas készletek és a készletezett adatbázisok Azure SQL Database a DTU-alapú vásárlási modell használatával.

Az önálló adatbázisokra vonatkozó DTU-alapú beszerzési modellek esetében lásd: [DTU-alapú erőforrás-korlátok – önálló adatbázisok](sql-database-vcore-resource-limits-elastic-pools.md). A virtuális mag-alapú erőforrások korlátaival kapcsolatban lásd: [virtuális mag-alapú erőforrások korlátai – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [virtuális mag-alapú erőforrás-korlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Rugalmas készlet: Tárolási méretek és számítási méretek

SQL Database rugalmas készletek esetében az alábbi táblázatok az egyes szolgáltatási szintek és számítási méretek számára elérhető erőforrásokat mutatják be. A szolgáltatási szintet, a számítási méretet és a tárterületet a [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), a [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), az [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)vagy a [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases)használatával állíthatja be.

> [!IMPORTANT]
> A méretezéssel kapcsolatos útmutatást és szempontokat lásd: [rugalmas készlet](sql-database-elastic-pool-scale.md) skálázása
> [!NOTE]
> A rugalmas készletekben található különálló adatbázisok erőforrás-korlátai általában ugyanazok, mint a készleteken kívüli önálló adatbázisok esetében a DTU és a szolgáltatási réteg alapján. Például az S2-adatbázisok maximális egyidejű feldolgozói 120 feldolgozók. Így a standard szintű készletben lévő adatbázisok maximálisan egyidejű feldolgozói is 120 feldolgozók, ha a készletben lévő adatbázis Max DTU 50 DTU (amely az S2-vel egyenértékű).

### <a name="basic-elastic-pool-limits"></a>Alapszintű rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Foglalt tárterület/készlet (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Tárolási lehetőségek maximális száma (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Memóriában tárolt OLTP-tárolók maximális száma (GB) | – | N/A | N/A | N/A | N/A | N/A | N/A | – |
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Edtu minimális száma adatbázis szerint | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Edtu maximális választéka adatbázison | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximális tárterület adatbázisonként (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

### <a name="standard-elastic-pool-limits"></a>Standard rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Foglalt tárterület/készlet (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Tárolási lehetőségek maximális száma (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Memóriában tárolt OLTP-tárolók maximális száma (GB) | – | N/A | N/A | N/A | N/A | – |
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Edtu minimális száma adatbázis szerint | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Edtu maximális választéka adatbázison | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximális tárterület adatbázisonként (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standard rugalmas készletek korlátai (folytatás)

| eDTU-k száma készletenként | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Foglalt tárterület/készlet (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Tárolási lehetőségek maximális száma (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Memóriában tárolt OLTP-tárolók maximális száma (GB) | – | N/A | N/A | N/A | – |
| Adatbázisok maximális száma készletenként | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 |
| Edtu minimális száma adatbázis szerint | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Edtu maximális választéka adatbázison | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Tárolási lehetőségek maximális száma (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits"></a>Prémium rugalmas készletek korlátai

| eDTU-k száma készletenként | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Foglalt tárterület/készlet (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Tárolási lehetőségek maximális száma (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Memóriában tárolt OLTP-tárolók maximális száma (GB) | 1 | 2 | 4 | 10 | 12 |
| Adatbázisok maximális száma készletenként | 50 | 100 | 100 | 100 | 100 |
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 200 | 400 | 800 | 1600 | 2400 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 |
| eDTU-k minimális száma adatbázisonként | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 |
| eDTU-k maximális száma adatbázisonként | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits-continued"></a>Prémium rugalmas készletek korlátai (folytatás)

| eDTU-k száma készletenként | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Foglalt tárterület/készlet (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Tárolási lehetőségek maximális száma (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Memóriában tárolt OLTP-tárolók maximális száma (GB) | 16 | 20 | 24 | 28 | 32 |
| Adatbázisok maximális száma készletenként | 100 | 100 | 100 | 100 | 100 |
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 |
| Edtu minimális száma adatbázis szerint | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Edtu maximális választéka adatbázison | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

> [!IMPORTANT]
> A prémium szinten több mint 1 TB tárterület érhető el az összes régióban, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország, Északkelet-Németország, USA nyugati középső régiója, US DoD régiók és USA kormányzati központja. Ezekben a régiókban a prémium szinthez tartozó tárterület maximuma 1 TB.  További információ: [P11-P15 current korlátozások](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Ha egy rugalmas készlet minden DTU-ja használatban van, akkor a készletben található minden adatbázis ugyanannyi erőforrást kap a lekérdezések feldolgozásához. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. A rugalmas készlet egyenlő erőforrás-megosztása hozzáadódik az egyes adatbázisok számára máshonnan garantált erőforrások mennyiségéhez, ha a minimális DTU/adatbázis érték nem 0-ra van állítva.

> [!NOTE]
> A `tempdb` korlátokat lásd: [tempdb korlátok](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>A készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat a készletezett adatbázisok tulajdonságait ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| eDTU-k maximális száma adatbázisonként |A készletben található adatbázisok bármelyike által használható eDTU-k maximális száma (az elérhetőség a készletben található további adatbázisok kihasználtságától függ). Az eDTU adatbázisonkénti maximális száma nem garantálja az erőforrásokat az adatbázisok számára. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU-k adatbázisonkénti maximális számát állítsa elég magasra ahhoz, hogy az adatbázis-kihasználtsági csúcsokkal is elbírjon. Elvárható, hogy a szükségesnél valamivel nagyobb értéket adjon meg, mivel a készlet általában hullámzó használati mintákat feltételez az adatbázisokkal kapcsolatban, amelyekben az adatbázisok kihasználtsága nem egyszerre éri el a csúcsértéket. Például tegyük fel, hogy az adatbázisonkénti felhasználási csúcs 20 eDTU, és a készletben található 100 adatbázisnak egyszerre csak a 20%-a éri el a csúcsot. Ha az eDTU-k adatbázisonkénti maximális száma 20-ra van állítva, akkor észszerű a készletet ötszörösen túlméretezni, és az eDTU-k készletenkénti számát 400-ra állítani. |
| eDTU-k minimális száma adatbázisonként |A készletben található adatbázisok mindegyike számára garantált eDTU-k minimális száma. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU adatbázisonkénti minimális száma lehet 0, ami egyben az alapértelmezett érték is. Ezen tulajdonság értékeként egy 0 és az adatbázisonkénti átlagosan használt eDTU-k száma közötti mennyiséget adjon meg. A készletben található adatbázisok számának és az eDTU-k adatbázisonkénti minimális számának szorzata nem lehet magasabb az eDTU-k készletenkénti számánál. Például ha egy készletben 20 adatbázis van, és az eDTU-k adatbázisonkénti minimális száma 10-re van állítva, akkor az eDTU-k készletenkénti száma legalább 200 kell, hogy legyen. |
| Tárterület maximális száma adatbázison |A felhasználó által a készletben lévő adatbázis számára beállított maximális adatbázis-méret. A készletezett adatbázisok azonban megosztják a lefoglalt készlet tárterületét. Még akkor is, ha az *adatbázis* teljes tárterülete úgy van beállítva, hogy nagyobb legyen, mint a *készlet*teljes rendelkezésre álló tárolóhelye, az összes adatbázis által ténylegesen felhasznált terület teljes mérete nem haladhatja meg a rendelkezésre álló készlet korlátját. Az adatbázis maximális mérete az adatfájlok maximális méretére vonatkozik, és nem tartalmazza a naplófájlok által használt területet. |
|||

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis virtuális mag erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a virtuális mag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- Egyetlen adatbázis DTU erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: Azure-előfizetések [és-szolgáltatások korlátai, kvótái és](../azure-subscription-service-limits.md)megkötései.
- Az adatbázis-kiszolgálók erőforrás-korlátaival kapcsolatos információkért tekintse meg a kiszolgáló és az előfizetési szint korlátaival kapcsolatos információkat a [SQL Database kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md) című témakörben.