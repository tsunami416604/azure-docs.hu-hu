---
title: Konzisztencia kezelése az Azure Cosmos DB-ben
description: Megtudhatja, hogy miként konfigurálhatja és kezelheti a konzisztenciaszinteket az Azure Cosmos DB-ban az Azure Portal, a .Net SDK, a Java SDK és számos más SDK használatával
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 651daa0af8188b386220d97390e7a61615f94120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369403"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Az Azure Cosmos DB konzisztenciaszintjeinek kezelése

Ez a cikk ismerteti, hogyan kezelheti a konzisztenciaszinteket az Azure Cosmos DB-ben. Megtudhatja, hogyan konfigurálhatja az alapértelmezett konzisztenciaszintet, felülbírálhatja az alapértelmezett konzisztenciát, manuálisan kezelheti a munkamenet-jogkivonatokat, és hogyan ismerheti meg a Probabilistically Bounded Staleness (PBS) metrikát.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Az [alapértelmezett konzisztenciaszint](consistency-levels.md) az ügyfelek által alapértelmezés szerint használt konzisztenciaszint. Az ügyfelek bármikor felülírhatják.

### <a name="cli"></a>parancssori felület

```azurecli
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

Ebben a példában egy új Azure Cosmos-fiókot hoz létre, amelyen engedélyezve van az USA keleti és az USA nyugati régióiban. Az alapértelmezett konzisztenciaszint munkamenet-konzisztenciára van állítva. *Session*

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Azure portál

Az alapértelmezett konzisztenciaszint megtekintéséhez vagy módosításához jelentkezzen be az Azure Portalra. Keresse meg az Azure Cosmos-fiókot, és nyissa meg az **alapértelmezett konzisztencia** ablaktáblát. Válassza ki a kívánt konzisztenciaszintet az új alapértelmezettként, majd kattintson a **Mentés gombra.** Az Azure Portal is biztosít egy vizualizációs különböző konzisztenciaszintek zenei jegyzetek. 

![Konzisztencia menü az Azure Portalon](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint felülírása

Az ügyfelek felülírhatják a szolgáltatás által beállított alapértelmezett konzisztenciaszintet. Konzisztenciaszint állítható be egy kérés, amely felülbírálja az alapértelmezett konzisztenciaszint a fiók szintjén beállított.

### <a name="net-sdk-v2"></a><a id="override-default-consistency-dotnet"></a>.NET SDK V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a name="net-sdk-v3"></a><a id="override-default-consistency-dotnet-v3"></a>.NET SDK V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
```

### <a name="java-async-sdk"></a><a id="override-default-consistency-java-async"></a>Java Async SDK

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a name="java-sync-sdk"></a><a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Munkamenet-jogkivonatok használata

Az Azure Cosmos DB egyik konzisztenciaszintje a munkamenet-konzisztencia. *Session* Ez az alapértelmezett szint a Cosmos-fiókok alapértelmezetten alkalmazott. A munkamenet-konzisztenciával végzett munka során az ügyfél minden egyes olvasási/lekérdezési kérelemmel belső munkamenet-jogkivonatot fog használni a beállított konzisztenciaszint fenntartásának biztosításához. *Session*

A munkamenet-jogkivonatok manuális kezeléséhez kérje le a munkamenet-jogkivonatot a válaszból, és állítsa be őket kérésenként. Ha nem kell manuálisan kezelnie a munkamenet-jogkivonatokat, nem kell használnia ezeket a mintákat. Az SDK automatikusan nyomon követi a munkamenet-jogkivonatokat. Ha nem állítja be manuálisan a munkamenet-jogkivonatot, alapértelmezés szerint az SDK a legutóbbi munkamenet-jogkivonatot használja.

### <a name="net-sdk-v2"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a name="net-sdk-v3"></a><a id="utilize-session-tokens-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```

### <a name="java-async-sdk"></a><a id="utilize-session-tokens-java-async"></a>Java Async SDK

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a name="java-sync-sdk"></a><a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>A valószínűség alapján korlátozott frissesség (PBS) metrika monitorozása

Mennyire végleges a végleges konzisztencia? Az átlagos esetben, tudunk kínálnak állság határok tekintetében verzióelőzmények és az idő. A [**Probabilistically Bounded Staleness (PBS)**](https://pbs.cs.berkeley.edu/) metrika megpróbálja számszerűsíteni a valószínűségét a frissesség, és azt a metrika. A PBS-metrika megtekintéséhez nyissa meg az Azure Cosmos-fiókot az Azure Portalon. Nyissa meg a **Metrikák** ablaktáblát, és válassza **Probability of strongly consistent reads based on your workload (see PBS)** a **Konzisztencia** lapot.

![PBS-grafikon az Azure portalon](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>További lépések

További információ az adatütközések kezeléséről, illetve az Azure Cosmos DB következő kulcskoncepciójának továbbfejlesztéséről. Lásd az alábbi cikkeket:

* [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md)
* [Régiók közötti ütközések kezelése](how-to-manage-conflicts.md)
* [Particionálás és adatelosztás](partition-data.md)
* [Konzisztencia kompromisszumok a modern elosztott adatbázis-rendszerek tervezésében](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Magas rendelkezésre állás](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
