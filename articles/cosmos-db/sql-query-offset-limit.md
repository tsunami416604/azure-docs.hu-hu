---
title: OFFSET LIMIT záradék az Azure Cosmos DB-ben
description: Ismerje meg, hogyan használhatja az OFFSET LIMIT záradékot bizonyos értékek kihagyására és bizonyos értékek rekedésére az Azure Cosmos DB lekérdezésekor
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771573"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>OFFSET LIMIT záradék az Azure Cosmos DB-ben

Az OFFSET LIMIT záradék egy választható záradék, amelyet ki kell hagyni, majd bizonyos számú értéket kell kivenni a lekérdezésből. Az ELTOLÁS-szám és a LIMIT-szám az OFFSET LIMIT záradékban szükséges.

Ha az ELTOLÁSi KORLÁTOT EGY ORDER BY záradékkal együtt használja, az eredményhalmaz a megrendelt értékek kihagyásával és átvállalásával jön létre. Ha nem használ ORDER BY záradékot, az az értékek determinisztikus sorrendjét eredményezi.

## <a name="syntax"></a>Szintaxis
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentumok

- `<offset_amount>`

   Megadja azon elemek egész számát, amelyeket a lekérdezés eredményének ki kell hagynia.

- `<limit_amount>`
  
   Megadja azon elemek egész számát, amelyeket a lekérdezés eredményének tartalmaznia kell

## <a name="remarks"></a>Megjegyzések
  
  Mind `OFFSET` a számláló, mind `LIMIT` a `OFFSET LIMIT` számláló szükséges a záradékban. Ha egy `ORDER BY` választható záradékot használ, az eredményhalmaz a rendezett értékek átugrásával készül el. Ellenkező esetben a lekérdezés az értékek rögzített sorrendjét adja vissza.

  A lekérdezés vt.-díja az ellenérték növekedésével `OFFSET LIMIT` növekszik. Több oldalnyi eredményt tartalmazó lekérdezések esetén általában ajánlott a folytatási tokenek használata. A folytatási tokenek egy "könyvjelző" azon a helyen, ahol a lekérdezés később folytatódhat. A használata `OFFSET LIMIT`esetén nincs "könyvjelző". Ha vissza szeretné adni a lekérdezés következő oldalát, akkor az elejétől kell kezdenie.
  
  Olyan esetekhez kell használnia, `OFFSET LIMIT` amikor teljesen ki szeretné hagyni a dokumentumokat, és ügyfélerőforrásokat szeretne menteni. Például akkor használja, `OFFSET LIMIT` ha az 1000. A háttérrendszeren `OFFSET LIMIT` továbbra is betölti az egyes dokumentumokat, beleértve az átmaradotteket is. A teljesítményelőnye az ügyfél-erőforrások megtakarítása a szükségtelen dokumentumok feldolgozásának elkerülésével.

## <a name="examples"></a>Példák

Az alábbiakban például egy lekérdezést akkor olvashat, amely kihagyja az első értéket, és a második értéket adja vissza (a helyi város nevének sorrendjében):

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

Az alábbiakban egy lekérdezést, amely kihagyja az első értéket, és visszaadja a második értéket (rendelés nélkül):

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

## <a name="next-steps"></a>További lépések

- [Kezdetekhez](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [ORDER BY záradék](sql-query-order-by.md)
