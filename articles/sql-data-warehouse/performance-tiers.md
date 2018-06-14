---
title: Az Azure SQL Data Warehouse teljesítményét rétegek |} Microsoft Docs
description: A rugalmasság és az Azure SQL Data Warehouse rendelkezésre álló számítási optimalizált teljesítményt rétegek bemutatása.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 03881c12faed723999e97431e4a69fdeb6bfa10d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2018
ms.locfileid: "31004526"
---
# <a name="azure-sql-data-warehouse-performance-tiers-preview"></a>Az Azure SQL Data Warehouse teljesítményét rétegek (előzetes verzió)
Az SQL Data Warehouse tárfióktípus két teljesítményszinttel analitikai munkaterhelések optimalizált kínál. Ez a cikk ismerteti a teljesítmény rétegek segítségével válassza ki a leginkább megfelelő teljesítményszintet a munkaterheléshez elveit. 

## <a name="what-is-a-performance-tier"></a>Mi az, hogy egy teljesítményszinttel?
Egy teljesítményszinttel olyan beállítás, amely az adatraktár konfigurációját határozza meg. Ezt a beállítást az egyik első hozott adatraktár létrehozásakor.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- A **rugalmasságra optimalizált teljesítményszint** különválasztja a számítási és tárolási rétegeket az architektúrában. Ez a lehetőség az olyan számítási feladatok esetében teljesít kiemelkedően, amelyek teljes mértékben képesek kiaknázni a számítási és a tárolási kapacitások elkülönítése nyújtotta lehetőségeket a rövid tevékenységcsúcsokat kielégítő gyakori átméretezések során. Ez a számítási teljesítményszint rendelkezik a legalacsonyabb belépő költségszinttel, és a skálázásnak köszönhetően jól szolgálja ki az ügyfelek számítási feladatainak nagy részét.

- A **számítási teljesítményre optimalizált teljesítményszint** az Azure legújabb hardverein egy új NVMe SSD-alapú gyorsítótárat vezet be, amely a leggyakrabban lehívott adatokat a processzorokhoz közel tárolja, azaz épp ott, ahol lenniük kell. A tárolás automatikus szintezésével ez a teljesítményszint kiemelkedően teljesít a komplex lekérdezések esetében, mivel az összes be- és kimeneti adat a helyi számítási rétegben található. Emellett a fejlesztett oszlopcentrikus tárolás révén korlátlan mennyiségű adat tárolható az SQL Data Warehouse-ban. A számítási teljesítményre optimalizált teljesítményszint kínálja a legnagyobb szintű vertikális felskálázást, akár 30 000 számítási adattárházegységig (cDWU). Ezt a teljesítményszintet folyamatos, villámgyors teljesítményt igénylő számítási feladatokhoz válassza.

## <a name="service-levels"></a>Szolgáltatási szintek
A szolgáltatási szint célkitűzést (SLO), a méretezhetőség beállítás határozza meg az adatraktár költségeket és a teljesítmény szintjét. A szolgáltatási szintek, a számítási teljesítmény réteg méretezési optimalizálása a számítási adattárházegységek (cDWU), például DW2000c értékek mérése. A rugalmasság szolgáltatási szintek optimalizálása a dwu-k, például DW2000 mérik. További információkért lásd: [Mi az a adatraktáregység?](what-is-a-data-warehouse-unit-dwu-cdwu.md)

T-SQL-ben a SERVICE_OBJECTIVE beállítás határozza meg, a szolgáltatási szint és az adatraktár teljesítményének rétegét.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="memory-maximums"></a>Memória maximális értékeket
A teljesítmény rétegek különböző memória-profilok fordítja le a különböző méretű memória lekérdezésenként rendelkezik. A számítási teljesítmény rétegben optimalizálása 2,5 x több memóriát, mint a rugalmassági teljesítményszinttel optimalizálása lekérdezésenként biztosít. Az extra memóriát segít a optimalizálása számítási teljesítmény rétegben a rendkívül gyors teljesítményt. További memória lekérdezésenként is lehetővé teszi további lekérdezések futtatásához egyidejűleg, mivel a lekérdezések használható alacsonyabb [erőforrás osztályok](resource-classes-for-workload-management.md). 

### <a name="optimized-for-elasticity"></a>Rugalmasságra optimalizált

A szolgáltatási szintek, a rugalmasság teljesítmény réteg DW100 és közé DW6000 a optimalizálása. 

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Számítási csomópontok | Azokat a terjesztéseket egyes számítási csomópontjain | Maximális memória (MB) eloszlása | Maximális memória mennyisége data warehouse-ba (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1,200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1,600                            |  96                                |
| DW500         | 20                     | 5             | 12                             | 2,000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2,400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4,000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4,800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6,000                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8,000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12,000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24,000                           | 1440                               |

### <a name="optimized-for-compute"></a>Számítási teljesítményre optimalizált

A számítási teljesítmény réteg DW1000c és közé DW30000c optimalizálása a szolgáltatási szintek. 

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Számítási csomópontok | Azokat a terjesztéseket egyes számítási csomópontjain | Maximális memória (GB) eloszlása | Maximális memória mennyisége data warehouse-ba (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

A maximális cDWU DW30000c, amely 60 számítási csomópontok és egy terjesztési egyes számítási csomópontjain. Például: DW30000c 600 TB adatraktár dolgozza fel nagyjából 10 TB-egyes számítási csomópontjain.

## <a name="concurrency-maximums"></a>Párhuzamossági méretkorlát
Az SQL Data Warehouse iparágvezető párhuzamossági egyetlen adatraktár biztosít. Minden egyes lekérdezés legyen elég-erőforrások végrehajtsák hatékony, a rendszer nyomon követi számítási erőforrás-használat párhuzamossági üzembe helyezési ponti rendel minden egyes lekérdezés. A rendszer által lekérdezések egy várólistára ahol azok Várjon, amíg elegendő feldolgozási telepítve tárhelyek. 

Párhuzamossági tárhelyek is CPU rangsorolási határozza meg. További információkért lásd: [elemzése a számítási feladatok](analyze-your-workload.md)

### <a name="concurrency-slots"></a>Párhuzamossági tárhelyek
Párhuzamossági üzembe helyezési ponti kényelmesen nyomon követheti a lekérdezés-végrehajtás rendelkezésre álló erőforrások. Például a munkaállomásokat, egy energiaoptimalizálást egyszerre lefoglalni, mivel a ülési korlátozott megvásárolt jegyek vannak. Hasonlóképpen az SQL Data Warehouse számos véges számítási erőforrásokat. Lekérdezések által az beszerzése a feldolgozási üzembe helyezési ponti számítási erőforrásokat lefoglalni. A lekérdezés végrehajtása szükséges, foglaljon le elegendő feldolgozási üzembe helyezési ponti képesnek kell lennie. A lekérdezés befejeztével a feldolgozási üzembe helyezési ponti felszabadít. 

* Az optimalizált rugalmasság teljesítményszinttel méretezi való 240 párhuzamossági küldésével.
* Az optimalizált a számítási teljesítmény réteg méretezik való 1200 párhuzamossági küldésével.

Minden egyes lekérdezés nulla, egy vagy több egyidejű üzembe helyezési ponti fog használni. Rendszer lekérdezések és az egyes triviális lekérdezések nem igényelnek tárolóhely. Alapértelmezés szerint erőforrás osztályok által szabályozott lekérdezések egy feldolgozási tárolóhely szükséges. Összetettebb lekérdezések megkövetelheti további párhuzamossági tárolóhelye.  

- 10 egyidejű üzembe helyezési ponti a futó lekérdezések férhetnek hozzá a 2 párhuzamossági üzembe helyezési ponti futó lekérdezések mint 5 alkalommal több számítási erőforrással.
- Ha minden lekérdezéshez 10 egyidejű tárhelyek és 40 párhuzamossági tárhelyek, majd csak 4 lekérdezések egyidejűleg is futtathatók.
 
Csak a szabályozott erőforrás lekérdezések párhuzamossági üzembe helyezési ponti felhasználását. Felhasznált párhuzamossági tárhelyek pontos számát határozza meg a lekérdezés [erőforrásosztály](resource-classes-for-workload-management.md).

### <a name="optimized-for-compute"></a>Számítási teljesítményre optimalizált
Az alábbi táblázat a maximális párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti minden [dinamikus erőforrásosztály](resource-classes-for-workload-management.md).  Ezek a számítási teljesítmény rétegben optimalizálása vonatkoznak.

**Dinamikus erőforrás-osztályok**
| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Párhuzamossági üzembe helyezési ponti érhető el | Üzembe helyezési ponti smallrc által használt | Üzembe helyezési ponti mediumrc által használt | Üzembe helyezési ponti largerc által használt | Üzembe helyezési ponti xlargerc által használt |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1200                        | 1                     | 64                     | 128                   | 256                    |

**Statikus erőforrás osztályok**

Az alábbi táblázat a maximális párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti minden [statikus erőforrásosztály](resource-classes-for-workload-management.md).  

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Párhuzamossági üzembe helyezési ponti érhető el |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Rugalmasságra optimalizált
Az alábbi táblázat a maximális párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti minden [dinamikus erőforrásosztály](resource-classes-for-workload-management.md).  Ezek a rugalmasság teljesítményszinttel optimalizálása vonatkoznak.

**Dinamikus erőforrás-osztályok**

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Párhuzamossági üzembe helyezési ponti érhető el | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**Statikus erőforrás osztályok** jeleníti meg a maximális párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti a következő táblázat az egyes [statikus erőforrásosztály](resource-classes-for-workload-management.md).  Ezek a rugalmasság teljesítményszinttel optimalizálása vonatkoznak.

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Maximális feldolgozási tárhelyek |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

E küszöbértékek valamelyike teljesül, amikor új lekérdezések helyezi várólistára, és végre érkezési sorrendben történő kiküldési alapon.  A lekérdezések befejeződése és a lekérdezések és üzembe helyezési ponti száma korlátokat alá esnek, az SQL Data Warehouse feloldja a várólistára helyezett lekérdezések. 

## <a name="next-steps"></a>További lépések

További információt hogyan használhatók ki az erőforrás-osztályok optimalizálása érdekében az alkalmazások és szolgáltatások további tekintse át a következő cikkeket:
* [Erőforrás-osztályok a munkaterhelés-kezelés](resource-classes-for-workload-management.md)
* [A számítási feladatok elemzése](analyze-your-workload.md)

