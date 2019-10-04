---
title: Az Azure Cosmos DB ORDER BY záradék
description: Az Azure Cosmos DB ismerje meg az SQL ORDER BY záradékban. Használ SQL-t egy Azure Cosmos DB JSON lekérdezési nyelvvel.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342809"
---
# <a name="order-by-clause"></a>ORDER BY záradék

A választható ORDER BY záradék megadja a rendezési sorrendjét a lekérdezés által visszaadott eredményeket.

## <a name="syntax"></a>Szintaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentumok
  
- `<sort_specification>`  
  
   Megadja egy tulajdonságot vagy a lekérdezés eredményhalmazában rendezéshez használandó kifejezés. A rendezési oszlop neve vagy tulajdonság aliasként adható meg.  
  
   Több tulajdonságok adhatók meg. A tulajdonság nevének egyedinek kell lennie. A feladatütemezés az ORDER BY záradékban rendezési tulajdonság határozza meg, hogy a szervezet az rendezett eredményhalmaz. Azt jelenti az eredményhalmaz az első tulajdonság szerint van rendezve, és ezután a rendezett lista van rendezve, a második tulajdonságot, és így tovább.  
  
   Az ORDER BY záradékban hivatkozott tulajdonságneveket vagy egy tulajdonság a kiválasztási listán, vagy bármely kétértelműséget nélkül a FROM záradékban megadott gyűjtemény meghatározott tulajdonságot meg kell felelnie.  
  
- `<sort_expression>`  
  
   Itt adhatja meg, egy vagy több tulajdonságait, vagy a lekérdezés eredményhalmazában rendezéshez használandó kifejezés.  
  
- `<scalar_expression>`  
  
   Tekintse meg a [skaláris kifejezések](sql-query-scalar-expressions.md) című szakasz részletezi.  
  
- `ASC | DESC`  
  
   Itt adhatja meg, hogy a megadott oszlopban szereplő értékek növekvő vagy csökkenő sorrendben jelennek meg. ASC rendezi a legkisebb érték a legmagasabb érték. DESC rendezi a legmagasabb érték a legkisebb értéket. ASC az alapértelmezett rendezési sorrend. A legkisebb lehetséges értékek NULL értéket kell kezelni.  
  
## <a name="remarks"></a>Megjegyzések  
  
   Az ORDER BY záradék szükséges, hogy az indexelési házirendet rendezi a mezőket egy indexet. Az Azure Cosmos DB lekérdezési modul támogatja a rendezési tulajdonság nevét és nem számított tulajdonságokat ellen. Az Azure Cosmos DB támogatja a több ORDER BY tulajdonságot. Annak érdekében, hogy a lekérdezések futtatása több ORDER BY tulajdonsággal rendelkező, meg kell határozni egy [összetett index](index-policy.md#composite-indexes) rendezi a mező alapján.

## <a name="examples"></a>Példák

Ha például a következő családok növekvő sorrendben a tartózkodási város neve, amely:

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

Az alábbi lekérdezés lekéri a család `id`s, az elem létrehozásának dátuma sorrendben. Elem `creationDate` van egy számot jelölő a *alapidőpont*, vagy 1970. január 1. másodpercek alatt óta eltelt idő.

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

Emellett a több tulajdonságot is rendezés. A lekérdezéshez, amely több tulajdonság alapján rendezi egy [összetett index](index-policy.md#composite-indexes). Vegye figyelembe a következő lekérdezést:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Ez a lekérdezés lekéri a család `id` növekvő sorrendben az városa nevétől. Ha több elem az városa névvel rendelkezik, a lekérdezés rendezés lesz a `creationDate` csökkenő sorrendben.

## <a name="next-steps"></a>További lépések

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [ELTOLÁS korlát záradék](sql-query-offset-limit.md)
