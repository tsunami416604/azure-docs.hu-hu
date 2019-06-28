---
title: Felhasználó által definiált függvények (UDF-EK) az Azure Cosmos DB-ben
description: Ismerje meg a felhasználó által definiált függvények az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342935"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Felhasználó által definiált függvények (UDF-EK) az Azure Cosmos DB-ben

Az SQL API támogatja a felhasználó által definiált függvények (UDF). Skaláris UDF-EK, a nulla vagy több argumentumot adja át, és a egy egyetlen argumentumot eredményt adja vissza. Az API-t minden argumentum jogi JSON-értékeit is ellenőrzi.  

Az API-t kibővíti az SQL-szintaxis az UDF-EK használatával egyéni alkalmazáslogika támogatása. Felhasználói függvények regisztrálásához az SQL API-val, és hivatkozni tudjon rájuk az SQL-lekérdezéseket. Sőt az UDF-EK exquisitely tervezték, hogy a lekérdezések, hívja meg. Maradhassanak felhasználói függvények nem rendelkezik hozzáféréssel a context objektumot, például a JavaScript más típusú, például a tárolt eljárásokkal és eseményindítókkal. Lekérdezések csak olvasható, és futtathatja vagy elsődleges vagy másodlagos replikákon. Futtassa a másodlagos replikákon UDF-EK, ellentétben más JavaScript típusú lettek kialakítva.

Az alábbi példa egy UDF-elem tárolóban regisztrálja a Cosmos DB-adatbázisban. A példa létrehoz egy UDF, amelynek a neve `REGEX_MATCH`. Két JSON karakterlánc értéket fogad el `input` és `pattern`, és ellenőrzi, hogy az első megegyezik a második megadott a minta használata JavaScript `string.match()` függvény.

## <a name="examples"></a>Példák

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

Most már használja egy query projection az UDF. A kis-és nagybetűket előtaggal kell minősíti UDF-EK `udf.` azokat belül lekérdezések hívásakor.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Az eredmények a következők:

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

Az UDF együtt használhatja a `udf.` előtag belül egy szűrő, az alábbi példában látható módon:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Az eredmények a következők:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Lényegében az UDF-EK érvényes skaláris kifejezés, amelyet használhat, a leképezések és a szűrők.

Bontsa ki az UDF-EK hatékonyságát, feltételes logikát az erre egy másik példát:

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

Az alábbi példa az UDF-ben él:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Az eredmények a következők:

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

Ha a tulajdonságok szerinti által az UDF paraméterek nem érhetők el a JSON-értékben, a paraméter minősül nincs definiálva, és a rendszer kihagyta az UDF-hívás. Ehhez hasonlóan az UDF eredménye nem definiált, ha azt nem szerepel az eredményben.

Ahogy az előző példák azt mutatják, UDF-EK integrálása JavaScript nyelven hatékonyságát az SQL API. UDF-EK egy részletes programozható felületet összetett eljárásokról, feltételes logikát, beépített JavaScript futtatókörnyezet képességek segítségével. Ehhez adja meg. Az SQL API-t biztosít az argumentumok az UDF-EK egyes forrás elem az aktuális HELYÉT, vagy a SELECT záradékban feldolgozási szintre. Az eredmény zökkenőmentesen a teljes végrehajtási folyamat részét képezik. Összefoglalva UDF-EK olyan összetett üzleti logikát ehhez a lekérdezések részeként nagyszerű eszközöket.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Rendszer-funkciók](sql-query-system-functions.md)
- [Összesítések](sql-query-aggregates.md)