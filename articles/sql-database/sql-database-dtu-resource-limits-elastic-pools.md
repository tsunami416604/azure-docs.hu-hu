---
title: A DTU-erőforrás korlátozza a rugalmas készleteket
description: Ez a lap néhány gyakori DTU erőforrás-korlátok rugalmas készletek az Azure SQL Database-ben ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 76085dc29d40944cf704dbc5efc578b3314f499a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419238"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>A DTU vásárlási modellt használó rugalmas készletek erőforrás-korlátai

Ez a cikk az Azure SQL Database rugalmas készletek és a DTU vásárlási modell használatával készletezett adatbázisok részletes erőforráskorlátokat tartalmaz.

Az egyes adatbázisok DTU beszerzési modellerőforrás-korlátairól lásd: [DTU erőforráskorlátok – egyetlen adatbázisok.](sql-database-vcore-resource-limits-elastic-pools.md) A virtuálismag-erőforráskorlátokról a [virtuálismag-erőforráskorlátok – egyetlen adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [virtuálismag-erőforráskorlátok – rugalmas készletek című témakörben található.](sql-database-vcore-resource-limits-elastic-pools.md)

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Rugalmas készlet: Tárolóméretek és számítási méretek

Az SQL Database rugalmas készletek, az alábbi táblázatok az egyes szolgáltatási szinteken és számítási méretben elérhető erőforrásokat jelenítik meg. Beállíthatja a szolgáltatási szint, a számítási méret és a tárolási összeg az [Azure Portalon,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [a PowerShell,](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases)az [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)vagy a [REST API használatával.](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases)

> [!IMPORTANT]
> A méretezési útmutatóés szempontok, [lásd: Rugalmas készlet méretezése](sql-database-elastic-pool-scale.md)
> [!NOTE]
> A rugalmas készletekben lévő egyes adatbázisok erőforráskorlátai általában megegyeznek a DRU-kon és a szolgáltatási szinten alapuló készleteken kívüli egyes adatbázisok erőforráskorlátaival. Egy S2-adatbázis maximális egyidejű dolgozóinak maximális egyidejű dolgozója például 120 dolgozó. Így a standard készletben lévő adatbázis egyidejű dolgozóinak maximális egyidejű dolgozói is 120 dolgozó, ha a készletben lévő adatbázisonkénti maximális DTU 50 DTU (ami az S2-nek felel meg).

> [!NOTE]
> A készlet erőforráskorlátonkénti tároló az alábbi táblázatok mindegyikében nem tartalmazza a tempdb és a naplótárolót.

### <a name="basic-elastic-pool-limits"></a>Alapszintű rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| A mellékelt készlet (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Készletenkéntmaximális tárolási lehetőségek (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximális memória-OLTP-tárterület készletenként (GB) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Maximális db-szám készletenként <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Egyidejű dolgozók (kérések) maximális a poolonként <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű munkamenetek maximális száma készletenként <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Min eDTO választási lehetőségek adatbázisonként | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Maximális eD-TUs-választási lehetőségek adatbázisonként | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximális tárterület adatbázisonként (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

### <a name="standard-elastic-pool-limits"></a>Standard rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| A mellékelt készlet (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Készletenkéntmaximális tárolási lehetőségek (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Maximális memória-OLTP-tárterület készletenként (GB) | N/A | N/A | N/A | N/A | N/A | N/A |
| Maximális db-szám készletenként <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Egyidejű dolgozók (kérések) maximális a poolonként <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Egyidejű munkamenetek maximális száma készletenként <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min eDTO választási lehetőségek adatbázisonként | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximális eD-TUs-választási lehetőségek adatbázisonként | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximális tárterület adatbázisonként (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

### <a name="standard-elastic-pool-limits-continued"></a>Standard rugalmas készletek korlátai (folytatás)

| eDTU-k száma készletenként | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| A mellékelt készlet (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Készletenkéntmaximális tárolási lehetőségek (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maximális memória-OLTP-tárterület készletenként (GB) | N/A | N/A | N/A | N/A | N/A |
| Maximális db-szám készletenként <sup>1</sup> | 500 | 500 | 500 | 500 | 500 |
| Egyidejű dolgozók (kérések) maximális a poolonként <sup>2</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű munkamenetek maximális száma készletenként <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min eDTO választási lehetőségek adatbázisonként | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maximális eD-TUs-választási lehetőségek adatbázisonként | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Adatbázisonkénti maximális tárolási lehetőségek (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

### <a name="premium-elastic-pool-limits"></a>Prémium rugalmas készletek korlátai

| eDTU-k száma készletenként | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| A mellékelt készlet (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Készletenkéntmaximális tárolási lehetőségek (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maximális memória-OLTP-tárterület készletenként (GB) | 1 | 2 | 4 | 10 | 12 |
| Maximális db-szám készletenként <sup>1</sup> | 50 | 100 | 100 | 100 | 100 |
| Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>2</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Egyidejű munkamenetek maximális száma készletenként <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| eDTU-k minimális száma adatbázisonként | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| eDTU-k maximális száma adatbázisonként | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

### <a name="premium-elastic-pool-limits-continued"></a>Prémium rugalmas készletek korlátai (folytatás)

| eDTU-k száma készletenként | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| A mellékelt készlet (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Készletenkéntmaximális tárolási lehetőségek (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Maximális memória-OLTP-tárterület készletenként (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximális db-szám készletenként <sup>1</sup> | 100 | 100 | 100 | 100 | 100 |
| Egyidejű dolgozók (kérések) maximális a poolonként <sup>2</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű munkamenetek maximális száma készletenként <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min eDTO választási lehetőségek adatbázisonként | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maximális eD-TUs-választási lehetőségek adatbázisonként | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

> [!IMPORTANT]
> A prémium szint több mint 1 TB tárhelye jelenleg minden régióban elérhető, kivéve: Kína keleti, Észak-Kína, Németország központi, északkeleti régiója, USA nyugati középső régiói, az USA DoD régiói és az Egyesült Államok központi kormánya. Ezekben a régiókban a prémium szint maximális tárháza 1 TB-ra korlátozódik.  További információ: [P11-P15 jelenlegi korlátozások](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Ha egy rugalmas készlet minden DTU-ja használatban van, akkor a készletben található minden adatbázis ugyanannyi erőforrást kap a lekérdezések feldolgozásához. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. A rugalmas készlet egyenlő erőforrás-megosztása hozzáadódik az egyes adatbázisok számára máshonnan garantált erőforrások mennyiségéhez, ha a minimális DTU/adatbázis érték nem 0-ra van állítva.

> [!NOTE]
> A `tempdb` korlátokat lásd: [tempdb korlátok](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Készletezésű adatbázisok adatbázis-tulajdonságai

Az alábbi táblázat a készletezett adatbázisok tulajdonságait ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| eDTU-k maximális száma adatbázisonként |A készletben található adatbázisok bármelyike által használható eDTU-k maximális száma (az elérhetőség a készletben található további adatbázisok kihasználtságától függ). Az eDTU adatbázisonkénti maximális száma nem garantálja az erőforrásokat az adatbázisok számára. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU-k adatbázisonkénti maximális számát állítsa elég magasra ahhoz, hogy az adatbázis-kihasználtsági csúcsokkal is elbírjon. Elvárható, hogy a szükségesnél valamivel nagyobb értéket adjon meg, mivel a készlet általában hullámzó használati mintákat feltételez az adatbázisokkal kapcsolatban, amelyekben az adatbázisok kihasználtsága nem egyszerre éri el a csúcsértéket. Például tegyük fel, hogy az adatbázisonkénti felhasználási csúcs 20 eDTU, és a készletben található 100 adatbázisnak egyszerre csak a 20%-a éri el a csúcsot. Ha az eDTU-k adatbázisonkénti maximális száma 20-ra van állítva, akkor észszerű a készletet ötszörösen túlméretezni, és az eDTU-k készletenkénti számát 400-ra állítani. |
| eDTU-k minimális száma adatbázisonként |A készletben található adatbázisok mindegyike számára garantált eDTU-k minimális száma. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU adatbázisonkénti minimális száma lehet 0, ami egyben az alapértelmezett érték is. Ezen tulajdonság értékeként egy 0 és az adatbázisonkénti átlagosan használt eDTU-k száma közötti mennyiséget adjon meg. A készletben található adatbázisok számának és az eDTU-k adatbázisonkénti minimális számának szorzata nem lehet magasabb az eDTU-k készletenkénti számánál. Például ha egy készletben 20 adatbázis van, és az eDTU-k adatbázisonkénti minimális száma 10-re van állítva, akkor az eDTU-k készletenkénti száma legalább 200 kell, hogy legyen. |
| Maximális tárhely adatbázisonként |A készletben lévő adatbázis felhasználó által megadott maximális adatbázismérete. A készletezett adatbázisok azonban megosztják a lefoglalt készlettárolót. Még ha az *adatbázisonkénti* maximális tárterület nagyobb is, mint a készlet teljes rendelkezésre álló *tárhelye,* az összes adatbázis által ténylegesen felhasznált teljes terület nem lesz képes túllépni a rendelkezésre álló készletkorlátot. Az adatbázisok maximális mérete az adatfájlok maximális méretére vonatkozik, de nem tartalmazza a naplófájlok által használt területet. |
|||

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis virtuálismag-erőforrás-korlátaiért tekintse meg [az egyes adatbázisok erőforráskorlátait a virtuálismag-vásárlási modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- Egyetlen adatbázis DTU erőforrás-korlátaihoz tekintse meg [az egyes adatbázisok erőforráskorlátait a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- A rugalmas készletek virtuálismag-erőforrás-korlátairól lásd: [a virtuálismag-vásárlási modellt használó rugalmas készletek erőforráskorlátai](sql-database-vcore-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforráskorlátairól a [Felügyelt példány erőforráskorlátai](sql-database-managed-instance-resource-limits.md)t.
- Az általános Azure-korlátokról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál.
- Az adatbázis-kiszolgálón lévő erőforráskorlátokról a kiszolgálón és az előfizetési szinteken lévő korlátozásokról az [SQL Database-kiszolgálóerőforrás-korlátozások áttekintése című témakörben](sql-database-resource-limits-database-server.md) olvashat.
