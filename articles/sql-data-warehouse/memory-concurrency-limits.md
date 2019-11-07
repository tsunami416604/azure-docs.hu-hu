---
title: Memória és egyidejűségi korlátok
description: Megtekintheti a Azure SQL Data Warehouse a különböző teljesítményszint és erőforrás-osztályok számára lefoglalt memóriát és egyidejűségi korlátokat.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 10/04/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3611e61b303997a4291f4436403bb0a95e647e65
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686037"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>A Azure SQL Data Warehouse memóriája és egyidejűségi korlátai
Megtekintheti a Azure SQL Data Warehouse a különböző teljesítményszint és erőforrás-osztályok számára lefoglalt memóriát és egyidejűségi korlátokat.  

## <a name="data-warehouse-capacity-settings"></a>Adatraktár kapacitásának beállításai
Az alábbi táblázatokban az adatraktár maximális kapacitása látható a különböző teljesítményszint esetén. A teljesítmény szintjének módosításához tekintse meg a [számítások – portál méretezése](quickstart-scale-compute-portal.md)című témakört.

### <a name="service-levels"></a>Szolgáltatási szintek

A szolgáltatási szintek a DW100c és a DW30000c között vannak.

| Teljesítményszint | Számítási csomópontok | Disztribúciók/számítási csomópontok | Memória/adattárház (GB) |
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

A maximális szolgáltatási szint a DW30000c, amelynek 60 számítási csomópontja van, és a számítási csomópontok egy eloszlása. Például egy 600 TB-os adattárház a DW30000c-on a számítási csomópont körülbelül 10 TB-ot dolgoz fel.

## <a name="concurrency-maximums-for-workload-groups"></a>Munkaterhelés-csoportok egyidejűségi maximális száma
A munkaterhelés-csoportok bevezetésével a következő HIVATKOZÁSsal a párhuzamossági bővítőhelyek fogalma már nem érvényes.  A kérelmekre vonatkozó erőforrások százalékos arányban vannak lefoglalva, és a munkaterhelés csoport definíciójában vannak megadva.  A párhuzamossági tárolóhelyek eltávolításával azonban a szolgáltatási szint alapján a lekérdezésekhez szükséges minimális mennyiségű erőforrásra van szükség.  Az alábbi táblázat a szolgáltatási szintek és a hozzájuk kapcsolódó egyidejűségek által lekérdezett erőforrások minimális mennyiségét definiálta. 

|Szolgáltatásszint|Egyidejű lekérdezések maximális száma|REQUEST_MIN_RESOURCE_GRANT_PERCENT által támogatott minimális%|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12,5%|
|DW300c|12|8|
|DW400c|16|6,25%|
|DW500c|20|5%|
|DW1000c|32|3|
|DW1500c|32|3|
|DW2000c|48|2|
|DW2500c|48|2|
|DW3000c|64|1,5%|
|DW5000c|64|1,5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Erőforrás-osztályok egyidejűségi maximális száma
Annak biztosítása érdekében, hogy az egyes lekérdezések elegendő erőforrással legyenek végrehajtva, SQL Data Warehouse az erőforrás-kihasználtságot az egyes lekérdezésekhez tartozó egyidejűségi tárolóhelyek hozzárendelésével nyomon követheti. A rendszer a lekérdezéseket a fontos és a párhuzamossági tárolóhelyek alapján a várólistára helyezi. A lekérdezések csak akkor várnak a várólistára, ha elegendő egyidejűségi tárolóhely áll rendelkezésre. A [fontosság](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) és a egyidejűségi tárolóhelyek a CPU rangsorolását határozzák meg. További információ: [a munkaterhelés elemzése](analyze-your-workload.md)

**Statikus erőforrás-osztályok**

A következő táblázat a [statikus erőforrások osztályának](resource-classes-for-workload-management.md)maximális egyidejű lekérdezéseit és egyidejűségi tárolóhelyeit mutatja be.  

| Szolgáltatásszint | Egyidejű lekérdezések maximális száma | Elérhető egyidejűségi bővítőhelyek | A staticrc10 által használt bővítőhelyek | A staticrc20 erőforrásosztályhoz által használt bővítőhelyek | A staticrc30 által használt bővítőhelyek | A staticrc40 által használt bővítőhelyek | A staticrc50 által használt bővítőhelyek | A staticrc60 által használt bővítőhelyek | A staticrc70 által használt bővítőhelyek | A staticrc80 által használt bővítőhelyek |
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

Az alábbi táblázat az egyes [dinamikus erőforrás-osztályok](resource-classes-for-workload-management.md)maximális egyidejű lekérdezéseit és egyidejűségi tárolóhelyeit mutatja be. A dinamikus erőforrás-osztályok a 3-10-22-70-es memória-kiosztást használják a kis-közepes méretű XLarge erőforrás-osztályokhoz az összes szolgáltatási szinten.

| Szolgáltatásszint | Egyidejű lekérdezések maximális száma | Elérhető egyidejűségi bővítőhelyek | A smallrc által használt bővítőhelyek | A mediumrc által használt bővítőhelyek | A largerc által használt bővítőhelyek | A xlargerc által használt bővítőhelyek |
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


Ha nincs elegendő egyidejűségi tárolóhely a lekérdezés végrehajtásának megkezdéséhez, a lekérdezéseket a rendszer a fontosság alapján várólistára helyezi és végrehajtja.  Ha ennek megfelelő jelentősége van, a lekérdezéseket a rendszer az első, első kijelentkezési alapon hajtja végre.  A lekérdezések befejeződése után a lekérdezések és a bővítőhelyek száma a határértékek alá csökken, SQL Data Warehouse kiadások várólistán lévő lekérdezések. 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogyan használhat erőforrás-osztályokat a számítási feladatok további optimalizálása érdekében, tekintse át a következő cikkeket:
* [Erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md)
* [Számítási feladatok elemzése](analyze-your-workload.md)