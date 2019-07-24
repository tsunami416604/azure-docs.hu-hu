---
title: Megismerheti, hogyan kezelhető a konzisztencia az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhető a konzisztencia az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: 2617aba0d790209d83f410ee632ffad43c952d55
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356421"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Az Azure Cosmos DB konzisztenciaszintjeinek kezelése

Ez a cikk azt ismerteti, hogyan kezelhető a konzisztencia szintjei Azure Cosmos DBban. Megtudhatja, hogyan konfigurálhatja az alapértelmezett konzisztencia-szintet, felülbírálhatja az alapértelmezett konzisztenciát, manuálisan kezelheti a munkamenet-jogkivonatokat, és megismerheti a Probabilistically kötött elavulás (PBS) metrikáját.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Az [alapértelmezett konzisztencia-szint](consistency-levels.md) az ügyfelek által alapértelmezés szerint használt konzisztencia-szint. Az ügyfelek bármikor felülbírálják.

### <a name="cli"></a>parancssori felület

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

Ez a példa egy új Azure Cosmos-fiókot hoz létre, amely több írási régiót is engedélyez, az USA keleti régiójában és az USA nyugati régiójában. Az alapértelmezett konzisztencia-érték a *munkamenet* konzisztenciája.

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

### <a name="azure-portal"></a>Azure Portal

Ha szeretné megtekinteni vagy módosítani az alapértelmezett konzisztencia-szintet, jelentkezzen be a Azure Portalba. Keresse meg az Azure Cosmos-fiókját, és nyissa meg az **alapértelmezett konzisztencia** -ablaktáblát. Válassza ki az új alapértelmezettként használni kívánt konzisztencia szintjét, majd kattintson a **Mentés**gombra. A Azure Portal a különböző konzisztencia-szintek vizualizációját is megjeleníti zenei megjegyzésekkel. 

![Konzisztencia menü a Azure Portal](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint felülírása

Az ügyfelek felülírhatják a szolgáltatás által beállított alapértelmezett konzisztenciaszintet. A konzisztencia szintje igény szerint állítható be, ami felülbírálja a fiók szintjén beállított alapértelmezett konzisztencia-szintet.

### <a id="override-default-consistency-dotnet"></a>.NET SDK V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>.NET SDK V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
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
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Munkamenet-jogkivonatok használata

A Azure Cosmos DB konzisztencia-szintjeinek egyike a *munkamenet* konzisztenciája. Alapértelmezés szerint ez az alapértelmezett szint a Cosmos-fiókok esetében. Ha a *munkamenet* konzisztenciájával dolgozik, az ügyfél a munkamenet-jogkivonatot belsőleg fogja használni minden olvasási/lekérdezési kérelemmel annak érdekében, hogy a rendszer karbantartsa a beállított konzisztencia-szintet.

A munkamenet-tokenek manuális kezeléséhez szerezze be a válaszból a munkamenet-jogkivonatot, és állítsa be őket kérésre. Ha nem kell manuálisan kezelnie a munkamenet-jogkivonatokat, ezeket a mintákat nem kell használnia. Az SDK automatikusan nyomon követi a munkamenet-jogkivonatokat. Ha a munkamenet-tokent nem manuálisan állítja be, az SDK alapértelmezés szerint a legutóbbi munkamenet-tokent használja.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
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

Hogyan lehetséges a végleges konzisztencia? Az átlagos esetben a korábbi verziókra és időpontokra vonatkozó elévülési korlátokat is kínáljuk. A [**Probabilistically határos elavulás (PBS)** ](https://pbs.cs.berkeley.edu/) mérőszáma megpróbálja számszerűsíteni az elavultság valószínűségét, és mérőszámként jeleníti meg azt. A PBS-metrika megtekintéséhez lépjen a Azure Portal Azure Cosmos-fiókjába. Nyissa  meg a metrikák ablaktáblát, és válassza a **konzisztencia** lapot. Tekintse meg a számítási **feladatok alapján a nagy mértékben konzisztens olvasások valószínűségét (lásd: PBS)** .

![PBS gráf a Azure Portal](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>További lépések

További információ az Adatütközések kezeléséről, illetve a Azure Cosmos DB következő kulcsfontosságú koncepciójának bevezetéséről. Lásd az alábbi cikkeket:

* [Azure Cosmos DB konzisztenciáji szintjei](consistency-levels.md)
* [Régiók közötti ütközések kezelése](how-to-manage-conflicts.md)
* [Particionálás és adatelosztás](partition-data.md)
* [Konzisztencia-kompromisszumok a modern elosztott adatbázis-rendszerek kialakításában](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Magas rendelkezésre állás](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
