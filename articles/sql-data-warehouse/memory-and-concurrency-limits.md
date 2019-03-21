---
title: Memória- és egyidejűségi korlátok – Azure SQL Data Warehouse |} A Microsoft Docs
description: Tekintse meg a különböző teljesítményszintek és az Azure SQL Data Warehouse erőforrásosztályok számára lefoglalt memória- és egyidejűségi korlátok.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 141112b8b6b44706a750d8a97780e018d96a5006
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890790"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse a memória- és egyidejűségi korlátok
Tekintse meg a különböző teljesítményszintek és az Azure SQL Data Warehouse erőforrásosztályok számára lefoglalt memória- és egyidejűségi korlátok. További információt, és a alkalmazni ezeket a képességeket a számítási feladat felügyeleti csomag: [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md). 

Jelenleg nincsenek két generáció esetében érhető el az SQL Data Warehouse – Gen1 és Gen2. Azt javasoljuk, akkor kihasználhatja Gen2 az SQL Data Warehouse a legjobb teljesítmény az adatraktár-számítási feladat számára. Gen2, amelyek tartja a leggyakrabban lehívott adatokat a processzorokhoz közel tárolja egy új NVMe tartós állapotú meghajtókat gyorsítótárat vezet be. Ezzel eltávolítja a távoli i/o a számításigényes és a nagy erőforrás-igényű számítási feladatokhoz. Teljesítmény mellett Gen2 kínálja a legnagyobb szintű vertikális engedélyezésével legfeljebb 30 000 Adattárházegységek méretezését, és korlátlan Oszlopalapú tárolást biztosít. Rendszer továbbra is támogatja az előző generációs (Gen1) az SQL Data Warehouse és ugyanazokat a szolgáltatásokat; megőrzése azonban javasoljuk, hogy [frissítés Gen2-re](upgrade-to-latest-generation.md) az Önnek legkorábbi címen. 

## <a name="data-warehouse-capacity-settings"></a>Data warehouse kapacitásbeállítások
Az alábbi táblázatokban található különböző teljesítményszintek az adatraktár maximális kapacitását. A teljesítmény szint módosításához lásd [méretezheti a számítási - portál](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Gen2

Gen2 2,5 x a Gen1 lekérdezésenként több memóriát biztosít. Az extra memóriát segít a Gen2 a gyors teljesítményt.  A teljesítményszintek DW100c Gen2 esnie DW30000c. 

| Teljesítményszint | Számítási csomópontok | Számítási csomópontok disztribúciók | Adatraktár (GB) memória |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
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

A maximális Gen2 DWU DW30000c, amely rendelkezik 60 számítási csomópontok és a számítási csomópontok egy terjesztési. Például DW30000c, 600 TB-os adattárház dolgozza fel a számítási csomópontok nagyjából 10 TB.

### <a name="gen1"></a>Gen1

A szolgáltatási szintek a Gen1 és DW6000 DW100 eső. 

| Teljesítményszint | Számítási csomópontok | Számítási csomópontok disztribúciók | Adatraktár (GB) memória |
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

## <a name="concurrency-maximums"></a>Párhuzamosság maximális értékei
Annak érdekében, hogy minden egyes lekérdezés hatékonyan végrehajtásához erőforrásokkal rendelkezik, az SQL Data Warehouse egyidejű helyet foglalnak le rendel az egyes lekérdezések erőforrás-használat nyomon követi. A rendszer lekérdezések alapján, fontosság és egyidejű helyet foglalnak le egy várólistába helyezi. Lekérdezések várakozhasson a várólistán addig, amíg elegendő egyidejű helyet foglalnak le nem érhető el. [Fontosság](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) és egyidejű helyet foglalnak le a CPU-rangsorolás határozza meg. További információkért lásd: [a számítási feladatok elemzése](analyze-your-workload.md)

### <a name="gen2"></a>Gen2
 
**Statikus erőforrásosztályok**

Az alábbi táblázat a maximális párhuzamos lekérdezéseket és az egyidejű helyet foglalnak le minden [statikus erőforrásosztály](resource-classes-for-workload-management.md).  

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Párhuzamos időszeletek érhető el | A tárolóhelyek staticrc10 által használt | A tárolóhelyek staticrc20 által használt | A tárolóhelyek staticrc30 által használt | A tárolóhelyek staticrc40 által használt | A tárolóhelyek staticrc50 által használt | A tárolóhelyek staticrc60 által használt | A tárolóhelyek staticrc70 által használt | A tárolóhelyek staticrc80 által használt |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dinamikus erőforrás-osztályok**

> [!NOTE]
> Gen2 smallrc erőforrásosztály dinamikusan ad hozzá a memória, a szolgáltatási szint nő, és csak a DW1000c és 4 és DW100c maximális 32 egyidejű lekérdezéseket támogatja.  Ha a példány méretezett DW1500c, az egyidejű helyet foglalnak le, és a által használt memória mellett smallrc a a szolgáltatási szint növekedése is nő. 
>
>

Az alábbi táblázat a maximális párhuzamos lekérdezéseket és az egyidejű helyet foglalnak le minden [dinamikus erőforrásosztályt](resource-classes-for-workload-management.md). Gen1, ellentétben a dinamikus erőforrásosztályok gen2 dinamikusak valóban.  Gen2 egy 3 – 10 – 22 – 70 százalék memóriafoglalás használja a kis-Közepes-nagy-xlarge erőforrásosztályok minden szolgáltatási szintek között.

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Párhuzamos időszeletek érhető el | A tárolóhelyek smallrc által használt | A tárolóhelyek mediumrc által használt | Largerc által használt tárolóhely | A tárolóhelyek xlargerc által használt |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
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



#### <a name="gen1"></a>Gen1

Statikus erőforrásosztályok

Az alábbi táblázat a maximális párhuzamos lekérdezéseket és az egyidejű helyet foglalnak le minden [statikus erőforrásosztály](resource-classes-for-workload-management.md) a **Gen1**.

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Maximális párhuzamos időszeletek | A tárolóhelyek staticrc10 által használt | A tárolóhelyek staticrc20 által használt | A tárolóhelyek staticrc30 által használt | A tárolóhelyek staticrc40 által használt | A tárolóhelyek staticrc50 által használt | A tárolóhelyek staticrc60 által használt | A tárolóhelyek staticrc70 által használt | A tárolóhelyek staticrc80 által használt |
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

Dinamikus erőforrás-osztályok
> [!NOTE]
> A Gen1 smallrc erőforrásosztály egy rögzített méretű memória lekérdezéseként, hasonló módon a statikus erőforrás osztály staticrc10 foglal le.  Mivel smallrc statikus, méretezhető, 128 lekérdezést tartalmaz. 
>
>

Az alábbi táblázat a maximális párhuzamos lekérdezéseket és az egyidejű helyet foglalnak le minden [dinamikus erőforrásosztályt](resource-classes-for-workload-management.md) a **Gen1**.

| Szolgáltatásszint | Maximális párhuzamos lekérdezések | Párhuzamos időszeletek érhető el | A tárolóhelyek smallrc által használt | A tárolóhelyek mediumrc által használt | Largerc által használt tárolóhely | A tárolóhelyek xlargerc által használt |
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


Ha ezeket a küszöbértékeket egyik feltétele teljesül, új lekérdezések várólistára, és egy első-érkezési hajtja végre.  A lekérdezések befejeződik, és lekérdezéseket és tárhelyek száma a korlátok alá esnek, az SQL Data Warehouse felszabadítja a várólistán lévő lekérdezések. 

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan hozhat erőforrásosztályok optimalizálása tájékozódhat a számítási feladatok további tekintse át a következő cikkeket:
* [Erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md)
* [A számítási feladatok elemzése](analyze-your-workload.md)

