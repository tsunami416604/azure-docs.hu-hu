---
title: ELTOLÁSi korlát záradéka Azure Cosmos DB
description: Megtudhatja, hogyan használhatja az ELTOLÁSi korlát záradékot, hogy kiugorjon bizonyos értékeket a lekérdezésekben Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76771573"
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
  
  A `OFFSET` számnak és a `LIMIT` számnak is szerepelnie `OFFSET LIMIT` kell a záradékban. Ha nem kötelező `ORDER BY` záradékot használ, az eredményhalmaz a megrendelt értékek kihagyásával jön létre. Ellenkező esetben a lekérdezés az értékek rögzített sorrendjét fogja visszaadni.

  A lekérdezés RU-díja növekedni `OFFSET LIMIT` fog, ahogy az eltolási feltételek száma növekszik. Az eredmények több oldalát tartalmazó lekérdezések esetében általában a folytatási tokenek használatát javasoljuk. A folytatási tokenek egy "könyvjelző" azon hely számára, ahol a lekérdezés később folytatható. Ha használja `OFFSET LIMIT`, nincs "könyvjelző". Ha a lekérdezés következő lapját szeretné visszaadni, az elejétől kell kezdődnie.
  
  Olyan esetekre `OFFSET LIMIT` érdemes használni, amikor a dokumentumokat teljes mértékben ki szeretné hagyni, és az ügyfél erőforrásait menti. `OFFSET LIMIT` Ha például a 1000th-lekérdezés eredményét szeretné kihagyni, és nem kell megtekintenie az 1 – 999 eredményt. A háttérrendszer `OFFSET LIMIT` továbbra is betölti az egyes dokumentumokat, beleértve a kihagyott tartalmakat is. A teljesítmény előnye az ügyfelek erőforrásainak megtakarítása a szükségtelen dokumentumok feldolgozásának elkerülésével.

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

## <a name="next-steps"></a>További lépések

- [Első lépések](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [ORDER BY záradék](sql-query-order-by.md)
