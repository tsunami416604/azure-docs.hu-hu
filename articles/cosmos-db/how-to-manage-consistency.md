---
title: Megismerheti, hogyan kezelhető a konzisztencia az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhető a konzisztencia az Azure Cosmos DB-ben.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 9d8c1296fc811d97dc9e7e66ad9bd9fdc79d66f9
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634336"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Az Azure Cosmos DB konzisztenciaszintjeinek kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure Cosmos DB-ben konzisztenciaszintek. Megismerheti, hogyan konfigurálhatja az alapértelmezett konzisztenciaszint, bírálja felül az alapértelmezett konzisztenciát, manuálisan munkamenet jogkivonatok kezeléséhez és megismerheti a Probabilistically korlátozott frissesség (PBS) metrikát.

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Az alapértelmezett konzisztenciaszint a konzisztenciaszint használó ügyfelek alapértelmezés szerint. Az ügyfelek felül lehet bírálni.

### <a name="cli"></a>parancssori felület

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

Ebben a példában egy új Azure Cosmos DB-fiókot hoz létre több főkiszolgálós engedélyezve van az USA keleti RÉGIÓJA és USA nyugati régiója. Az alapértelmezett konzisztencia-szabályzat munkamenet van beállítva.

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

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>Portál

Megtekintheti, vagy módosíthatja az alapértelmezett konzisztenciaszint, jelentkezzen be az Azure Portalon. Keresse meg az Azure Cosmos DB-fiókot, és nyissa meg a **alapértelmezett konzisztencia** ablaktáblán. Válassza ki az új alapértelmezett szeretne, majd válassza ki a konzisztencia szintjét **mentése**.

![Az Azure Portalon konzisztencia menü](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint felülírása

Az ügyfelek felülbírálhatja az alapértelmezett konzisztenciaszintet választ, amely szerint a szolgáltatás. Ez a beállítás a teljes ügyfél vagy kérelmenként is kell beállítani.

### <a id="override-default-consistency-dotnet"></a>.NET SDK

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

### <a id="override-default-consistency-java-async"></a>Java Async SDK

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

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Munkamenet-jogkivonatok használata

Munkamenet-jogkivonatok manuális kezelése, a munkamenet token beszerzése a válaszból, és állítsa be őket kérelmenként. Ha nem szeretne munkamenet jogkivonatok manuális kezelése, nem kell ezeket a mintákat használják. Az SDK nyilvántartja a munkamenet-jogkivonatok automatikusan. Ha nem állít be a munkamenet-jogkivonat manuálisan, alapértelmezés szerint az SDK-t használ a legutóbbi munkamenet-jogkivonat.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK

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

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

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

A PBS metrika megtekintéséhez nyissa meg az Azure Cosmos DB-fiókot az Azure Portalon. Nyissa meg a **metrikák** panelre, és válassza a **konzisztencia** fülre. Tekintse meg a gráf nevű **erősen konzisztens olvasás valószínűségét a számítási feladatok alapján (lásd a PBS)**.

![Az Azure Portalon PBS graph](./media/how-to-manage-consistency/pbs-metric.png)

Az Azure Cosmos DB-metrikák menü használatával lásd: Ez a metrika. Azt nem jelenik meg az Azure figyelési metrikákat élmény.

## <a name="next-steps"></a>További lépések

Többet szeretne megtudni az adatok ütközések kezelése, vagy helyezze át következő fő fogalmat az Azure Cosmos DB. Lásd az alábbi cikkeket:

* [Régiók közötti ütközések kezelése](how-to-manage-conflicts.md)
* [Particionálás és adatelosztás](partition-data.md)
