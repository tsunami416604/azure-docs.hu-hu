---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: 1ddb81de479317a098f9de8aa5756cbaae59cb72
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331077"
---
### <a name="cacheskuname"></a>cacheSKUName
Az új Azure Redis Cache tarifacsomagját.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

A sablon meghatározza az értékeket, amelyeknél engedélyezve van a ezt a paramétert (alap vagy Standard), és hozzárendeli egy alapértelmezett értéket (alapszintű), ha nem ad meg értéket. Alapszintű egyetlen csomópont, akár több mérettel 53 GB-os biztosít.
Standard két csomópontos elsődleges/replika, többféle méret akár 53 GB-os és a 99,9 %-os SLA-t biztosít.

### <a name="cacheskufamily"></a>cacheSKUFamily
A termékváltozat a család.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Az új Azure Redis Cache-példány mérete. 

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
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


A sablon meghatározza az értékeket, amelyeknél engedélyezve van ez a paraméter (0, 1, 2, 3, 4, 5 vagy 6) a, és hozzárendeli az alapértelmezett érték (0), ha nem ad meg értéket. Ezeknek a számoknak felel meg a következő gyorsítótár méretének: 0 = 250 MB-os, 1 = 1 GB-os, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

