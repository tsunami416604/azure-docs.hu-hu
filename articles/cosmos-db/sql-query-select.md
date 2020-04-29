---
title: SELECT záradék Azure Cosmos DB
description: További információ az Azure Cosmos DB SQL SELECT záradékáról. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77469935"
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

  Megadja, hogy a JSON-értéket a teljes JSON-objektum helyett le kell olvasni. Ezzel ellentétben `<property_list>` a nem zárja be a tervezett értéket egy objektumban.  
 
- `DISTINCT`
  
  Megadja, hogy el kell távolítani a kijelzett tulajdonságok másodpéldányait.  

- `<scalar_expression>`  

  A kiszámítani kívánt értéket jelölő kifejezés. Részletekért lásd a [skaláris kifejezések](sql-query-scalar-expressions.md) szakaszt.  

## <a name="remarks"></a>Megjegyzések

A `SELECT *` szintaxis csak akkor érvényes, ha a from záradék pontosan egy aliast deklarált. `SELECT *`identitás-kivetítést biztosít, amely akkor lehet hasznos, ha nincs szükség leképezésre. A SELECT * utasítás csak akkor érvényes, ha a FROM záradék meg van adva, és csak egyetlen bemeneti forrást mutat be.  
  
`SELECT *` Mindkettő `SELECT <select_list>` "szintaktikai cukor", és az alábbiakban látható egyszerű Select utasításokkal is megadható.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   egyenértékű a következővel:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   egyenértékű a következővel:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Példák

A következő SELECT lekérdezési példa `address` azt `Families` adja `id` vissza `AndersenFamily`, hogy mely egyezések közül választhat:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmény a következő:

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
A tulajdonságokat az idézett tulajdonság operátor [] használatával érheti el. Például a `SELECT c.grade` és `SELECT c["grade"]` a egyenértékű. Ez a szintaxis akkor hasznos, ha olyan tulajdonságot szeretne elmenekülni, amely szóközt, speciális karaktereket vagy egy SQL-kulcsszó vagy fenntartott szó nevét tartalmazza.

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

Az eredmény a következő:

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

Az eredmény a következő:

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

Az eredmény a következő:

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
## <a name="reserved-keywords-and-special-characters"></a>Fenntartott kulcsszavak és speciális karakterek

Ha az adatai olyan tulajdonságokat tartalmaznak, amelyek neve megegyezik a fenntartott kulcsszavakkal (például "Order" vagy "Group"), akkor a dokumentumok lekérdezése szintaktikai hibákat eredményez. A lekérdezés sikeres futtatásához explicit módon fel `[]` kell tüntetnie a tulajdonságot a karakterben.

Tegyük fel például, hogy egy olyan dokumentum található, `order` amely egy nevű `price($)` tulajdonságot és egy speciális karaktereket tartalmazó tulajdonságot tartalmaz:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Ha a `order` tulajdonságot vagy `price($)` tulajdonságot tartalmazó lekérdezéseket futtat, szintaktikai hiba jelenik meg.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
Az eredmény a következőket eredményezi:

`
Syntax error, incorrect syntax near 'order'
`

Az alábbihoz hasonló lekérdezéseket kell újraírnia:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>További lépések

- [Első lépések](sql-query-getting-started.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE záradék](sql-query-where.md)
