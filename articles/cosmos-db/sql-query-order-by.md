---
title: ORDER BY záradék Azure Cosmos DB
description: Az SQL ORDER BY záradék megismerése Azure Cosmos DB. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: fc5c875f4ae54ed334318efc5a1d5610b89bdda5
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75929579"
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
  
   Megadja azt a tulajdonságot vagy kifejezést, amelyen a lekérdezési eredményhalmaz rendezése történik. A rendezési oszlop nevet vagy tulajdonság-aliasként is megadható.  
  
   Több tulajdonság is megadható. A tulajdonságok nevének egyedinek kell lennie. Az ORDER BY záradékban szereplő rendezési tulajdonságok sorrendje határozza meg a rendezett eredményhalmaz szervezetét. Ez azt jelenti, hogy az eredményhalmaz az első tulajdonság szerint van rendezve, és a rendezett listát a második tulajdonság rendezi, és így tovább.  
  
   Az ORDER BY záradékban hivatkozott tulajdonságok neveinek meg kell egyezniük a kiválasztási listán szereplő tulajdonsággal vagy a FROM záradékban meghatározott tulajdonsággal, kétértelműség nélkül.  
  
- `<sort_expression>`  
  
   Egy vagy több olyan tulajdonságot vagy kifejezést ad meg, amelyeken a lekérdezési eredményhalmaz sorba rendezésére kerül sor.  
  
- `<scalar_expression>`  
  
   A részletekért tekintse meg a [skaláris kifejezések](sql-query-scalar-expressions.md) szakaszt.  
  
- `ASC | DESC`  
  
   Megadja, hogy a megadott oszlopban szereplő értékeket növekvő vagy csökkenő sorrendbe kell rendezni. Az ASC a legalacsonyabb értékről a legmagasabb értékre rendez. A DESC a legmagasabb értékről a legalacsonyabb értékre rendez. Az ASC az alapértelmezett rendezési sorrend. A null értékeket a lehető legalacsonyabb értékként kezeli a rendszer.  
  
## <a name="remarks"></a>Megjegyzések  
  
   Az ORDER BY záradék megköveteli, hogy az indexelési házirend tartalmazzon egy indexet a rendezés alatt álló mezőkhöz. A Azure Cosmos DB lekérdezési futtatókörnyezet támogatja a tulajdonságok neve szerinti rendezést, és nem a számított tulajdonságokkal. Azure Cosmos DB több SORRENDet is támogat a tulajdonságok alapján. Ha több SORRENDet tartalmazó lekérdezést szeretne futtatni, definiálnia kell egy [összetett indexet](index-policy.md#composite-indexes) a rendezés alatt álló mezőkben.
   
> [!Note] 
> Ha előfordulhat, hogy a rendezés alatt álló tulajdonságok egyes dokumentumok esetében nem definiáltak, és lekérdezési sorrendben szeretné lekérni őket, explicit módon létre kell hoznia egy indexet ezekhez a tulajdonságokhoz. Az alapértelmezett indexelési házirend nem engedélyezi a dokumentumok lekérését, ha a rendezési tulajdonság nincs definiálva.

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

A következő lekérdezés lekéri a család `id`s-t az elemek létrehozási dátumának megfelelően. Az Item `creationDate` egy szám, amely az *időpontot*jelöli, vagy az eltelt időt a Jan. 1, 1970 másodpercben.

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

A lekérdezés a város nevének növekvő sorrendjében kérdezi le a család `id`ét. Ha több elem is ugyanaz a város neve, a lekérdezés sorrendjét a `creationDate` csökkenő sorrendbe kell rendezni.

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [ELTOLÁSi korlát záradéka](sql-query-offset-limit.md)
