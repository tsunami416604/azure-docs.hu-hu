---
title: SELECT záradék Azure Cosmos DB
description: További információ az Azure Cosmos DB SQL SELECT záradékáról. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: b90fc6f1f50ec2ea75619188cca36f78061f28df
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326791"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT záradék Azure Cosmos DB

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

  Az eredményhalmaz számára kijelölhető tulajdonságok vagy értékek.  
  
- `'*'`  

  Megadja, hogy az értéket a módosítások végrehajtása nélkül kell beolvasni. Különösen, ha a feldolgozott érték egy objektum, az összes tulajdonságot lekéri a rendszer.  
  
- `<object_property_list>`  
  
  Meghatározza a lekérdezni kívánt tulajdonságok listáját. Minden visszaadott érték egy objektum lesz, amely a megadott tulajdonságokkal rendelkezik.  
  
- `VALUE`  

  Megadja, hogy a JSON-értéket a teljes JSON-objektum helyett le kell olvasni. Ez a `<property_list>` értékkel ellentétben a kijelzett értéket nem zárja be egy objektumban.  
 
- `DISTINCT`
  
  Megadja, hogy el kell távolítani a kijelzett tulajdonságok másodpéldányait.  

- `<scalar_expression>`  

  A kiszámítani kívánt értéket jelölő kifejezés. Részletekért lásd a [skaláris kifejezések](sql-query-scalar-expressions.md) szakaszt.  

## <a name="remarks"></a>Megjegyzések

A `SELECT *` szintaxis csak akkor érvényes, ha a FROM záradékban pontosan egy alias van deklarálva. a `SELECT *` egy identitás-kivetítést biztosít, amely akkor lehet hasznos, ha nincs szükség leképezésre. A SELECT * utasítás csak akkor érvényes, ha a FROM záradék meg van adva, és csak egyetlen bemeneti forrást mutat be.  
  
Mind a `SELECT <select_list>`, mind a `SELECT *` a "szintaktikai cukor", és az alábbiakban látható egyszerű SELECT utasításokkal is megadható.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   egyenértékű a következővel:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   egyenértékű a következővel:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Példák

A következő SELECT lekérdezési példa a `address` értéket adja vissza `Families` közül, amelynek `id` értéke `AndersenFamily`:

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
A tulajdonságokat az idézett tulajdonság operátor [] használatával érheti el. Például a `SELECT c.grade` és a `SELECT c["grade"]` egyenértékű. Ez a szintaxis akkor hasznos, ha olyan tulajdonságot szeretne elmenekülni, amely szóközt, speciális karaktereket vagy egy SQL-kulcsszó vagy fenntartott szó nevét tartalmazza.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Beágyazott tulajdonságok

Az alábbi példa két beágyazott tulajdonságot, `f.address.state` és `f.address.city`.

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

Az előző példában a SELECT záradéknak létre kell hoznia egy JSON-objektumot, és mivel a minta nem tartalmaz kulcsot, a záradék az implicit argumentum változó nevét használja `$1`. A következő lekérdezés két implicit argumentum változót ad vissza: `$1` és `$2`.

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

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE záradék](sql-query-where.md)