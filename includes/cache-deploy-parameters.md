---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178928"
---
### <a name="cacheskuname"></a>cacheSKUName

The pricing tier of the new Azure Cache for Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

A sablon meghatározza az ehhez a paraméterhez engedélyezett értékeket (Alapszintű, Standard vagy Prémium), és alapértelmezett értéket (Alapérték) rendel hozzá, ha nincs megadva érték. Az Alapszintű egy csomópontot biztosít, amely akár 53 GB-os méretben is elérhető. A Standard kétcsomópontos elsődleges/replika-verziót biztosít több féle méretben, akár 53 GB-os és 99,9%-os SLA-val.

### <a name="cacheskufamily"></a>cacheSKUFamily

A család a sku-ért.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Az új Azure-gyorsítótár a Redis-példány mérete.

Az alap- és standard családok esetében:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

A prémium érték gyorsítótár kapacitása ugyanaz, kivéve az engedélyezett értékek futtatása 1-től 5 helyett 0-6.

A sablon meghatározza azokat az egész értékeket, amelyek ehhez a paraméterhez engedélyezettek (0-tól 6-ig az Alap- és Standard családokesetében, 1-től 5-ig a Prémium család esetében). Ha nincs megadva érték, a sablon 0 alapértelmezett értéket rendel hozzá az Alapszintű és a Standard, 1 a Prémium értékhez.

Az értékek a következő gyorsítótár-méreteknek felelnek meg:

| Érték | Alap és standard<br>gyorsítótár mérete | Prémium<br>gyorsítótár mérete |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (alapértelmezett)                 | n/a                   |
| 1     | 1 GB                             | 6 GB (alapértelmezett)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/a                   |
