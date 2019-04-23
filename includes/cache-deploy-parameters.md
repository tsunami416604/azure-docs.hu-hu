---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118597"
---
### <a name="cacheskuname"></a>cacheSKUName

Az új Azure Cache redis tarifacsomagját.

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

A sablon meghatározza az értékeket, amelyeknél engedélyezve van a ezt a paramétert (alapszintű, Standard vagy prémium), és hozzárendeli egy alapértelmezett értéket (alapszintű), ha nem ad meg értéket. Alapszintű egyetlen csomópont, akár több mérettel 53 GB-os biztosít. Standard két csomópontos elsődleges/replika, többféle méret akár 53 GB-os és a 99,9 %-os SLA-t biztosít.

### <a name="cacheskufamily"></a>cacheSKUFamily

A termékváltozat a család.

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

Az új Azure Cache Redis-példány mérete.

Az alapszintű és standard szintű családhoz:

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

Az érték cache prémium szintű kapacitáshoz van definiálva azonos, kivéve az engedélyezett értékek Futtatás 1-től 5 helyett 0, 6.

A sablon az egész számok, amelyeknél engedélyezve van ez a paraméter (0 – 6. az alapszintű és standard szintű családok; 1-5 a prémium szintű termékcsalád) határozza meg. Ha nem ad meg értéket, a sablon az alapszintű és Standard, Premium 1 rendeli hozzá egy alapértelmezett érték a 0.

Az értékek következő gyorsítótár méretének felelnek meg:

| Value | Alapszintű és Standard<br>Gyorsítótár mérete | Prémium<br>Gyorsítótár mérete |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (alapértelmezett)                 | n/a                   |
| 1     | 1 GB                             | 6 GB (alapértelmezett)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/a                   |
