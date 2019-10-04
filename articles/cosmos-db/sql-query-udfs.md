---
title: Felhasználó által definiált függvények (UDF) Azure Cosmos DB
description: A Azure Cosmos DB felhasználó által definiált függvények megismerése.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614332"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Felhasználó által definiált függvények (UDF) Azure Cosmos DB

Az SQL API támogatást nyújt a felhasználó által definiált függvények (UDF) számára. A skaláris UDF használatával nulla vagy több argumentumot adhat meg, és egyetlen argumentumot ad vissza eredményként. Az API ellenőrzi, hogy az egyes argumentumok jogi JSON-értékekként vannak-e megadva.  

Az API kiterjeszti az SQL szintaxisát, hogy támogassa az egyéni alkalmazás-logikát a UDF használatával. A UDF az SQL API-val regisztrálhatja, és SQL-lekérdezésekben hivatkozhat rájuk. Sőt az UDF-EK exquisitely tervezték, hogy a lekérdezések, hívja meg. A UDF nem fér hozzá a környezeti objektumhoz, például a más JavaScript-típusokhoz, például a tárolt eljárásokhoz és triggerekhez. A lekérdezések csak olvashatók, és az elsődleges vagy másodlagos replikán is futtathatók. A más JavaScript-típusokkal ellentétben a UDF a másodlagos replikák futtatására szolgálnak.

Az alábbi példa egy UDF-tárolót regisztrál a Cosmos adatbázisban. A példa létrehoz egy UDF-t, `REGEX_MATCH`amelynek a neve:. Két JSON-karakterlánc-értéket `input` `pattern`fogad el, és ellenőrzi, hogy az első a JavaScript `string.match()` függvény használatával a másodikban megadott mintázattal egyezik-e.

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

Most használja ezt az UDF-t egy lekérdezési leképezésben. A UDF a kis-és nagybetűket megkülönböztető `udf.` előtaggal kell rendelkeznie, ha lekérdezéseken belülről hívja őket.

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

A szűrőn belüli `udf.` előtaggal minősített UDF-t használhatja az alábbi példában látható módon:

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

Lényegében a UDF érvényes skaláris kifejezések, amelyeket kivetítések és szűrők is használhatnak.

Ha ki szeretné bővíteni a UDF erejét, tekintse meg a feltételes logikát bemutató példát:

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

Az alábbi példa az UDF-t alkalmazza:

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

Ha az UDF-paraméterek által hivatkozott tulajdonságok nem érhetők el a JSON-értékben, a paraméter nem definiált minősül, és az UDF-hívás kimarad. Hasonlóképpen, ha az UDF eredménye nincs meghatározva, az eredmény nem tartalmazza azt.

Ahogy az előző példákban is látható, a UDF integrálja a JavaScript nyelv erejét az SQL API-val. A UDF a beépített JavaScript-futtatókörnyezeti funkciókkal összetett eljárási, feltételes logikát biztosító, sokoldalú, programozható felületet biztosít. Az SQL API megadja a UDF argumentumait az egyes forrásoldali elemeknél az aktuális WHERE vagy SELECT záradékban a feldolgozáshoz. Az eredmény zökkenőmentesen be van építve a teljes végrehajtási folyamatba. Összefoglalva, a UDF nagyszerű eszközei a lekérdezések részeként összetett üzleti logikának.

## <a name="next-steps"></a>További lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Rendszerfunkciók](sql-query-system-functions.md)
- [Aggregátumok](sql-query-aggregates.md)