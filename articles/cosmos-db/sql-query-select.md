---
title: Az Azure Cosmos DB SELECT záradék
description: További információk SQL SELECT záradékban az Azure Cosmos DB-hez. Használ SQL-t egy Azure Cosmos DB JSON lekérdezési nyelvvel.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 84d0212f7f212b4554b506726e027fe51f795eea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342695"
---
# <a name="select-clause"></a>SELECT záradék

Minden egyes lekérdezés tartalmaz egy SELECT záradékban, és a választható [FROM](sql-query-from.md) és [ahol](sql-query-where.md) záradékok ANSI SQL előírások szerint. Általában a forrás a FROM záradékban számbavétele megtörtént, és a WHERE záradékban szűrőt alkalmaz az adatforrás, JSON-elemek részhalmazának beolvasásához. A SELECT záradékban, majd a kért JSON-értékeket a kiválasztási listán projektek.

## <a name="syntax"></a>Szintaxis

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Argumentumok
  
- `<select_specification>`  

  Tulajdonságok vagy ki kell választania a az eredményhalmaz érték.  
  
- `'*'`  

  Itt adhatja meg, hogy az érték legyen beolvasva módosítása nélkül. Kifejezetten a feldolgozott értéke egy objektumot, ha a rendszer lekéri az összes tulajdonság.  
  
- `<object_property_list>`  
  
  Meghatározza a lekérdezni kívánt tulajdonságok listája. Minden egyes visszaküldött érték a megadott tulajdonságokkal rendelkező objektum lesz.  
  
- `VALUE`  

  Itt adhatja meg, hogy a JSON értéke legyen beolvasva a teljes JSON-objektum helyett. Ezzel ellentétben `<property_list>` nem wrap funkciót az előre jelzett érték egy objektumot.  
 
- `DISTINCT`
  
  Megadja, hogy ismétlődések tervezett tulajdonságai el kell távolítani.  

- `<scalar_expression>`  

  A kifejezés a következő időpontban számítja értéket jelölő. Lásd: [skaláris kifejezések](sql-query-scalar-expressions.md) című szakasz részletezi.  

## <a name="remarks"></a>Megjegyzések

A `SELECT *` szintaxis csak akkor érvényes, ha a FROM záradékban van deklarálva pontosan egy aliast. `SELECT *` Itt egy identitás leképezése, amely lehet hasznos, ha nincs leképezés van szükség. Válassza ki * csak akkor érvényes, ha a FROM záradék van megadva, és csak egyetlen bemeneti forrás bevezetni.  
  
Mindkét `SELECT <select_list>` és `SELECT *` "szintaktikai cukor", és azt is megteheti egyszerű SELECT utasítás használatával, ahogy az alábbi jelöl.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   a következő azonos:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   a következő azonos:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Példák

VÁLASSZA a következő lekérdezés például értéket ad vissza `address` a `Families` amelynek `id` megegyezik `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Határolójeles tulajdonság hozzáférő
Tulajdonságok a határolójeles tulajdonság Operator használatával is elérheti. Ha például `SELECT c.grade` és `SELECT c["grade"]` egyenértékűek. Ez a szintaxis hasznos egy szóközt, speciális karaktereket tartalmaz, vagy a neve megegyezik egy SQL kulcsszó vagy fenntartott szó rendelkező tulajdonság karaktert.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Beágyazott tulajdonságok

Az alábbi példa két beágyazott tulajdonságok, projektek `f.address.state` és `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>JSON-kifejezések

Leképezés a JSON-kifejezések, is támogatja, az alábbi példában látható módon:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Az előző példában a SELECT záradékban kell hozzon létre egy JSON-objektumot, és a mintául szolgáló kulcs nélkül biztosít, mivel a záradékot használ-e az implicit argumentum változó neve `$1`. A következő lekérdezés visszaadja az implicit argumentum két változót: `$1` és `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>További lépések

- [Bevezetés](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [WHERE záradék](sql-query-where.md)