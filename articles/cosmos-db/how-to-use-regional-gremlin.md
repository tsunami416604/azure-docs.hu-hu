---
title: Az Azure Cosmos DB Graph adatbázis regionális végpontjai
description: További információ az alkalmazás legközelebbi Graph-adatbázis-végpontjához való csatlakozásról
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 7aa1e0aa6bbbee9d40eb0d48318a8e2908a75f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897859"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Az Azure Cosmos DB Graph-fiók regionális végpontjai
Az Azure Cosmos DB Graph adatbázis [globálisan elosztott,](distribute-data-globally.md) így az alkalmazások több olvasási végpontot is használhatnak. A több helyen írási hozzáférést igénylő alkalmazásoknak lehetővé kell tenniük [a többfőes](how-to-multi-master.md) képességet.

Egynél több régió kiválasztása okok:
1. **Horizontális olvasási méretezhetőség** – az alkalmazás terhelésének növekedésével célszerű lehet az olvasási forgalmat különböző Azure-régiókba irányítani.
2. **Kisebb késés** – csökkentheti a hálózati késés terhelését az egyes bejárások útválasztásával olvasási és írási forgalmat a legközelebbi Azure-régióban.

**Az adatok tárolási** követelménye az Azure Resource Manager-szabályzat Cosmos DB-fiókra való beállításával érhető el. Az ügyfél korlátozhatja azokat a régiókat, amelyekbe a Cosmos DB replikálja az adatokat.

## <a name="traffic-routing"></a>Forgalom útválasztása

A Cosmos DB Graph adatbázis-motor több régióban fut, amelyek mindegyike több fürtöt tartalmaz. Minden fürt több száz géppel rendelkezik. Cosmos DB Graph fiók DNS CNAME *accountname.gremlin.cosmos.azure.com* feloldja a DNS Egy fürt rekordja. A terheléselosztó egyetlen IP-címe elrejti a belső fürttopológiát.

A Cosmos DB Graph-fiók minden régiójához létrejön egy regionális DNS CNAME rekord. A regionális végpont formátuma *accountname-region.gremlin.cosmos.azure.com.* A regionális végpont régiószegmense az [Azure régió](https://azure.microsoft.com/global-infrastructure/regions) nevéből az összes szóköz eltávolításával érhető el. A globális `"East US 2"` adatbázisfiók régiójának `"contoso"` például DNS CNAME *contoso-eastus2.gremlin.cosmos.azure.com*

TinkerPop Gremlin kliens célja, hogy működjön együtt egy szerver. Az alkalmazás globális írható DNS CNAME-t használhat az olvasási és írási forgalomhoz. A régióbarát alkalmazásoknak regionális végpontot kell használniuk az olvasási forgalomhoz. Csak akkor használjon regionális végpontot írási forgalomhoz, ha egy adott régió írási műveletek fogadására van konfigurálva. 

> [!NOTE]
> A Cosmos DB Graph motor képes fogadni az olvasási régióban az olvasási régióban a régió proxyáltal történő írási műveletet. Nem ajánlott írásokat küldeni csak olvasható régióba, mivel növeli a bejárási késést, és a jövőben korlátozások vonatkoznak rá.

A CNAME globális adatbázisfiók mindig érvényes írási területre mutat. Az írási régió kiszolgálóoldali feladatátvétele során a Cosmos DB frissíti a CNAME globális adatbázis-fiókot, hogy az új régióra mutasson. Ha az alkalmazás nem tudja kezelni a forgalom átirányítását a feladatátvétel után, akkor a DNS CNAME globális adatbázisfiókot kell használnia.

> [!NOTE]
> A Cosmos DB nem irányítja a forgalmat a hívó földrajzi közelsége alapján. Minden alkalmazástól megkell adnia a megfelelő régiót az egyedi alkalmazásigényeknek megfelelően.

## <a name="portal-endpoint-discovery"></a>Portálvégpont felderítése

Az Azure Cosmos DB Graph-fiók régióinak listájának legegyszerűbb lekérnie az Azure Portal áttekintése az Azure Portalon. Működni fog az alkalmazások, amelyek nem módosítják a régiók gyakran, vagy egy módja annak, hogy frissítse a listát alkalmazás konfiguráción keresztül.

![A Cosmos DB Graph-fiók régióinak lekérése a portálról](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

Az alábbi példa a regionális Gremlin végpont elérésének általános elveit mutatja be. Az alkalmazásnak figyelembe kell vennie a forgalmat küldő régiók számát, és a megfelelő Gremlin-ügyfelek számát példányosítani.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>SDK-végpont felderítése

Az alkalmazás használhatja [az Azure Cosmos DB SDK-t](sql-api-sdk-dotnet.md) a Graph-fiók olvasási és írási helyeinek felderítéséhez. Ezek a helyek bármikor módosíthatók a kiszolgálóoldali kézi újrakonfigurálás vagy az automatikus feladatátvétel révén.

A TinkerPop Gremlin SDK nem rendelkezik API-val a Cosmos DB Graph adatbázis-fiók régióinak felderítéséhez. A futásidejű végpontfelderítést igénylő alkalmazásoknak 2 különálló SDK-t kell üzemeltetnia a folyamattérben.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>További lépések
* [Adatbázisfiókok vezérlésének kezelése](how-to-manage-database-account.md) az Azure Cosmos DB-ben
* [Magas rendelkezésre állás](high-availability.md) az Azure Cosmos DB-ben
* [Globális disztribúció az Azure Cosmos DB-vel – a motorháztető alatt](global-dist-under-the-hood.md)
* [Azure CLI-minták](cli-samples.md) az Azure Cosmos DB-hez
