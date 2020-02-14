---
title: ORDER BY záradék Azure Cosmos DB
description: Az SQL ORDER BY záradék megismerése Azure Cosmos DB. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188732"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY záradék Azure Cosmos DB

A választható ORDER BY záradék a lekérdezés által visszaadott eredmények rendezési sorrendjét határozza meg.

## <a name="syntax"></a>Szintaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentumok
  
- `<sort_specification>`  
  
   Megadja egy tulajdonságot vagy a lekérdezés eredményhalmazában rendezéshez használandó kifejezés. A rendezési oszlop nevet vagy tulajdonság-aliasként is megadható.  
  
   Több tulajdonság is megadható. A tulajdonságok nevének egyedinek kell lennie. Az ORDER BY záradékban szereplő rendezési tulajdonságok sorrendje határozza meg a rendezett eredményhalmaz szervezetét. Azt jelenti az eredményhalmaz az első tulajdonság szerint van rendezve, és ezután a rendezett lista van rendezve, a második tulajdonságot, és így tovább.  
  
   Az ORDER BY záradékban hivatkozott tulajdonságok neveinek meg kell egyezniük a kiválasztási listán szereplő tulajdonsággal vagy a FROM záradékban meghatározott tulajdonsággal, kétértelműség nélkül.  
  
- `<sort_expression>`  
  
   Egy vagy több olyan tulajdonságot vagy kifejezést ad meg, amelyeken a lekérdezési eredményhalmaz sorba rendezésére kerül sor.  
  
- `<scalar_expression>`  
  
   A részletekért tekintse meg a [skaláris kifejezések](sql-query-scalar-expressions.md) szakaszt.  
  
- `ASC | DESC`  
  
   Itt adhatja meg, hogy a megadott oszlopban szereplő értékek növekvő vagy csökkenő sorrendben jelennek meg. ASC rendezi a legkisebb érték a legmagasabb érték. DESC rendezi a legmagasabb érték a legkisebb értéket. ASC az alapértelmezett rendezési sorrend. A legkisebb lehetséges értékek NULL értéket kell kezelni.  
  
## <a name="remarks"></a>Megjegyzések  
  
   A `ORDER BY` záradék megköveteli, hogy az indexelési házirend tartalmazzon egy indexet a rendezés alatt álló mezőkhöz. A Azure Cosmos DB lekérdezési futtatókörnyezet támogatja a tulajdonságok neve szerinti rendezést, és nem a számított tulajdonságokkal. Azure Cosmos DB több `ORDER BY` tulajdonságot is támogat. Ha több SORRENDet tartalmazó lekérdezést szeretne futtatni, definiálnia kell egy [összetett indexet](index-policy.md#composite-indexes) a rendezés alatt álló mezőkben.

> [!Note]
> Ha a rendezés alatt álló tulajdonságok egyes dokumentumok esetében nem definiálhatók, és lekérdezési sorrendben szeretné lekérni őket, explicit módon fel kell vennie ezt az útvonalat az indexbe. Az alapértelmezett indexelési házirend nem engedélyezi a dokumentumok lekérését, ha a rendezési tulajdonság nincs definiálva. [Tekintse át például a dokumentumok lekérdezéseit néhány hiányzó mezővel](#documents-with-missing-fields).

## <a name="examples"></a>Példák

Például itt egy olyan lekérdezés, amely a családokat a rezidens város nevének növekvő sorrendjében kéri le:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Az eredmények a következők:

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

A következő lekérdezés lekéri a család `id`s-t az elemek létrehozási dátumának megfelelően. Az Item `creationDate` egy szám, amely az *időpontot*jelöli, vagy az eltelt időt a Jan. 1, 1970 másodpercben.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Az eredmények a következők:

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

Emellett több tulajdonság is megrendelhető. A több tulajdonság által megrendelt lekérdezéseknek [összetett indexre](index-policy.md#composite-indexes)van szükségük. Vegye figyelembe a következő lekérdezést:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

A lekérdezés a város nevének növekvő sorrendjében kérdezi le a család `id`ét. Ha több elem is ugyanaz a város neve, a lekérdezés sorrendjét a `creationDate` csökkenő sorrendbe kell rendezni.

## <a name="documents-with-missing-fields"></a>Hiányzó mezőket tartalmazó dokumentumok

Az alapértelmezett indexelési házirenddel rendelkező tárolókban futtatott `ORDER BY` lekérdezések nem adnak vissza olyan dokumentumokat, amelyekben a rendezés tulajdonság nincs meghatározva. Ha olyan dokumentumokat szeretne szerepeltetni, amelyekben a rendezési tulajdonság nincs meghatározva, explicit módon fel kell vennie ezt a tulajdonságot az indexelési házirendbe.

Például itt található egy olyan indexelő házirenddel rendelkező tároló, amely nem tartalmaz explicit módon `"/*"`on kívüli elérési utat:

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

Ha olyan lekérdezést futtat, amely `lastName` tartalmaz a `Order By` záradékban, akkor az eredmények csak a `lastName` tulajdonsággal megadott dokumentumokat tartalmazzák. Nem adott meg explicit módon belefoglalt elérési utat a `lastName`, így a `lastName` nélküli dokumentumok nem jelennek meg a lekérdezés eredményei között.

Itt látható egy olyan lekérdezés, amely két dokumentumon `lastName` szerint rendezi, amelyek közül az egyiknek nincs `lastName` definiálva:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Az eredmények csak a megadott `lastName`tartalmazó dokumentumot tartalmazzák:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Ha a tároló indexelési házirendjét úgy frissíti, hogy explicit módon tartalmazza a `lastName`elérési útját, a lekérdezés eredményeiben a nem definiált rendezési tulajdonsággal rendelkező dokumentumok is szerepelni fognak. Explicit módon meg kell határozni az elérési utat a skaláris értékhez (és nem a fentiekhez képest). Az indexelési házirendben az elérésiút-definícióban szereplő `?` karaktert kell használnia annak biztosításához, hogy explicit módon indexelje a tulajdonságot, `lastName` és ne legyenek további beágyazott útvonalak.

Az alábbi példa egy minta-indexelési házirendet tartalmaz, amely lehetővé teszi, hogy a dokumentumok nem definiált `lastName` jelenjenek meg a lekérdezés eredményei között:

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

Ha ugyanezt a lekérdezést is futtatja, akkor a hiányzó dokumentumok `lastName` megjelennek a lekérdezés eredményei között:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Az eredmények a következők:

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

Ha módosítja a rendezési sorrendet a `DESC`re, a hiányzó dokumentumok `lastName` megjelennek a lekérdezés eredményei között:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Az eredmények a következők:

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

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [Az Azure Cosmos DB indexelési szabályzatai](index-policy.md)
- [ELTOLÁSi korlát záradéka](sql-query-offset-limit.md)
