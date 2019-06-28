---
title: Az Azure Cosmos DB ELTOLÁS korlát záradék
description: További információk a korlát ELTOLÁS záradék az Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342481"
---
# <a name="offset-limit-clause"></a>ELTOLÁS korlát záradék

Az ELTOLÁS korlát záradék egy nem kötelező záradék, hagyja ki, majd vennie néhány a lekérdezésből származó értékek száma. Az ELTOLÁS száma és a korlát a korlát ELTOLÁS záradékban szükségesek.

ELTOLÁS KORLÁTOT egy ORDER BY záradékkal együtt kell használni, amikor az eredményhalmaz kihagyása végrehajtásával előállítása, és igénybe vehet a rendezett értékekhez. Nincs ORDER BY záradék használata esetén azt eredményezi értékek determinisztikus sorrendjét.

## <a name="syntax"></a>Szintaxis
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentumok

- `<offset_amount>`

   Adja meg az egész számmal megadott elemek, amelyek a lekérdezés eredményeinek ki kell hagyni.

- `<limit_amount>`
  
   Adja meg az elemek, amelyek tartalmaznia kell a lekérdezés eredményeit az egész számmal megadott

## <a name="remarks"></a>Megjegyzések
  
  Az ELTOLÁS száma és a korlát száma is szükségesek a korlát ELTOLÁS záradékban. Ha egy nem kötelező `ORDER BY` záradék használata esetén az eredményhalmaz előállítása a skip rendezett értékek végrehajtásával. Ellenkező esetben a lekérdezés visszaadja az olyan rögzített értékek sorrendjét. Jelenleg ez a záradék támogatott lekérdezések csak egyetlen partíción belül, partícióra kiterjedő lekérdezések még nem támogatják ezt.

## <a name="examples"></a>Példák

Ha például a következő lekérdezés, amely kihagyja az első értékét adja vissza (a legerősebbtől a tartózkodási város nevét a) a második érték:

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

A következő lekérdezés, amely kihagyja az első érték második értéket adja vissza (rendelés) nélkül:

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

## <a name="next-steps"></a>További lépések

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [ORDER BY záradék](sql-query-order-by.md)
