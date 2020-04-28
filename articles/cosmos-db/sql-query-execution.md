---
title: SQL-lekérdezés végrehajtása Azure Cosmos DB
description: Megtudhatja, hogyan hozhat létre SQL-lekérdezést, és hogyan hajthatja végre Azure Cosmos DBban. Ez a cikk azt ismerteti, hogyan hozhat létre és futtathat SQL-lekérdezéseket REST API, .net SDK, JavaScript SDK és más SDK-k használatával.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74871261"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>SQL-lekérdezés végrehajtásának Azure Cosmos DB

A HTTP/HTTPS-kérések létrehozására alkalmas bármely nyelv hívhatja a Cosmos DB REST API. A Cosmos DB a .NET, a Node. js, a JavaScript és a Python programozási nyelvekhez is biztosít programozási kódtárakat. A REST API és a kódtárak támogatják az SQL-en keresztüli lekérdezéseket, és a .NET SDK is támogatja a [LINQ-lekérdezéseket](sql-query-linq-to-sql.md).

Az alábbi példák bemutatják, hogyan hozhat létre egy lekérdezést, és hogyan küldhet el egy Cosmos-adatbázis-fiókkal.

## <a name="rest-api"></a><a id="REST-API"></a>REST API

A Cosmos DB egy nyitott REST-alapú programozási modellt kínál HTTP-n keresztül. Az erőforrás-modell egy, az Azure-előfizetéshez tartozó adatbázis-fiókhoz tartozó erőforrás-készletből áll. Az adatbázis-fiók *adatbázisokból*áll, amelyek mindegyike több *tárolót*is tartalmazhat, amelyek pedig *elemeket*, UDF és más erőforrásokat tartalmaznak. Minden Cosmos DB erőforrás egy logikai és egy stabil URI-vel van címezve. Az erőforrások készletét *hírcsatorna*-csoportnak nevezzük. 

Az ezekkel az erőforrásokkal rendelkező alapszintű interakciós modell `GET`a `PUT`http `POST`-műveletek `DELETE`, a, a és a standard értelmezések segítségével érhető el. Új `POST` erőforrás létrehozásához, tárolt eljárás végrehajtásához vagy Cosmos db-lekérdezés kikibocsátásához használható. A lekérdezések mindig írásvédett műveletek, amelyeknek nincs mellékhatása.

Az alábbi példák egy SQL `POST` API-lekérdezést mutatnak be a minta elemeknél. A lekérdezés egy egyszerű szűrőt tartalmaz a JSON `name` -tulajdonsághoz. A `x-ms-documentdb-isquery` és a Content-Type `application/query+json` : fejlécek azt jelzik, hogy a művelet lekérdezés. Cserélje `mysqlapicosmosdb.documents.azure.com:443` le a elemet a Cosmos db-fiókjához tartozó URI azonosítóra.

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

A következő, összetettebb lekérdezés több találatot ad vissza egy illesztésből:

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

Ha egy lekérdezés eredménye nem fér el egyetlen lapon, a REST API a `x-ms-continuation-token` válasz fejlécén keresztül visszaadja a folytatási tokent. Az ügyfelek az eredményeket a későbbi eredményekben szereplő fejléctel is megadhatják. Az eredmények számát az egyes lapokon a `x-ms-max-item-count` szám fejlécen keresztül is szabályozhatja.

Ha egy lekérdezés aggregációs függvénnyel (például DARABSZÁM) rendelkezik, a lekérdezési oldal részlegesen aggregált értéket adhat vissza az eredmények csak egy oldalára. Az ügyfeleknek el kell végezniük az eredmények második szintű összesítését a végső eredmények létrehozásához. Például az egyes lapokon visszaadott számok összege a teljes szám visszaadásához.

A lekérdezések adatkonzisztencia-házirendjének kezeléséhez használja a `x-ms-consistency-level` fejlécet az összes REST API kérelemben. A munkamenet-konzisztencia is megköveteli, hogy `x-ms-session-token` a lekérdezési kérelemben a legújabb cookie-fejlécet is megismétli. A lekérdezett tároló indexelési házirendje befolyásolhatja a lekérdezési eredmények konzisztenciáját is. A tárolók alapértelmezett indexelési házirend-beállításaival az index mindig aktuális az elem tartalmával, és a lekérdezési eredmények megegyeznek az adatkonzisztencia által választott értékkel. További információt a [Azure Cosmos DB konzisztencia-szintek] [konzisztencia-szintek] című témakörben talál.

Ha a tárolóban beállított indexelési szabályzat nem támogatja a megadott lekérdezést, a Azure Cosmos DB-kiszolgáló a "hibás kérés" 400 értéket adja vissza. Ez a hibaüzenet az indexelésből kizárni kívánt elérési úttal rendelkező lekérdezések esetében ad vissza. Megadhatja a `x-ms-documentdb-query-enable-scan` fejlécet, amely lehetővé teszi, hogy a lekérdezés végezzen vizsgálatot, ha egy index nem érhető el.

A lekérdezés-végrehajtás részletes mérőszámait úgy érheti el, hogy `x-ms-documentdb-populatequerymetrics` `true`beállítja a fejlécet a következőre:. További információ: [Azure Cosmos db SQL-lekérdezési metrikái](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

A .NET SDK a LINQ és az SQL lekérdezéseket is támogatja. Az alábbi példa bemutatja, hogyan végezheti el az előző szűrési lekérdezést a .NET-tel:

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

Az alábbi példa összehasonlítja az egyes elemeken belüli egyenlőség két tulajdonságát, és névtelen kivetítéseket használ.

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

A következő példa azt mutatja be, hogy a `SelectMany`program összekapcsolja a LINQ-t.

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

A .NET-ügyfél automatikusan megismétli a lekérdezés eredményének összes lapját a `foreach` blokkokban, ahogy az előző példában is látható. A [REST API](#REST-API) szakaszban bemutatott lekérdezési lehetőségek a .net SDK-ban is elérhetők, a `FeedOptions` `FeedResponse` `CreateDocumentQuery` metódus és az osztályok használatával. A lapok számát a `MaxItemCount` beállítás használatával szabályozhatja.

A lapozást explicit módon is szabályozhatja az `IDocumentQueryable` `IQueryable` objektummal való létrehozással, majd az `ResponseContinuationToken` értékek olvasásával és a `RequestContinuationToken` alkalmazásban `FeedOptions`történő visszaadásával. Beállíthatja `EnableScanInQuery` , hogy engedélyezze a vizsgálatokat, ha a konfigurált indexelési házirend nem támogatja a lekérdezést. Particionált tárolók esetén a paranccsal `PartitionKey` egyetlen partíción futtathatja a lekérdezést, bár a Azure Cosmos db automatikusan kinyerheti ezt a lekérdezési szövegből. A paranccsal `EnableCrossPartitionQuery` több partíción is futtathat lekérdezéseket.

További .NET-minták lekérdezésekkel kapcsolatban: [Azure Cosmos db .net-minták](https://github.com/Azure/azure-cosmos-dotnet-v3) a githubban.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript kiszolgálóoldali API

A Azure Cosmos DB egy programozási modellt biztosít a [JavaScript-alapú alkalmazások logikájának futtatásához](stored-procedures-triggers-udfs.md) a tárolókban, tárolt eljárások és eseményindítók használatával. A tároló szintjén regisztrált JavaScript-logika ezután a környezeti sav-tranzakciókban becsomagolt adatbázis-műveleteket adhat ki az adott tároló elemein.

Az alábbi példa bemutatja, hogyan használható `queryDocuments` a JavaScript Server API-ban a tárolt eljárások és eseményindítók használatával történő lekérdezések elvégzéséhez:

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

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Az Azure Cosmos DB konzisztenciaszintjei](consistency-levels.md)
