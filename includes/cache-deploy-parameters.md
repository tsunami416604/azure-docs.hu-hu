---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: dd9700c9472e07daf294eca12b766e3dc4832955
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111644"
---
### <a name="cacheskuname"></a>cacheSKUName
Az új Azure Cache redis tarifacsomagját.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
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
Az új Azure Cache Redis-példány mérete. 

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


A sablon meghatározza az értékeket, amelyeknél engedélyezve van ez a paraméter (0, 1, 2, 3, 4, 5 vagy 6) a, és hozzárendeli az alapértelmezett érték (0), ha nem ad meg értéket. Ezeknek a számoknak felel meg a következő gyorsítótár méretének: 0 = 250 MB-os, 1 = 1 GB-os, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

