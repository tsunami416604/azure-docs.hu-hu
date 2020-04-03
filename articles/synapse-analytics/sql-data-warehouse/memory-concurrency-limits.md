---
title: Memória- és egyidejűségi korlátok
description: Tekintse meg a memória és egyidejűségi korlátok lerendelt különböző teljesítményszintek és erőforrásosztályok az Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c427c832eb613dddbff33ef6e67af63112e2f136
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586056"
---
# <a name="memory-and-concurrency-limits-for-azure-synapse-analytics"></a>Memória- és egyidejűségi korlátok az Azure Synapse Analytics számára
Tekintse meg a memória és egyidejűségi korlátok lerendelt különböző teljesítményszintek és erőforrásosztályok az Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Adatraktár kapacitásbeállításai
Az alábbi táblázatok az adattárház maximális kapacitását mutatják be különböző teljesítményszinteken. A teljesítményszint módosításáról a [Számítási - portál méretezése](quickstart-scale-compute-portal.md)című témakörben található.

### <a name="service-levels"></a>Szolgáltatási szintek

A szolgáltatási szintek DW100c-től DW30000c-ig terjednek.

| Teljesítményszint | Számítási csomópontok | Disztribúciók számítási csomópontonként | Adattárházonkénti memória (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c lehetőséget            | 1             | 60                             |   300                          |
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

A maximális szolgáltatási szint DW30000c, amely 60 számítási csomóponttal és számítási csomópontonként egy disztribúcióval rendelkezik. A DW30000c 600 TB-os adattárháza például számítási csomópontonként körülbelül 10 TB-ot dolgoz fel.

## <a name="concurrency-maximums-for-workload-groups"></a>Egyidejűségi maximumok a munkaterhelés-csoportokhoz
A számítási [feladatok csoportjainak](sql-data-warehouse-workload-isolation.md)bevezetésével az egyidejűségi résidők fogalma már nem érvényes.  A kérésenkénti erőforrások felosztása százalékalapon történik, és a számítási feladatcsoport-definícióban vannak megadva.  Azonban még az egyidejűségi tárolóhelyek eltávolításával is vannak minimális erőforrások szükségesek a lekérdezések alapján a szolgáltatási szint alapján.  Az alábbi táblázat a lekérdezésenként szükséges erőforrások minimális mennyiségét és az elérhető kapcsolódó egyidejűséget határozta meg. 

|Szolgáltatási szint|Egyidejű lekérdezések maximális|Min % támogatott REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12.5%|
|DW300c|12|8%|
|DW400c|16|6.25%|
|DW500c lehetőséget|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1.5%|
|DW5000c|64|1.5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Egyidejűségi maximumok erőforrásosztályokhoz
Annak érdekében, hogy minden lekérdezés elegendő erőforrással rendelkezzen a hatékony végrehajtáshoz, az erőforrás-kihasználtságot az egyes lekérdezésekhez az egyidejűségi tárolóhelyek hozzárendelésével követi nyomon a rendszer. A rendszer a lekérdezéseket a fontosság és az egyidejűségi tárolóhelyek alapján várólistába helyezi. A lekérdezések a várólistában várnak, amíg elegendő egyidejűségi tárolóhely nem érhető el. [A fontosság](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) és az egyidejűségi tárolóhelyek határozzák meg a PROCESSZOR rangsorolását. További információ: [A munkaterhelés elemzése](analyze-your-workload.md)

**Statikus erőforrásosztályok**

Az alábbi táblázat az egyes [statikus erőforrás-osztályok](resource-classes-for-workload-management.md)maximális egyidejű lekérdezéseit és egyidejűségi tárolóhelyeket mutatja be .  

| Szolgáltatási szint | Egyidejű lekérdezések maximális | Egyidejűségi bővítőhelyek állnak rendelkezésre | A staticrc10 által használt bővítőhelyek | Staticrc20 által használt bővítőhelyek | Staticrc30 által használt bővítőhelyek | Staticrc40 által használt bővítőhelyek | Staticrc50 által használt bővítőhelyek | Staticrc60 által használt bővítőhelyek | A staticrc70 által használt bővítőhelyek | A staticrc80 által használt bővítőhelyek |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c lehetőséget        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
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

**Dinamikus erőforrásosztályok**

Az alábbi táblázat az egyes [dinamikus erőforrás-osztályok](resource-classes-for-workload-management.md)maximális egyidejű lekérdezéseit és egyidejűségi tárolóhelyeket mutatja be . A dinamikus erőforrásosztályok 3-10-22-70 memóriaszázalékos foglalást használnak a kis-közepes-nagy-xnagy erőforrásosztályokhoz az összes szolgáltatási szinten.

| Szolgáltatási szint | Egyidejű lekérdezések maximális | Egyidejűségi bővítőhelyek állnak rendelkezésre | A smallrc által használt nyílások | A mediumrc által használt nyílások | A largerc által használt bővítőhelyek | Az xlargerc által használt nyílások |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c lehetőséget        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
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


Ha nincs elég szabad egyidejűségi bővítőhely a lekérdezés-végrehajtás elindításához, a lekérdezések várólistára kerülnek, és a fontosság alapján hajtják végre.  Ha van egyenértékű fontossági, lekérdezések hajtják végre az első-in, first-out alapon.  A lekérdezések befejeződésével, és a lekérdezések és bővítőhelyek száma a korlátok alá esik, az SQL Data Warehouse várólistás lekérdezéseket szabadít fel. 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogyan használhatja ki az erőforrásosztályokat a munkaterhelés további optimalizálása érdekében, kérjük, olvassa el az alábbi cikkeket:
* [Erőforrásosztályok a munkaterhelés-kezeléshez](resource-classes-for-workload-management.md)
* [A munkaterhelés elemzése](analyze-your-workload.md)