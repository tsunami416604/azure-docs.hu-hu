---
title: ORDER BY záradék az Azure Cosmos DB-ben
description: Ismerje meg az Azure Cosmos DB SQL ORDER BY záradékát. Az SQL használata Azure Cosmos DB JSON lekérdezési nyelvként.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641401"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY záradék az Azure Cosmos DB-ben

A `ORDER BY` választható záradék a lekérdezés által visszaadott eredmények rendezési sorrendjét adja meg.

## <a name="syntax"></a>Szintaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentumok
  
- `<sort_specification>`  
  
   Megadja azt a tulajdonságot vagy kifejezést, amely alapján rendezni szeretné a lekérdezés eredményhalmazát. A rendezési oszlop névként vagy tulajdonságaliasként is megadható.  
  
   Több tulajdonság is megadható. A tulajdonságneveknek egyedinek kell lenniük. A rendezési tulajdonságok sorrendje határozza `ORDER BY` meg a rendezett eredményhalmaz t. Ez azt, hogy az eredményhalmaz az első tulajdonság szerint van rendezve, majd a rendezett lista a második tulajdonság szerint van rendezve, és így tovább.  
  
   A `ORDER BY` záradékban hivatkozott tulajdonságneveknek vagy a választólistában szereplő tulajdonságnak, vagy a `FROM` záradékban meghatározott gyűjteményben meghatározott tulajdonságnak kell megfelelniük, anélkül, hogy bármilyen kétértelműség lenne.  
  
- `<sort_expression>`  
  
   Egy vagy több tulajdonságot vagy kifejezést ad meg, amelyek alapján rendezni szeretné a lekérdezés eredményhalmazát.  
  
- `<scalar_expression>`  
  
   A részleteket a [Skaláris kifejezések](sql-query-scalar-expressions.md) című szakaszban találja.  
  
- `ASC | DESC`  
  
   Itt adható meg, hogy a megadott oszlopértékeit növekvő vagy csökkenő sorrendben kell rendezni. `ASC`a legalacsonyabb értéktől a legmagasabb értékig. `DESC`a legmagasabb értéktől a legalacsonyabb értékig. `ASC`az alapértelmezett rendezési sorrend. A null értékeket a lehető legalacsonyabb értékként kezeli a függvény.  
  
## <a name="remarks"></a>Megjegyzések  
  
   A `ORDER BY` záradék megköveteli, hogy az indexelési házirend tartalmazzon indexet a rendezve lévő mezőkhöz. Az Azure Cosmos DB lekérdezési futásidejű támogatja a tulajdonság név és nem számított tulajdonságok rendezését. Az Azure Cosmos `ORDER BY` DB több tulajdonságot is támogat. Ahhoz, hogy több ORDER BY tulajdonsággal rendelkező lekérdezést futtasson, [összetett indexet](index-policy.md#composite-indexes) kell definiálnia a rendezve lévő mezőkön.

> [!Note]
> Ha a rendezendő tulajdonságok egyes dokumentumokesetében nem definiálhatók, és egy ORDER BY lekérdezésben szeretné beolvasni őket, akkor ezt az elérési utat kifejezetten fel kell tüntetnie az indexben. Az alapértelmezett indexelési házirend nem teszi lehetővé azoknak a dokumentumoknak a lekérését, amelyeknél a rendezési tulajdonság nincs definiálva. [Tekintse át a hiányzó mezőkkel rendelkező dokumentumokra vonatkozó példalekérdezéseket.](#documents-with-missing-fields)

## <a name="examples"></a>Példák

Az alábbiakban például egy olyan lekérdezést olvashat be, amely a helyi város nevének növekvő sorrendjében olvassa be a családokat:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Az eredmény a következő:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

A következő lekérdezés `id`az elemek létrehozásának dátuma szerint olvassa be a családsakat. A `creationDate` cikk az *1970.*

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Az eredmény a következő:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Emellett több tulajdonság alapján is rendelhet. A több tulajdonság szerint rendelésre leadott lekérdezéshez [összetett indexszükséges.](index-policy.md#composite-indexes) Tekintse meg a következő lekérdezést:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Ez a lekérdezés `id` a városnevének növekvő sorrendjében olvassa be a családot. Ha több elem nek ugyanaz a városneve, `creationDate` a lekérdezés csökkenő sorrendben lesz megrendezve.

## <a name="documents-with-missing-fields"></a>Hiányzó mezőkkel rendelkező dokumentumok

`ORDER BY` Az alapértelmezett indexelési házirenddel rendelkező tárolókon futtatott lekérdezések nem adnak vissza olyan dokumentumokat, ahol a rendezési tulajdonság nincs definiálva. Ha olyan dokumentumokat szeretne felvenni, amelyeknél a rendezési tulajdonság nincs definiálva, akkor ezt a tulajdonságot kifejezetten vegye fel az indexelési házirendbe.

Például az itt egy olyan indexelési házirenddel rendelkező tároló, `"/*"`amely nem tartalmaz kifejezetten elérési utakat a következők mellett:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Ha olyan lekérdezést `lastName` futtat, `Order By` amely a záradékban szerepel, `lastName` az eredmények csak olyan dokumentumokat tartalmaznak, amelyektulajdonsága definiálva van. Nem határoztunk meg explicit módon `lastName` mellékelt elérési `lastName` utat, így a nélkül özve lévő dokumentumok nem jelennek meg a lekérdezés eredményében.

Itt van egy lekérdezés, amely két dokumentum ravatalozott, `lastName` amelyek közül az egyiknem rendelkezik `lastName` definiált:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Az eredmények csak a definiált `lastName`dokumentumot tartalmazzák:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Ha frissítjük a tároló indexelési szabályzatát, `lastName`hogy explicit módon tartalmazza a elérési útját, akkor a lekérdezés eredményében egy nem definiált rendezési tulajdonsággal rendelkező dokumentumokat is tartalmazni fogunk. Explicit módon meg kell határoznia azt az elérési utat, amely a skaláris értékhez vezet (és nem azon túl). Az indexelési házirendben az `?` elérési út definíciójában lévő karaktert `lastName` kell használnia annak érdekében, hogy explicit módon indexelje a tulajdonságot, és ne legyen rajta túl további beágyazott elérési út. Ha `Order By` a lekérdezés [összetett indexet](index-policy.md#composite-indexes)használ, az eredmények mindig tartalmazni fogják a nem definiált rendezési tulajdonsággal rendelkező dokumentumokat a lekérdezés eredményében.

Az alábbiakban egy mintaindexelési házirend et `lastName` olvashat, amely lehetővé teszi, hogy a nem definiált dokumentumok megjelenjenek a lekérdezés eredményében:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Ha ismét futtatja ugyanazt a `lastName` lekérdezést, a hiányzó dokumentumok jelennek meg először a lekérdezés eredményében:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Az eredmény a következő:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Ha a rendezési `DESC`sorrendet a `lastName` , a hiányzó dokumentumok jelennek meg utoljára a lekérdezés eredménye:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Az eredmény a következő:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>További lépések

- [Első lépések](sql-query-getting-started.md)
- [Az Azure Cosmos DB indexelési szabályzatai](index-policy.md)
- [OFFSET LIMIT záradék](sql-query-offset-limit.md)
