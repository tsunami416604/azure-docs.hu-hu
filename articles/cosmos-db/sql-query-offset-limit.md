---
title: ELTOLÁSi korlát záradéka Azure Cosmos DB
description: Megtudhatja, hogyan használhatja az ELTOLÁSi korlát záradékot, hogy kiugorjon bizonyos értékeket a lekérdezésekben Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
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
  
  A `OFFSET LIMIT` záradékban a `OFFSET` és a `LIMIT` darabszám is szükséges. Ha nem kötelező `ORDER BY` záradékot használ, az eredményhalmaz a megrendelt értékek kihagyásával jön létre. Ellenkező esetben a lekérdezés az értékek rögzített sorrendjét fogja visszaadni.

  A `OFFSET LIMIT`i lekérdezés RU-díja növekedni fog, ahogy az eltolási feltételek száma növekszik. Az eredmények több oldalát tartalmazó lekérdezések esetében általában a folytatási tokenek használatát javasoljuk. A folytatási tokenek egy "könyvjelző" azon hely számára, ahol a lekérdezés később folytatható. Ha `OFFSET LIMIT`használ, nincs "könyvjelző". Ha a lekérdezés következő lapját szeretné visszaadni, az elejétől kell kezdődnie.
  
  Ha a dokumentumokat teljes egészében szeretné kihagyni, és az ügyfelek erőforrásainak mentésére van szüksége, használja a `OFFSET LIMIT`. Ha például a 1000th-lekérdezés eredményét szeretné kihagyni, és nem kell megtekintenie az 1 – 999 eredményt, akkor a `OFFSET LIMIT`t kell használnia. A háttérben `OFFSET LIMIT` továbbra is betölti az egyes dokumentumokat, beleértve azokat is, amelyek kimaradnak. A teljesítmény előnye az ügyfelek erőforrásainak megtakarítása a szükségtelen dokumentumok feldolgozásának elkerülésével.

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
