---
title: SELECT záradék az Azure Cosmos DB-ben
description: Ismerje meg az Azure Cosmos DB SQL SELECT záradékát. Az SQL használata Azure Cosmos DB JSON lekérdezési nyelvként.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469935"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT záradék az Azure Cosmos DB-ben

Minden lekérdezés egy SELECT záradékból és választható [FROM](sql-query-from.md) és [WHERE](sql-query-where.md) záradékból áll, ANSI SQL szabványok szerint. Általában a FORRÁS záradékban szereplő forrás van számbavételre, és a WHERE záradék szűrőt alkalmaz a forrásra a JSON-elemek egy részhalmazának beolvasásához. A SELECT záradék ezután kivetíti a kért JSON-értékeket a kijelölési listában.

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

  Az eredményhalmazhoz kijelölendő tulajdonságok vagy értékek.  
  
- `'*'`  

  Itt adhatja meg, hogy az értéket módosítások nélkül kell beolvasni. Ha a feldolgozott érték objektum, akkor az összes tulajdonság lekérésre kerül.  
  
- `<object_property_list>`  
  
  Megadja a beolvasandó tulajdonságok listáját. Minden visszaadott érték egy megadott tulajdonságokkal rendelkező objektum lesz.  
  
- `VALUE`  

  Itt adható meg, hogy a JSON-értéket a teljes JSON-objektum helyett be kell olvasni. Ez, `<property_list>` ellentétben nem tördeli a vetített értéket egy objektumban.  
 
- `DISTINCT`
  
  Itt adható meg, hogy a kivetített tulajdonságok ismétlődéseit el kell távolítani.  

- `<scalar_expression>`  

  A kiszámítandó értéket jelölő kifejezés. A részleteket lásd a [Skaláris kifejezések](sql-query-scalar-expressions.md) című szakaszban.  

## <a name="remarks"></a>Megjegyzések

A `SELECT *` szintaxis csak akkor érvényes, ha a FROM záradék pontosan egy aliast deklarált. `SELECT *`identitás-kivetítést biztosít, amely akkor lehet hasznos, ha nincs szükség kivetítésre. A SELECT * csak akkor érvényes, ha a FROM záradék meg van adva, és csak egyetlen bemeneti forrást vezetett be.  
  
Mindkettő `SELECT <select_list>` `SELECT *` "szintaktikai cukor", és lehet kifejezni egyszerű SELECT utasítások, mint az alábbiakban látható.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   egyenértékű a következőkkal:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   egyenértékű a következőkkal:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Példák

A következő SELECT `address` lekérdezési példa akövetkező találatot adja vissza, amelynek `Families` `id` egyezései: `AndersenFamily`

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

### <a name="quoted-property-accessor"></a>Idézett tulajdonság-elérő
A tulajdonságokat a idézett tulajdonságoperátorral [] érheti el. Például, `SELECT c.grade` `SELECT c["grade"]` és egyenértékűek. Ez a szintaxis akkor hasznos, ha olyan tulajdonságot szeretne kikerülni, amely szóközöket, speciális karaktereket tartalmaz, vagy ugyanaz a neve, mint egy SQL-kulcsszónak vagy egy fenntartott szónak.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Beágyazott tulajdonságok

A következő példa két egymásba ágyazott tulajdonságot vetít ki, `f.address.state` és `f.address.city`.

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

A Vetítés támogatja a JSON-kifejezéseket is, amint az a következő példában látható:

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

Az előző példában a SELECT záradéknak létre kell hoznia egy JSON-objektumot, és `$1`mivel a minta nem tartalmaz kulcsot, a záradék az implicit argumentumváltozó nevét használja. A következő lekérdezés két implicit `$1` argumentumváltozót ad vissza: és `$2`.

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

Ha az adatok olyan tulajdonságokat tartalmaznak, amelyek neve megegyezik a fenntartott kulcsszavakkal , például a "sorrend" vagy a "Csoport" nevekkel, akkor a dokumentumokhoz tartozó lekérdezések szintaktikai hibákat eredményeznek. A lekérdezés sikeres futtatásához `[]` explicit módon fel kell tüntetni a tulajdonságot a karakterben.

Az alábbiakban például egy elnevezett `order` tulajdonságot `price($)` tartalmazó dokumentum, a tulajdonság pedig speciális karaktereket tartalmaz:

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

Ha olyan lekérdezéseket futtat, amelyek tartalmazzák a `order` tulajdonságot vagy `price($)` tulajdonságot, szintaktikai hiba lép fel.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
Az eredmény:

`
Syntax error, incorrect syntax near 'order'
`

Ugyanazokat a lekérdezéseket kell átírnia, mint az alábbiakban:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>További lépések

- [Kezdetekhez](sql-query-getting-started.md)
- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE záradék](sql-query-where.md)
