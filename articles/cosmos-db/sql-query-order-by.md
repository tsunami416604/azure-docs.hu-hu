---
title: ORDER BY záradék Azure Cosmos DB
description: Az SQL ORDER BY záradék megismerése Azure Cosmos DB. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641401"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY záradék Azure Cosmos DB

A választható `ORDER BY` záradék a lekérdezés által visszaadott eredmények rendezési sorrendjét határozza meg.

## <a name="syntax"></a>Szintaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentumok
  
- `<sort_specification>`  
  
   Megadja azt a tulajdonságot vagy kifejezést, amelyen a lekérdezési eredményhalmaz rendezése történik. A rendezési oszlop nevet vagy tulajdonság-aliasként is megadható.  
  
   Több tulajdonság is megadható. A tulajdonságok nevének egyedinek kell lennie. A rendezési tulajdonságok sorrendje a `ORDER BY` záradékban a rendezett eredményhalmaz szervezetét határozza meg. Ez azt jelenti, hogy az eredményhalmaz az első tulajdonság szerint van rendezve, és a rendezett listát a második tulajdonság rendezi, és így tovább.  
  
   A `ORDER BY` záradékban hivatkozott tulajdonságnév a kiválasztási listán szereplő tulajdonságnak vagy a `FROM` záradékban megadott gyűjteményben, kétértelműség nélkül definiált tulajdonságnak kell lennie.  
  
- `<sort_expression>`  
  
   Egy vagy több olyan tulajdonságot vagy kifejezést ad meg, amelyeken a lekérdezési eredményhalmaz sorba rendezésére kerül sor.  
  
- `<scalar_expression>`  
  
   A részletekért tekintse meg a [skaláris kifejezések](sql-query-scalar-expressions.md) szakaszt.  
  
- `ASC | DESC`  
  
   Megadja, hogy a megadott oszlopban szereplő értékeket növekvő vagy csökkenő sorrendbe kell rendezni. `ASC`a legalacsonyabb értékről a legmagasabb értékre rendezi a rendszer. `DESC`a legmagasabb értékről a legalacsonyabb értékre rendezi a rendszer. `ASC`az alapértelmezett rendezési sorrend. A null értékeket a lehető legalacsonyabb értékként kezeli a rendszer.  
  
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

A következő lekérdezés lekéri a `id`családot az elemek létrehozási dátumának megfelelően. Az `creationDate` elem az a szám, amely az *időpontot*jelöli, vagy az eltelt idő a Jan. 1, 1970 másodpercben.

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

Emellett több tulajdonság is megrendelhető. A több tulajdonság által megrendelt lekérdezéseknek [összetett indexre](index-policy.md#composite-indexes)van szükségük. Tekintse meg a következő lekérdezést:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Ez a lekérdezés a város nevének `id` növekvő sorrendjében kérdezi le a családot. Ha több elem neve megegyezik a város nevével, a lekérdezés sorrendjét csökkenő `creationDate` sorrendbe kell rendezni.

## <a name="documents-with-missing-fields"></a>Hiányzó mezőket tartalmazó dokumentumok

Azok a `ORDER BY` lekérdezések, amelyek az alapértelmezett indexelési házirenddel rendelkező tárolók ellen futnak, nem adnak vissza olyan dokumentumokat, amelyekben a rendezés tulajdonság nincs meghatározva. Ha olyan dokumentumokat szeretne szerepeltetni, amelyekben a rendezési tulajdonság nincs meghatározva, explicit módon fel kell vennie ezt a tulajdonságot az indexelési házirendbe.

Például itt található egy olyan indexelési szabályzattal rendelkező tároló, amely nem tartalmaz explicit módon olyan elérési `"/*"`utakat, mint a következő:

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

Ha olyan lekérdezést futtat, amely tartalmazza `lastName` a `Order By` záradékot, akkor az eredmények csak azokat a `lastName` dokumentumokat tartalmazzák, amelyeken a tulajdonság definiálva van. Nem határoztak meg explicit módon megadott elérési `lastName` utat, hogy a- `lastName` t nem tartalmazó dokumentumok ne jelenjenek meg a lekérdezés eredményei között.

Az alábbi két dokumentum alapján rendezi `lastName` a lekérdezéseket, amelyek közül az egyiknek nincs `lastName` definiálva:

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

Ha a tároló indexelési házirendjét úgy frissítjük `lastName`, hogy explicit módon tartalmazzon egy elérési utat, a lekérdezés eredményei között a nem definiált rendezési tulajdonsággal rendelkező dokumentumok is szerepelni fognak. Explicit módon meg kell határozni az elérési utat a skaláris értékhez (és nem a fentiekhez képest). Az indexelési házirendben az `?` elérésiút-definícióban szereplő karaktert kell használnia, így biztosítva, hogy explicit `lastName` módon indexelje a tulajdonságot, és ne legyenek további beágyazott útvonalak. Ha a `Order By` lekérdezés [összetett indexet](index-policy.md#composite-indexes)használ, az eredmények mindig tartalmazzák a lekérdezés eredményeiben a nem definiált rendezési tulajdonsággal rendelkező dokumentumokat.

Itt látható egy minta indexelési házirend, amely lehetővé teszi, hogy a nem definiált `lastName` dokumentumok megjelenjenek a lekérdezés eredményei között:

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

Ha ugyanezt a lekérdezést is futtatja, akkor a hiányzó `lastName` dokumentumok először a lekérdezés eredményeiben jelennek meg:

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

Ha módosítja a rendezési sorrendet `DESC`, a hiányzó `lastName` dokumentumok a lekérdezés eredményei között utolsóként jelennek meg:

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
- [ELTOLÁSi korlát záradéka](sql-query-offset-limit.md)
