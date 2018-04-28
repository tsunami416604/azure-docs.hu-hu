---
title: Memória és a feldolgozási korlátok - Azure SQL Data Warehouse |} Microsoft Docs
description: Tekintse meg a különböző teljesítményszintet és az Azure SQL Data Warehouse erőforrás osztályok számára lefoglalt memória és a feldolgozási korlátok.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 380990ac4173f0495f35aeb0e7a3995af938dffb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse memória és a feldolgozási korlátok
Tekintse meg a különböző teljesítményszintet és az Azure SQL Data Warehouse erőforrás osztályok számára lefoglalt memória és a feldolgozási korlátok. További információ az alkalmazások és szolgáltatások felügyeleti csomag ezen képességek alkalmazandó talál, [erőforrás-osztályok a munkaterhelés felügyeleti](resource-classes-for-workload-management.md). 

Jelenleg nincsenek két generációja érhető el az SQL Data Warehouse – Gen1 és Gen2 szolgáltatással. Azt javasoljuk, hogy is Gen2 az SQL Data Warehouse a legjobb teljesítményt az adatraktár-számítási feladat eléréséhez használja. Gen2 vezet be egy új NVMe teli állapot lemezgyorsítótár a processzorokat megközelíti a leggyakrabban használt adatokat biztosít. Ezzel eltávolítja a távoli i/o-a intenzív és a nagy munkaterheléseket. Teljesítmény Gen2 lehetőséget biztosít a legnagyobb szintű skála engedélyezésével legfeljebb 30 000 Adattárházegységek téve, és korlátlan oszlopos tárolására. Rendszer továbbra is támogatja az előző generációs (Gen1) az SQL Data Warehouse és megőrizni a szolgáltatásait; azonban javasoljuk, hogy [Gen2 frissítés](upgrade-to-latest-generation.md) legkorábbi tetszés. 

## <a name="data-warehouse-capacity-settings"></a>Adatraktár kapacitásának beállítások
Az alábbi táblázatok bemutatják az adatraktár más-más teljesítménybeli szinten maximális kapacitását. Ha módosítani szeretné a teljesítményszintet, lásd: [méretezési számítási - portál](quickstart-scale-compute-portal.md).

### <a name="gen1"></a>Gen1

A szolgáltatási szintjeit DW100 és DW6000 Gen1 közé. 

| Teljesítményszint | Számítási csomópontok | Azokat a terjesztéseket egyes számítási csomópontjain | Adatraktár (GB) memória |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1440                           |

### <a name="gen2"></a>Gen2

Gen2 2,5 x több memóriát, mint a Gen1 lekérdezésenként biztosít. Az extra memóriát segít a Gen2 a gyors teljesítményt.  A teljesítményszintet és DW30000c DW1000c a Gen2 közé. 

| Teljesítményszint | Számítási csomópontok | Azokat a terjesztéseket egyes számítási csomópontjain | Adatraktár (GB) memória |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

A maximális Gen2 DWU DW30000c, amely 60 számítási csomópontok és egy terjesztési egyes számítási csomópontjain. Például: DW30000c 600 TB adatraktár dolgozza fel nagyjából 10 TB-egyes számítási csomópontjain.


## <a name="concurrency-maximums"></a>Párhuzamossági méretkorlát
-Nek minden egyes lekérdezés sikeres végrehajtásához az SQL Data Warehouse erőforrásokkal nyomon követi számítási erőforrás-használat párhuzamossági üzembe helyezési ponti rendel minden lekérdezésben. A rendszer elhelyezi lekérdezések várólista ahol azok Várjon, amíg elegendő [egyidejűségi üzembe helyezési ponti](resource-classes-for-workload-management.md#concurrency-slots) érhetők el. 

Párhuzamossági tárhelyek is CPU rangsorolási határozza meg. További információkért lásd: [elemzése a számítási feladatok](analyze-your-workload.md)

### <a name="gen1"></a>Gen1
Az alábbi táblázat a maximális párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti minden [dinamikus erőforrásosztály](resource-classes-for-workload-management.md).  Ezek a Gen1 vonatkoznak.

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
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |

**Statikus erőforrás osztályok** jeleníti meg a maximális párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti a következő táblázat az egyes [statikus erőforrásosztály](resource-classes-for-workload-management.md).  Ezek a Gen1 vonatkoznak.

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
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="gen2"></a>Gen2
Az alábbi táblázat a maximális párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti minden [dinamikus erőforrásosztály](resource-classes-for-workload-management.md). Eltérően Gen1 dinamikus erőforrás osztályokat a Gen2 valóban dinamikus.  A következő egy 3-10-22-70 százalékos aránya a memóriafoglalás a kis-medium-nagy-xlarge erőforrás osztályok Gen2. 

**Dinamikus erőforrás-osztályok**

| Teljesítményszint | Maximális párhuzamos lekérdezések | Párhuzamossági üzembe helyezési ponti érhető el | Üzembe helyezési ponti smallrc által használt | Üzembe helyezési ponti mediumrc által használt | Üzembe helyezési ponti largerc által használt | Üzembe helyezési ponti xlargerc által használt |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

**Statikus erőforrás osztályok**

Az alábbi táblázat a maximális párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti minden [statikus erőforrásosztály](resource-classes-for-workload-management.md).  

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Párhuzamossági üzembe helyezési ponti érhető el |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

E küszöbértékek valamelyike teljesül, amikor új lekérdezések helyezi várólistára, és végre érkezési sorrendben történő kiküldési alapon.  A lekérdezések befejeződése és a lekérdezések és üzembe helyezési ponti száma korlátokat alá esnek, az SQL Data Warehouse feloldja a várólistára helyezett lekérdezések. 

## <a name="next-steps"></a>További lépések

További információt hogyan használhatók ki az erőforrás-osztályok optimalizálása érdekében az alkalmazások és szolgáltatások további tekintse át a következő cikkeket:
* [Erőforrás-osztályok a munkaterhelés-kezelés](resource-classes-for-workload-management.md)
* [A számítási feladatok elemzése](analyze-your-workload.md)

