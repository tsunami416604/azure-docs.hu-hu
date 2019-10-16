---
title: ELTOLÁSi korlát záradéka Azure Cosmos DB
description: Tudnivalók a Azure Cosmos DB ELTOLÁSi korlát záradékáról.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 7aae56783f83f13b50321c88d69f07d910e589dd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326870"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>ELTOLÁSi korlát záradéka Azure Cosmos DB

Az ELTOLÁSi korlát záradék egy opcionális záradék, amely a lekérdezésből néhány értéket igénybe vehet. Az eltolási korlátot és a HATÁRÉRTÉKek számát kötelező megadni az ofszet LIMIT záradékban.

Ha az ELTOLÁSi KORLÁTot ORDER BY záradékkal együtt használja, az eredményhalmaz a kihagyás és a megrendelt értékek megtételével jön létre. Ha nem használ ORDER BY záradékot, a rendszer determinisztikus sorrendet eredményez.

## <a name="syntax"></a>Szintaxis
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentumok

- `<offset_amount>`

   Meghatározza, hogy hány elemből kell kihagyni a lekérdezés eredményeit.

- `<limit_amount>`
  
   Meghatározza, hogy hány elemnek kell szerepelnie a lekérdezés eredményei között.

## <a name="remarks"></a>Megjegyzések
  
  Az eltolási korlátot és a KORLÁTot is meg kell adni az OFFSet LIMIT záradékban. Ha nem kötelező `ORDER BY` záradékot használ, az eredményhalmaz a megrendelt értékek kihagyásával jön létre. Ellenkező esetben a lekérdezés az értékek rögzített sorrendjét fogja visszaadni. Jelenleg ez a záradék csak egy partíción belüli lekérdezésekhez támogatott, a több partíciós lekérdezések még nem támogatják azt.

## <a name="examples"></a>Példák

Például itt egy olyan lekérdezés, amely kihagyja az első értéket, és a második értéket adja vissza (a rezidens város nevének sorrendjében):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Az eredmények a következők:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Íme egy olyan lekérdezés, amely kihagyja az első értéket, és a második értéket adja vissza (megrendelés nélkül):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Az eredmények a következők:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [ORDER BY záradék](sql-query-order-by.md)
