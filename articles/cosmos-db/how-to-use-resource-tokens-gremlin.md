---
title: Erőforrás-tokenek Azure Cosmos DB Gremlin
description: Ismerje meg, hogyan hozhat létre erőforrás-jogkivonatokat, és hogyan használhatja őket a Graph-adatbázis eléréséhez
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806910"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Erőforrás-tokenek Azure Cosmos DB Gremlin
Ez a cikk azt ismerteti, hogyan használhatók [Cosmos db erőforrás-tokenek](secure-access-to-data.md) a Graph-adatbázis eléréséhez a Gremlin SDK-n keresztül.

## <a name="create-a-resource-token"></a>Erőforrás-jogkivonat létrehozása

Az TinkerPop Gremlin SDK nem rendelkezik API-val erőforrás-tokenek létrehozásához. Az erőforrás-jogkivonat Cosmos DB koncepció. Erőforrás-tokenek létrehozásához töltse le [Azure Cosmos db SDK](sql-api-sdk-dotnet.md)-t. Ha az alkalmazásnak erőforrás-jogkivonatokat kell létrehoznia, és azokat a Graph-adatbázis eléréséhez szeretné használni, akkor 2 különálló SDK-t kell használnia.

Objektummodell-hierarchia az erőforrás-jogkivonatok felett:
- **Cosmos db fiók** – legfelső szintű entitás, amelyhez DNS társítva van, például:`contoso.gremlin.cosmos.azure.com`
  - **Adatbázis Cosmos DB**
    - **Felhasználói**
      - **Engedéllyel**
        - *Token* – az **engedély** objektum tulajdonsága, amely azt jelzi, hogy milyen műveleteket lehet engedélyezni vagy megtagadni.

Az erőforrás-jogkivonat formátuma `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Ez a karakterlánc nem átlátszó az ügyfelek számára, és a következőképpen használható: módosítás vagy értelmezés nélkül.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Erőforrás-jogkivonat használata
Az erőforrás-jogkivonatok közvetlenül a "password" tulajdonságként használhatók `GremlinServer` az osztály összeállításakor.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Ugyanez a módszer működik az összes TinkerPop Gremlin SDK-ban.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Korlát

Egyetlen Gremlin-fiók korlátlan számú tokent tud kiadni, azonban legfeljebb **100** tokent lehet egyszerre **1 órán**belül használni. Ha az alkalmazás túllépi a jogkivonat-korlátot óránként, a hitelesítési kérelem elutasítása hibaüzenetet `"Exceeded allowed resource token limit of 100 that can be used concurrently"`kap. Az aktív kapcsolatok bezárása adott jogkivonatokkal az új tokenekhez tartozó tárolóhelyek felszabadítása nem lesz gyümölcsöző. Cosmos DB Gremlin-adatbázismotor a hitelesítési kérelem előtt nyomon követi a különböző tokeneket az elmúlt órában.

## <a name="permission"></a>Engedély

Az általános hibaüzenetek az `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`erőforrás-jogkivonatok használatakor jönnek át. Ezt a hibát akkor adja vissza a rendszer, amikor a Gremlin bejárási kísérlet egy Edge vagy egy `Read` csúcspont írására, de az erőforrás-jogkivonat csak engedélyt ad. Vizsgálja meg a bejárást, hogy az a következő lépések bármelyikét tartalmazza `.drop()`-e `.property()`: `.addV()`, `.addE()`,, vagy.

## <a name="next-steps"></a>További lépések
* [Szerepköralapú hozzáférés-vezérlés](role-based-access-control.md) a Azure Cosmos DBban
* [Megtudhatja, hogyan védheti meg Azure Cosmos db az adathozzáférését](secure-access-to-data.md)
