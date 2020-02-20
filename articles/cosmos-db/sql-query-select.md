---
title: SELECT záradék Azure Cosmos DB
description: További információ az Azure Cosmos DB SQL SELECT záradékáról. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
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

  Tulajdonságok vagy ki kell választania a az eredményhalmaz érték.  
  
- `'*'`  

  Itt adhatja meg, hogy az érték legyen beolvasva módosítása nélkül. Kifejezetten a feldolgozott értéke egy objektumot, ha a rendszer lekéri az összes tulajdonság.  
  
- `<object_property_list>`  
  
  Meghatározza a lekérdezni kívánt tulajdonságok listája. Minden egyes visszaküldött érték a megadott tulajdonságokkal rendelkező objektum lesz.  
  
- `VALUE`  

  Itt adhatja meg, hogy a JSON értéke legyen beolvasva a teljes JSON-objektum helyett. Ez a `<property_list>`tól eltérően nem takarja el a tervezett értéket egy objektumban.  
 
- `DISTINCT`
  
  Megadja, hogy el kell távolítani a kijelzett tulajdonságok másodpéldányait.  

- `<scalar_expression>`  

  A kifejezés a következő időpontban számítja értéket jelölő. Részletekért lásd a [skaláris kifejezések](sql-query-scalar-expressions.md) szakaszt.  

## <a name="remarks"></a>Megjegyzések

Az `SELECT *` szintaxis csak akkor érvényes, ha a FROM záradékban pontosan egy alias van deklarálva. a `SELECT *` identitás-kivetítést biztosít, ami hasznos lehet, ha nincs szükség leképezésre. Válassza ki * csak akkor érvényes, ha a FROM záradék van megadva, és csak egyetlen bemeneti forrás bevezetni.  
  
Mind a `SELECT <select_list>`, mind a `SELECT *` "szintaktikai cukor", és az alábbiakban látható egyszerű SELECT utasításokkal is megadható.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   a következő azonos:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   a következő azonos:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Példák

A következő SELECT lekérdezési példa olyan `Families` `address` adja vissza, amelynek `id` `AndersenFamily`:

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
A tulajdonságokat az idézett tulajdonság operátor [] használatával érheti el. A `SELECT c.grade` és a `SELECT c["grade"]` például egyenértékűek. Ez a szintaxis akkor hasznos, ha olyan tulajdonságot szeretne elmenekülni, amely szóközt, speciális karaktereket vagy egy SQL-kulcsszó vagy fenntartott szó nevét tartalmazza.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Beágyazott tulajdonságok

A következő példában két beágyazott tulajdonságot (`f.address.state` és `f.address.city`) tervezünk.

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
## <a name="reserved-keywords-and-special-characters"></a>Fenntartott kulcsszavak és speciális karakterek

Ha az adatai olyan tulajdonságokat tartalmaznak, amelyek neve megegyezik a fenntartott kulcsszavakkal (például "Order" vagy "Group"), akkor a dokumentumok lekérdezése szintaktikai hibákat eredményez. A lekérdezés sikeres futtatásához explicit módon fel kell vennie a tulajdonságot `[]` karakterbe.

Például itt található egy `order` nevű és egy speciális karaktereket tartalmazó `price($)` tulajdonságot tartalmazó dokumentum:

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

Ha olyan lekérdezéseket futtat, amelyek tartalmazzák a `order` tulajdonságot vagy `price($)` tulajdonságot, szintaktikai hibaüzenetet fog kapni.

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

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE záradék](sql-query-where.md)
