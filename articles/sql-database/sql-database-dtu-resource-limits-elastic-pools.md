---
title: Az Azure SQL Database DTU-alapú erőforrás rugalmas készletek korlátai |} A Microsoft Docs
description: Ez az oldal néhány gyakori DTU-alapú erőforráskorlátok az Azure SQL Database rugalmas készletek ismerteti.
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 128b4bc787cd882e719ee3332d285af44b15dfe2
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412990"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-based-purchasing-model"></a>A DTU-alapú vásárlási modellt használó rugalmas készletek korlátai erőforrások 

Ez a cikk a részletes erőforráskorlátok Azure SQL Database rugalmas készletek és a készletezett adatbázisokat, a DTU-alapú vásárlási modell használatával. 

DTU-alapú vásárlási modell erőforráskorlátok az önálló adatbázisok számára, lásd: [DTU-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-elastic-pools.md). Virtuálismag-alapú erőforráskorlátok, lásd: [Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Rugalmas készlet: tárterületet és teljesítményszintek

Az SQL Database rugalmas készletek esetén az alábbi táblázatok bemutatják az egyes szolgáltatási csomagot és teljesítményszintet szinten elérhető erőforrásokat. A szolgáltatási rétegben, a teljesítményszint és a tárolási összeg használatával beállíthatja a [az Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), a [Azure CLI-vel](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), vagy a [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> A rugalmas készletek az egyes adatbázisok erőforráskorlátok megegyeznek a általában önálló adatbázis dtu-k és a szolgáltatásszint alapján készletek kívül. Például az S2 adatbázis maximális egyidejű feldolgozók 120 feldolgozók. Tehát egy adatbázis egy Standard adatbáziskészletben max. egyidejű feldolgozók egyben 120 feldolgozók 50 dtu-k (Ez egyenértékű s2 esetén) a készlet adatbázisonkénti maximális DTU-e.

### <a name="basic-elastic-pool-limits"></a>Alapszintű rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Belefoglalt tárterület készletenként (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximális tárolási lehetőségek száma készletenként (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximális In-Memory OLTP storage (GB) készletenként | – | N/A | N/A | N/A | N/A | N/A | N/A | – |
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Az adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Az adatbázisonkénti maximális edtu-k választási lehetőségek | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximális tárterület adatbázisonként (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Standard rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Belefoglalt tárterület készletenként (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Maximális tárolási lehetőségek száma készletenként (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Maximális In-Memory OLTP storage (GB) készletenként | – | N/A | N/A | N/A | N/A | – | 
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Az adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Az adatbázisonkénti maximális edtu-k választási lehetőségek | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Maximális tárterület adatbázisonként (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standard rugalmas készletek korlátai (folytatás) 

| eDTU-k száma készletenként | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Belefoglalt tárterület készletenként (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Maximális tárolási lehetőségek száma készletenként (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maximális In-Memory OLTP storage (GB) készletenként | – | N/A | N/A | N/A | – | 
| Adatbázisok maximális száma készletenként | 500 | 500 | 500 | 500 | 500 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Az adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Az adatbázisonkénti maximális edtu-k választási lehetőségek | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Maximális tárolási lehetőségeket (GB) adatbázisonként | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Prémium rugalmas készletek korlátai

| eDTU-k száma készletenként | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Belefoglalt tárterület készletenként (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Maximális tárolási lehetőségek száma készletenként (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maximális In-Memory OLTP storage (GB) készletenként | 1 | 2 | 4 | 10 | 12 | 
| Adatbázisok maximális száma készletenként | 50 | 100 | 100 | 100 | 100 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 200 | 400 | 800 | 1600 | 2400 | 
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| eDTU-k minimális száma adatbázisonként | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| eDTU-k maximális száma adatbázisonként | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Prémium rugalmas készletek korlátai (folytatás) 

| eDTU-k száma készletenként | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Belefoglalt tárterület készletenként (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximális tárolási lehetőségek száma készletenként (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Maximális In-Memory OLTP storage (GB) készletenként | 16 | 20 | 24 | 28 | 32 |
| Adatbázisok maximális száma készletenként | 100 | 100 | 100 | 100 | 100 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Az adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Az adatbázisonkénti maximális edtu-k választási lehetőségek | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> A következő kivételével minden régióban több mint 1 TB prémium szintű storage jelenleg érhető el: USA nyugati középső Régiója, kelet-Kína, közép-USDoDCentral, Németország, USDoDEast, USA-beli államigazgatás – délnyugati, Északkelet-Németország, USGov Iowa, Kínát. Más régiókban a Prémium szinthez tartozó tárterület maximuma 1 TB. Lásd: [P11–P15 – Aktuális korlátozások](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

Ha egy rugalmas készlet minden DTU-ja használatban van, akkor a készletben található minden adatbázis ugyanannyi erőforrást kap a lekérdezések feldolgozásához. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. A rugalmas készlet egyenlő erőforrás-megosztása hozzáadódik az egyes adatbázisok számára máshonnan garantált erőforrások mennyiségéhez, ha a minimális DTU/adatbázis érték nem 0-ra van állítva.

### <a name="database-properties-for-pooled-databases"></a>Készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat ismerteti a készletezett adatbázisok tulajdonságait.

| Tulajdonság | Leírás |
|:--- |:--- |
| eDTU-k maximális száma adatbázisonként |A készletben található adatbázisok bármelyike által használható eDTU-k maximális száma (az elérhetőség a készletben található további adatbázisok kihasználtságától függ). Az eDTU adatbázisonkénti maximális száma nem garantálja az erőforrásokat az adatbázisok számára. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU-k adatbázisonkénti maximális számát állítsa elég magasra ahhoz, hogy az adatbázis-kihasználtsági csúcsokkal is elbírjon. Elvárható, hogy a szükségesnél valamivel nagyobb értéket adjon meg, mivel a készlet általában hullámzó használati mintákat feltételez az adatbázisokkal kapcsolatban, amelyekben az adatbázisok kihasználtsága nem egyszerre éri el a csúcsértéket. Például tegyük fel, hogy az adatbázisonkénti felhasználási csúcs 20 eDTU, és a készletben található 100 adatbázisnak egyszerre csak a 20%-a éri el a csúcsot. Ha az eDTU-k adatbázisonkénti maximális száma 20-ra van állítva, akkor észszerű a készletet ötszörösen túlméretezni, és az eDTU-k készletenkénti számát 400-ra állítani. |
| eDTU-k minimális száma adatbázisonként |A készletben található adatbázisok mindegyike számára garantált eDTU-k minimális száma. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU adatbázisonkénti minimális száma lehet 0, ami egyben az alapértelmezett érték is. Ezen tulajdonság értékeként egy 0 és az adatbázisonkénti átlagosan használt eDTU-k száma közötti mennyiséget adjon meg. A készletben található adatbázisok számának és az eDTU-k adatbázisonkénti minimális számának szorzata nem lehet magasabb az eDTU-k készletenkénti számánál. Például ha egy készletben 20 adatbázis van, és az eDTU-k adatbázisonkénti minimális száma 10-re van állítva, akkor az eDTU-k készletenkénti száma legalább 200 kell, hogy legyen. |
| Maximális tárterület adatbázisonként |A készletben található adatbázis a felhasználó által beállított adatbázis maximális méretét. Azonban a készletezett adatbázisok osztoznak a készlettárolón lefoglalt. Akkor is, ha az összes adatbázisonkénti maximális tárolási *adatbázisonként* értéke lehet nagyobb, mint a teljes rendelkezésre álló tár *terület a készlet*, az összes adatbázis által ténylegesen felhasznált teljes lemezterület nem fogja tudni haladhatja meg a rendelkezésre álló készlet kapacitása. Az adatbázis maximális mérete az adatfájlok maximális méretére vonatkozik, és nem tartalmazza a naplófájlok által használt területet. |
|||
 


## <a name="next-steps"></a>További lépések

- Lásd: [SQL Database: gyakori kérdések](sql-database-faq.md) kapcsolatos gyakori kérdésekre adott válaszokat.
- Azure – általános korlátozások kapcsolatos információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
- További információ a dtu-król és edtu-k: [dtu-król és edtu-k](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- A tempdb méretbeli korlátokat kapcsolatos információkért lásd: https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
