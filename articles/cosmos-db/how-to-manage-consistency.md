---
title: Megismerheti, hogyan kezelhető a konzisztencia az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhető a konzisztencia az Azure Cosmos DB-ben.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: aa42737274432180540584c01a7125c1a9765b49
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243656"
---
# <a name="manage-consistency"></a>Konzisztencia kezelése

Ez a cikk ismerteti az alapértelmezett konzisztencia beállításának különböző módjait, a konzisztencia felülírását az ügyfélen, a munkamenet-jogkivonatok manuális kezelését, valamint a valószínűség alapján korlátozott frissesség (PBS) metrika működését.

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Az alapértelmezett konzisztenciaszint az ügyfelek által alapértelmezés szerint használt konzisztenciaszint. Ezt az ügyfelek felülírhatják.

### <a name="cli"></a>parancssori felület

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

Az alábbi példában egy új Cosmos DB-fiókot hozunk létre. Az USA keleti régiójában és az USA nyugati régiójában engedélyezve van a több főkiszolgálós beállítás. Az alapértelmezett konzisztenciaszabályzat a Korlátozott frissesség; a maximális frissességi időtartam 10 másodperc, az elavult kérelmek maximális engedélyezett száma pedig 200.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>Portál

Az alapértelmezett konzisztenciaszint megtekintéséhez vagy módosításához jelentkezzen be az Azure Portalra. Keresse meg Cosmos DB-fiókját, és nyissa meg az **Alapértelmezett konzisztencia** panelt. Válassza ki a konzisztenciaszintet, amelyet új alapértelmezettként kíván beállítani, majd kattintson a Mentés gombra.

![A konzisztenciamenüt ábrázoló kép az Azure Portalon](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint felülírása

Az ügyfelek felülírhatják a szolgáltatás által beállított alapértelmezett konzisztenciaszintet. Ezt megtehetik a teljes ügyfélre vonatkozóan vagy kérelmenként.

### <a id="override-default-consistency-dotnet"></a>.NET

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async

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

### <a id="override-default-consistency-java-sync"></a>Java Sync

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Munkamenet-jogkivonatok használata

Ha manuálisan szeretné kezelni a munkamenet-jogkivonatokat, beszerezheti őket a válaszokból, és kérelmenként beállíthatja őket. Ha nem szeretné manuálisan kezelni a munkamenet-jogkivonatokat, nem kell használnia az alábbi példákat. Az SDK automatikusan nyomon követi a munkamenet-jogkivonatokat, és mindig a legújabbat használja, ha nem állít be manuálisan munkamenet-jogkivonatot.

### <a id="utilize-session-tokens-dotnet"></a>.NET

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async

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

### <a id="utilize-session-tokens-java-sync"></a>Java Sync

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python

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

A PBS metrika megtekintéséhez keresse meg Cosmos DB-fiókját az Azure Portalon, és nyissa meg a **Metrikák** panelt. Ott kattintson a **Konzisztencia** lapra, és tekintse meg az **Erősen konzisztens beolvasások valószínűsége a számítási feladatok alapján (lásd PBS)** nevű grafikont.

![A PBS grafikon képe az Azure Portalon](./media/how-to-manage-consistency/pbs-metric.png)

A metrikát a Cosmos DB metrikamenüjében tekintheti meg. Az Azure Monitoring-metrikák között nem fog megjelenni.

## <a name="next-steps"></a>További lépések

Részletesebben megismerheti az adatütközések kezelését, vagy elsajátíthatja a Cosmos DB-vel kapcsolatos következő fontos fogalmat az alábbi dokumentumokból:

* [Régiók közötti ütközések kezelése](how-to-manage-conflicts.md)
* [Particionálás és adatelosztás](partition-data.md)
