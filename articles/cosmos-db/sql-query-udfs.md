---
title: Felhasználó által definiált függvények (UDF-ek) az Azure Cosmos DB-ben
description: Ismerje meg a felhasználó által definiált függvények az Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011123"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Felhasználó által definiált függvények (UDF-ek) az Azure Cosmos DB-ben

Az SQL API támogatja a felhasználó által definiált függvényeket (UDF). A skaláris UDF-ek segítségével nulla vagy több argumentumot adhat át, és egyetlen argumentum eredményt adhat vissza. Az API ellenőrzi az egyes argumentumok, hogy a jogi JSON-értékek.  

## <a name="udf-use-cases"></a>UDF-használati esetek

Az API kiterjeszti az SQL szintaxist az UDF-ek használatával az egyéni alkalmazáslogika támogatásához. Az UDF-eket regisztrálhatja az SQL API-val, és hivatkozhat rájuk az SQL-lekérdezésekben. A tárolt eljárásokkal és eseményindítókkal ellentétben az UDF-ek írásvédettek.

UDF-ek használatával kiterjesztheti az Azure Cosmos DB lekérdezési nyelvét. UdFs egy nagyszerű módja annak, hogy kifejezzék összetett üzleti logika a lekérdezés kivetítése.

Azonban azt javasoljuk, elkerülve UDFs, ha:

- Az Azure Cosmos DB-ben már létezik egy egyenértékű [rendszerfüggvény.](sql-query-system-functions.md) A rendszerfüggvények mindig kevesebb RU-t használnak, mint az egyenértékű UDF.System functions will always use less RU's than the equivalent UDF.
- Az UDF az egyetlen `WHERE` szűrő a lekérdezés záradékában. UdF nem használja az indexet, így az UDF kiértékelése dokumentumok betöltését igényli. Az indexet udf-fel kombinálva az indexet használó további `WHERE` szűrőpredikátumok kombinálásával a záradék ban csökken az UDF által feldolgozott dokumentumok száma.

Ha egy lekérdezésben többször is ugyanazt az UDF-et kell használni, akkor az UDF-re kell hivatkozni egy [segédlekérdezésben,](sql-query-subquery.md#evaluate-once-and-reference-many-times)amely lehetővé teszi, hogy join kifejezéssel értékeljük ki egyszer az UDF-et, de többször is hivatkozhatunk rá.

## <a name="examples"></a>Példák

A következő példa regisztrálja az UDF egy elemtároló a Cosmos-adatbázisban. A példa létrehoz egy UDF-et, amelynek neve `REGEX_MATCH`. Két JSON-karakterlánc-értéket `input` fogad `pattern`el, és ellenőrzi, hogy az első megegyezik-e `string.match()` a másodikban megadott mintával a JavaScript függvény használatával.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Most használja ezt az UDF-et egy lekérdezés-vetületben. A kis- és nagybetűket megkülönböztető `udf.` előtaggal rendelkező UDF-eket a lekérdezéseken belüli híváskor fel kell jogosítania.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Az eredmény a következő:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Használhatja az UDF minősített `udf.` az előtag egy szűrőn belül, mint a következő példában:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Az eredmény a következő:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Lényegében udf-ek érvényes skaláris kifejezések, amelyek et vetületekben és szűrőkben is használhat.

Az UDF-ek teljesítményének bővítéséhez tekintsen egy másik, feltételes logikával rendelkező példát:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

A következő példa az UDF-et gyakorolja:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Az eredmény a következő:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Ha az UDF-paraméterek által említett tulajdonságok nem érhetők el a JSON-értékben, a paraméter nem definiáltnak minősül, és az UDF-meghívás kimarad. Hasonlóképpen, ha az UDF eredménye nincs definiálva, az nem szerepel az eredményben.

Amint azt az előző példák mutatják, udf-ek integrálja a JavaScript-nyelv erejét az SQL API-val. UdFs nyújt gazdag programozható felület et összetett eljárási, feltételes logika segítségével a beépített JavaScript futásidejű képességek. Az SQL API az adott forráselem-hoz az adott FORRÁSelem argumentumait adja meg az aktuális WHERE vagy SELECT záradék fázisban. Az eredmény zökkenőmentesen beépül a teljes végrehajtási folyamatba. Összefoglalva, UDFs nagyszerű eszközök összetett üzleti logika részeként lekérdezések.

## <a name="next-steps"></a>További lépések

- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
- [Rendszerfüggvények](sql-query-system-functions.md)
- [Összesítések](sql-query-aggregates.md)