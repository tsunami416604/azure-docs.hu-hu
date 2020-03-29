---
title: SQL-lekérdezés végrehajtása az Azure Cosmos DB-ben
description: Ismerje meg, hogyan hozhat létre egy SQL-lekérdezést, és hajtsa végre az Azure Cosmos DB-ben. Ez a cikk azt ismerteti, hogyan hozhat létre és hajthat végre SQL-lekérdezést REST API, .Net SDK, JavaScript SDK és számos más SDK használatával.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871261"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Az Azure Cosmos DB SQL-lekérdezés végrehajtása

Bármely nyelv, amely képes HTTP/HTTPS-kérelmek et készíteni, meghívhatja a Cosmos DB REST API-t. A Cosmos DB programozási könyvtárakat is kínál .NET, Node.js, JavaScript és Python programozási nyelvekhez. A REST API és a könyvtárak mindegyike támogatja az SQL-en keresztültörténő lekérdezést, és a .NET SDK is támogatja a [LINQ lekérdezést.](sql-query-linq-to-sql.md)

Az alábbi példák bemutatják, hogyan hozhat létre egy lekérdezést, és küldje el egy Cosmos-adatbázis-fiók ellen.

## <a name="rest-api"></a><a id="REST-API"></a>REST API

Cosmos DB kínál nyílt RESTful programozási modell HTTP-n keresztül. Az erőforrásmodell egy adatbázis-fiók alatti erőforrások készletéből áll, amelyeket egy Azure-előfizetési rendelkezések tartalmaznak. Az adatbázisfiók *adatbázisok*készletéből áll, amelyek mindegyike több *tárolót*is tartalmazhat, amelyek viszont *elemeket,* UDF-eket és más erőforrástípusokat tartalmaznak. Minden Cosmos DB-erőforrás egy logikai és stabil URI használatával címezhető. Az erőforrások készletét *hírcsatornának nevezzük.* 

Az ezekkel az erőforrásokkal való alapvető `GET` `PUT`interakciós modell a HTTP-műveleteken keresztül , a `POST`, és `DELETE`a szokásos értelmezésekkel történik. Új `POST` erőforrás létrehozása, tárolt eljárás végrehajtása vagy Cosmos DB-lekérdezés kiadása. A lekérdezések mindig csak olvasható műveletek mellékhatások nélkül.

Az alábbi példák egy `POST` SQL API-lekérdezést mutatnak be a mintaelemekkel szemben. A lekérdezés egy egyszerű szűrővel `name` rendelkezik a JSON tulajdonságon. A `x-ms-documentdb-isquery` tartalomtípusa: `application/query+json` fejlécek azt jelölik, hogy a művelet lekérdezés. Cserélje `mysqlapicosmosdb.documents.azure.com:443` le a Cosmos DB-fiók URI-ját.

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

Az eredmény a következő:

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

A következő, összetettebb lekérdezés több eredményt ad vissza egy illesztésből:

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

Az eredmény a következő: 

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

Ha egy lekérdezés eredményei nem férnek el egy oldalon, a REST `x-ms-continuation-token` API egy folytatási jogkivonatot ad vissza a válaszfejlécen keresztül. Az ügyfelek lapozthatják az eredményeket, ha a fejlécet beleadják a következő eredményekbe. Az oldalankénti eredmények számát a `x-ms-max-item-count` számfejlécen keresztül is szabályozhatja.

Ha egy lekérdezés a COUNT aggregációs függvényt , például a DARAB függvényt, a lekérdezési lap csak egy oldalon keresztül adhat vissza részlegesen összesített értéket. Az ügyfeleknek a végső eredmények eléréséhez második szintű összesítést kell végrehajtaniuk ezeken az eredményeken. Összegezd például az egyes oldalakon visszaadott számokat a teljes szám visszaadásához.

A lekérdezések adatkonzisztenciaházirendjének kezeléséhez használja a fejlécet, `x-ms-consistency-level` mint az összes REST API-kérelemben. A munkamenet-konzisztencia a lekérdezési kérelemben a legújabb `x-ms-session-token` cookie-fejléc visszhangját is igényli. A lekérdezett tároló indexelési házirendje is befolyásolhatja a lekérdezési eredmények konzisztenciáját. A tárolók alapértelmezett indexelési házirend-beállításaival az index mindig aktuális az elem tartalmával, és a lekérdezési eredmények megegyeznek az adatokhoz kiválasztott konzisztenciával. További információ: [Azure Cosmos DB konzisztenciaszintek][konzisztenciaszintek].

Ha a tárolón konfigurált indexelési szabályzat nem támogatja a megadott lekérdezést, az Azure Cosmos DB-kiszolgáló 400 "Hibás kérés" értéket ad vissza. Ez a hibaüzenet olyan lekérdezéseknél ad vissza, amelyek az indexelésből kifejezetten kizárt elérési utakat tartalmazó kontitisszák. Megadhatja a `x-ms-documentdb-query-enable-scan` fejlécet, hogy a lekérdezés vizsgálatot hajtson végre, ha az index nem érhető el.

A lekérdezések végrehajtásával kapcsolatos részletes `x-ms-documentdb-populatequerymetrics` mutatókat `true`úgy kaphatja meg, hogy a fejlécet a-ra állítja. További információ: [SQL query metrikák az Azure Cosmos DB.](sql-api-query-metrics.md)

## <a name="c-net-sdk"></a>C# (.NET SDK)

A .NET SDK támogatja a LINQ és az SQL lekérdezést is. A következő példa bemutatja, hogyan hajthatja végre az előző szűrőlekérdezést a .NET segítségével:

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

A következő példa az egyes elemeken belüli egyenlőség két tulajdonságát hasonlítja össze, és névtelen előrejelzéseket használ.

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

A következő példa a LINQ-n `SelectMany`keresztül kifejezett illesztéseket mutatja be.

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

A .NET-ügyfél automatikusan végighalad a lekérdezési `foreach` eredmények összes lapján a blokkokban, ahogy az az előző példában is látható. A [REST API](#REST-API) szakaszban bevezetett lekérdezési beállítások a .NET SDK-ban is `CreateDocumentQuery` elérhetők a `FeedOptions` metódus és `FeedResponse` osztályok használatával. Az oldalak számát a `MaxItemCount` beállítás sal szabályozhatja.

A lapozást `IDocumentQueryable` az `IQueryable` objektum használatával történő létrehozással `ResponseContinuationToken` is szabályozhatja, majd az értékek elolvasásával és visszaadásával visszaadva a alkalmazásban. `RequestContinuationToken` `FeedOptions` Beállíthatja, `EnableScanInQuery` hogy engedélyezze a vizsgálatokat, ha a lekérdezést a konfigurált indexelési házirend nem támogatja. Particionált tárolók `PartitionKey` esetén a lekérdezés egyetlen partíción futtatható, bár az Azure Cosmos DB automatikusan kibonthatja ezt a lekérdezés szövegéből. Több `EnableCrossPartitionQuery` partíción is futtathat lekérdezéseket.

További .NET-minták lekérdezések, tekintse meg az [Azure Cosmos DB .NET minták](https://github.com/Azure/azure-cosmos-dotnet-v3) at GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript kiszolgálóoldali API

Az Azure Cosmos DB programozási modellt biztosít a [JavaScript-alapú alkalmazáslogika közvetlenül](stored-procedures-triggers-udfs.md) a tárolókon, tárolt eljárások és eseményindítók használatával végrehajtásához. A tároló szintjén regisztrált JavaScript-logika ezután adatbázis-műveleteket adhat ki az adott tároló elemein, környezeti ACID-tranzakciókba csomagolva.

A következő példa bemutatja, hogyan használható `queryDocuments` a JavaScript-kiszolgáló API-ban a tárolt eljárásokés eseményindítók belső lekérdezéseinek lebonyolításához:

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

- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Az Azure Cosmos DB konzisztenciaszintjei](consistency-levels.md)
