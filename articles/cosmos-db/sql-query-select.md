---
title: SELECT záradék Azure Cosmos DB
description: További információ az Azure Cosmos DB SQL SELECT záradékáról. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: d34b1c39d9789409dc365cd4cf07fdc3d5a780fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003512"
---
# <a name="select-clause"></a>SELECT záradék

Minden lekérdezés egy SELECT záradékból, valamint opcionális és [Where](sql-query-where.md) záradékokból áll, és az ANSI SQL-szabványok [alapján](sql-query-from.md) . A FROM záradékban lévő forrás jellemzően enumerálásra kerül, és a WHERE záradék egy szűrőt alkalmaz a forráson a JSON-elemek egy részhalmazának lekéréséhez. A SELECT záradék ezt követően a kért JSON-értékeket a kiválasztási listában adja meg.

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
  
  Megadja, hogy el kell távolítani a kijelzett tulajdonságok másodpéldányait.  

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

A következő Select lekérdezési példa `address` azt adja `id` vissza `AndersenFamily`, hogy mely egyezések közül `Families` választhat:

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

### <a name="quoted-property-accessor"></a>Idézett tulajdonság-hozzáférés
A tulajdonságokat az idézett tulajdonság operátor [] használatával érheti el. Ha például `SELECT c.grade` és `SELECT c["grade"]` egyenértékűek. Ez a szintaxis akkor hasznos, ha olyan tulajdonságot szeretne elmenekülni, amely szóközt, speciális karaktereket vagy egy SQL-kulcsszó vagy fenntartott szó nevét tartalmazza.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Beágyazott tulajdonságok

A következő példa két beágyazott tulajdonságot `f.address.state` és. `f.address.city`

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

A vetítés a JSON-kifejezéseket is támogatja, ahogy az az alábbi példában is látható:

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

Az előző példában a SELECT záradéknak létre kell hoznia egy JSON-objektumot, és mivel a minta nem tartalmaz kulcsot, a záradék az implicit argumentum változó nevét `$1`használja. A következő lekérdezés két implicit argumentum változót ad `$1` vissza `$2`: és.

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
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE záradék](sql-query-where.md)