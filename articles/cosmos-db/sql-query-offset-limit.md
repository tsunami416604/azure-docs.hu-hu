---
title: ELTOLÁSi korlát záradéka Azure Cosmos DB
description: Megtudhatja, hogyan használhatja az ELTOLÁSi korlát záradékot, hogy kiugorjon bizonyos értékeket a lekérdezésekben Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: a8df220be211c3c8d8cdeab8a8aebfd35e77ebf8
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732586"
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
  
  Az eltolási korlátot és a KORLÁTot is meg kell adni az OFFSet LIMIT záradékban. Ha nem kötelező `ORDER BY` záradékot használ, az eredményhalmaz a megrendelt értékek kihagyásával jön létre. Ellenkező esetben a lekérdezés az értékek rögzített sorrendjét fogja visszaadni. Ez a záradék mostantól támogatott egyetlen partíción belüli lekérdezések, valamint a több partíciós lekérdezések esetében is.

## <a name="examples"></a>Példák

Például itt egy olyan lekérdezés, amely kihagyja az első értéket, és a második értéket adja vissza (a rezidens város nevének sorrendjében):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Az eredmény a következő:

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

Az eredmény a következő:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [ORDER BY záradék](sql-query-order-by.md)
