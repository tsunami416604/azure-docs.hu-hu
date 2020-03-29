---
title: Az Azure Cosmos DB erőforrástokenek használata a Gremlin SDK-val
description: Ismerje meg, hogyan hozhat létre erőforrás-jogkivonatokat, és hogyan használhatja őket a Graph-adatbázis eléréséhez.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 42f3c7f3351bddab429489dccf28587549d76e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897851"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Az Azure Cosmos DB erőforrástokenek használata a Gremlin SDK-val

Ez a cikk bemutatja, hogyan használhatja az [Azure Cosmos DB erőforrás-jogkivonatokat](secure-access-to-data.md) a Graph-adatbázis gremlin SDK-n keresztül való eléréséhez.

## <a name="create-a-resource-token"></a>Erőforrás-jogkivonat létrehozása

Az Apache TinkerPop Gremlin SDK nem rendelkezik erőforrás-tokenek létrehozásához használható API-val. Az *erőforrás-jogkivonat* kifejezés egy Azure Cosmos DB-koncepció. Erőforrás-jogkivonatok létrehozásához töltse le az [Azure Cosmos DB SDK-t.](sql-api-sdk-dotnet.md) Ha az alkalmazásnak erőforrás-jogkivonatokat kell létrehoznia, és azokat kell használnia a Graph-adatbázis eléréséhez, két külön SDK-ra van szükség.

Az erőforrás-jogkivonatok feletti objektummodell-hierarchiát a következő tagolás szemlélteti:

- **Azure Cosmos DB-fiók** – A legfelső szintű entitás, amelyhez `contoso.gremlin.cosmos.azure.com`dns-t társított (például).
  - **Az Azure Cosmos DB adatbázisa**
    - **Felhasználó**
      - **Engedély**
        - **Jogkivonat** – Egy engedélyobjektum-tulajdonság, amely azt jelzi, hogy milyen műveletek engedélyezettek vagy megtagadva.

Az erőforrás-token a `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`következő formátumot használja: . Ez a karakterlánc átlátszatlan az ügyfelek számára, és módosítás vagy értelmezés nélkül kell használni.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Erőforrás-jogkivonat használata
Az erőforrás-tokeneket közvetlenül "jelszó" tulajdonságként használhatja a GremlinServer osztály létrehozásakor.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Ugyanez a megközelítés működik minden TinkerPop Gremlin SDK.The same approach works in all TinkerPop Gremlin SDKs.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Korlát

Egyetlen Gremlin-fiókkal korlátlan számú jogkivonatot adhat ki. Azonban 1 órán belül egyszerre legfeljebb 100 jogkivonatot használhat. Ha egy alkalmazás túllépi az óránkénti jogkivonat-korlátot, a rendszer elutasítja a hitelesítési kérelmet, és a következő hibaüzenet jelenik meg: "Túllépte az egyidejűleg használható 100-as engedélyezett erőforrás-jogkivonat-korlátot." Nem működik az aktív kapcsolatok bezárása, amelyek meghatározott jogkivonatokat használnak az új jogkivonatok bővítőhelyfelszabadításához. Az Azure Cosmos DB Gremlin adatbázis-motor nyomon követi az egyedi jogkivonatok at a hitelesítési kérelem előtti órában.

## <a name="permission"></a>Engedély

Az alkalmazások gyakori hiba, amely az erőforrás-jogkivonatok használata közben találkozik, "Nem elegendő engedély az engedélyezési fejlécben a megfelelő kérelemhez. Próbálkozzon újra egy másik engedélyezési fejléccel." Ez a hiba akkor jelenik meg, amikor egy Gremlin-bejárás peremvagy csúcspont írására próbál meg írni, de az erőforrásjogkivonat csak *olvasási* engedélyeket ad. Ellenőrizze, hogy a bejárás tartalmazza-e a következő lépések valamelyikét: *.addV()*, *.addE()*, *.drop()* vagy *.property()*.

## <a name="next-steps"></a>További lépések
* [Szerepköralapú hozzáférés-vezérlés](role-based-access-control.md) az Azure Cosmos DB-ben
* [Ismerje meg, hogyan biztosítható az adatokhoz való hozzáférés biztonságossá tétele](secure-access-to-data.md) az Azure Cosmos DB-ben
