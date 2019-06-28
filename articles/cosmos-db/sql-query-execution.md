---
title: Az Azure Cosmos DB SQL-lekérdezésének végrehajtása
description: További tudnivalók az Azure Cosmos DB SQL-lekérdezésének végrehajtása
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342583"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Az Azure Cosmos DB SQL-lekérdezés végrehajtása

Bármilyen, HTTP/HTTPS-kérelem indítására képes nyelv meghívhat a Cosmos DB – REST API-hoz. A cosmos DB is biztosít programozási könyvtárakat, .NET, Node.js, JavaScript és Python programozási nyelveket. A REST API és tárak összes támogatják a lekérdezése SQL használatával, és a .NET SDK-t is támogatja [LINQ lekérdezés](sql-query-linq-to-sql.md).

Az alábbi példák bemutatják, hogyan hozzon létre egy lekérdezést, és küldje el azt egy Cosmos DB-adatbázisfiók ellen.

## <a id="REST-API"></a>REST API-VAL

A cosmos DB egy megnyitott RESTful programozási modellt kínál a HTTP-n keresztül. Az erőforrás-modellje több erőforrást, adatbázis-fiókja alatt áll. amely egy Azure-előfizetés rendelkezések. Az adatbázis-fiókot egy készlete áll *adatbázisok*, is tartalmazhat, amelyek mindegyike több *tárolók*, amelyek viszont tartalmaznak *elemek*, UDF-EK és más erőforrástípusok. Mindegyik Cosmos DB erőforrás címmel rendelkező logikai és stabil URI segítségével. Erőforráscsoport neve egy *hírcsatorna*. 

Az alapszintű interakció ezekkel az erőforrásokkal modellje a HTTP-műveletek keresztül `GET`, `PUT`, `POST`, és `DELETE`, az a szabványos értelmezéseit. Használat `POST` hozzon létre egy új erőforrást, egy tárolt eljárás végrehajtása, vagy kiállítani egy Cosmos DB lekérdezéssel. Lekérdezések mindig csak olvasható műveletekhez, nincs mellékhatásokkal.

Az alábbi példák mutatják a `POST` egy SQL API-lekérdezés, a minta elemek esetén. A lekérdezés tartalmaz egy egyszerű szűrő a JSON `name` tulajdonság. A `x-ms-documentdb-isquery` és a Content-Type: `application/query+json` fejlécek jelölésére, hogy-e a művelet egy lekérdezést. Cserélje le `mysqlapicosmosdb.documents.azure.com:443` URI-a Cosmos DB-fiókjához.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Az eredmények a következők:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

A következő, összetettebb lekérdezés több eredmények illesztés adja vissza:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Az eredmények a következők: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

A lekérdezési eredmények nem fér el egyetlen lapon, ha a REST API-t adja vissza egy folytatási tokent keresztül a `x-ms-continuation-token` válaszfejléc. Ügyfelek eredmények oldalakra bontása fel a fejléc a következő eredményeket is. Eredmények száma oldalanként keresztül számát is szabályozhatja a `x-ms-max-item-count` szám fejléc.

Ha a lekérdezés száma például egy összesítő függvényt tartalmaz, a lekérdezés lap csak egy oldalnyi találatot részlegesen összesített értéket adhat vissza. Az ügyfelek végre kell hajtania egy második szintű összesítés keresztül ezekkel az eredményekkel el a végső eredményt. Például összeg fölé a számát adja vissza az egyes lapok teljes száma.

Lekérdezések az adatok konzisztencia-szabályzat kezeléséhez használja a `x-ms-consistency-level` hasonlóan minden REST API-kérelem fejléce. Munkamenet-konzisztencia is szükséges a legújabb echo `x-ms-session-token` a lekérdezési kérelem cookie-fejlécet. A lekérdezett tároló indexelési házirendet is befolyásolhatja a lekérdezési eredmények konzisztenciáját. Az alapértelmezett indexelési házirend-beállítások tárolókhoz, az index mindig aktuális elem tartalmát, és lekérdezési eredmények megfelelnek a kiválasztott adatok konzisztencia. További információkért lásd az [Azure Cosmos DB konzisztenciaszintjeinek] [konzisztenciaszintek].

Ha a beállított indexelési házirendet a tárolón a megadott lekérdezés nem támogatja, az Azure Cosmos DB kiszolgáló 400 "Hibás kérés" adja vissza. Ez a hibaüzenet a lekérdezésekhez az indexelő kifejezetten kizárva elérési úttal adja vissza. Megadhatja a `x-ms-documentdb-query-enable-scan` fejlécet, hogy a lekérdezést, hogy vizsgálatot végezzen, ha az index nem érhető el.

Megjelenik a részletes mérőszámokat a lekérdezés-végrehajtás beállításával a `x-ms-documentdb-populatequerymetrics` fejlécet `true`. További információkért lásd: [az Azure Cosmos DB SQL-lekérdezés metrikák](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

A .NET SDK támogatja a LINQ- és SQL lekérdezése. Az alábbi példa bemutatja, hogyan hajthat végre a fenti szűrő lekérdezés a .NET használatával:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Az alábbi példa összehasonlítja a két tulajdonság hasonlítania az egyezés keresésekor minden elemen belül, és használja a névtelen leképezések.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

A következő példa bemutatja az illesztések, LINQ keresztül `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

A .NET-ügyfél automatikusan végighalad a lekérdezési eredményeket minden oldalát a `foreach` blokkolja, az előző példában látható módon. A lekérdezési beállítások rendszerben bevezetett a [REST API-val](#REST-API) szakaszban is rendelkezésre állnak a .NET SDK használatával a `FeedOptions` és `FeedResponse` osztályoknak a `CreateDocumentQuery` metódus. A lapok száma segítségével szabályozhatja a `MaxItemCount` beállítás.

Explicit módon is szabályozhatja, lapozási létrehozásával `IDocumentQueryable` használatával a `IQueryable` objektumot, majd olvassa el a `ResponseContinuationToken` értékeket, és átadja azokat a biztonsági másolatot `RequestContinuationToken` a `FeedOptions`. Beállíthat `EnableScanInQuery` vizsgálatok engedélyezéséhez, ha a lekérdezés a konfigurált indexelési szabályzat által nem támogatott. A particionált tárolók használhatja `PartitionKey` a lekérdezés futtatásához egy olyan partíciót, bár az Azure Cosmos DB automatikusan kinyerheti az Ez a lekérdezés szövege a. Használhat `EnableCrossPartitionQuery` több partíciót irányuló lekérdezések futtatása.

.NET minták további lekérdezésekkel, tekintse meg a [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet) a Githubon.

## <a id="JavaScript-server-side-API"></a>JavaScript server-side API

Az Azure Cosmos DB programozási modellt kínál [végrehajtása JavaScript-alapú alkalmazás](stored-procedures-triggers-udfs.md) logikai tárolók, közvetlenül a tárolt eljárásokkal és eseményindítókkal. A JavaScript-logika regisztrálva a tároló szintjén majd adhat ki az adatbázis-műveleteket a megadott tárolóhoz, a környezeti ACID-tranzakciókat csomagolni elemeket.

Az alábbi példa bemutatja, hogyan használható `queryDocuments` az API-t a lekérdezéseket a JavaScript-kiszolgálón belül tárolt eljárások és eseményindítók:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Az Azure Cosmos DB konzisztenciaszintjeinek](consistency-levels.md)
