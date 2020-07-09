---
title: EndsWith Azure Cosmos DB lekérdezési nyelven
description: A Azure Cosmos DB ENDSWITH SQL System függvényének megismerése egy olyan logikai érték visszaadásához, amely azt jelzi, hogy az első karakterlánc kifejezése a másodikval végződik-e
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3d37786c7364b07228d1d8d6540e7b6d8a174eb5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84322686"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

Egy olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc kifejezése a másodperctel végződik-e  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Egy karakterlánc-kifejezés.  
  
*str_expr2*  
   A *str_expr1*végéhez összehasonlítandó karakterlánc-kifejezés.

*bool_expr* Nem kötelező érték az eset figyelmen kívül hagyásával. Ha igaz értékre van állítva, a ENDSWITH kis-és nagybetűket nem megkülönböztető keresést hajt végre. Ha nincs megadva, ez az érték hamis.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
A következő példa ellenőrzi, hogy az "ABC" karakterlánc a "b" és a "bC" karakterlánccal végződik-e.  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

A EndsWith RU-fogyasztása növekedni fog, mivel a rendszerfunkcióban megnövekszik a tulajdonsága. Más szóval, ha azt ellenőrzi, hogy egy tulajdonság értéke egy bizonyos karakterlánccal végződik-e, az RU-lekérdezés díja az adott tulajdonság lehetséges értékeinek számától függ.

Vegyük például a következő két tulajdonságot: város és ország. A város Bíborosa 5 000, az ország pedig 200. Íme két példa a lekérdezésekre:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

Az első lekérdezés valószínűleg több RUs-t fog használni a második lekérdezésnél, mert a város számos országa magasabb.

Ha egyes dokumentumoknál a EndsWith nagyobb, mint 1 KB, akkor a lekérdezési motornak be kell töltenie ezeket a dokumentumokat. Ebben az esetben a lekérdezési motor nem tudja teljes mértékben kiértékelni a EndsWith indextel. A EndsWith-díj akkor lesz magas, ha nagy számú, 1 KB-nál nagyobb méretű dokumentumot tartalmaz.

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
