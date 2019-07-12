---
title: Jelentésszerkesztő egy RESTful-végpont egyéni szolgáltatók számára
description: Ez az oktatóanyag ismerteti, hogyan hozhat létre egy RESTful-végpont egyéni szolgáltatók kerül. Részletes, akkor lépnek, a kérelmek és válaszok a támogatott RESTful HTTP-metódusok kezelése.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800035"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Jelentésszerkesztő egy RESTful-végpont egyéni szolgáltatók számára

Egyéni szolgáltatók lehetővé teszik az Azure-ban a munkafolyamatok testreszabása. Egy egyéni szolgáltató az Azure között létrejött szerződés és a egy `endpoint`. Ebben az oktatóanyagban a folyamat a szerzői egyéni szolgáltatót RESTful fog áthaladni `endpoint`. Ha ismeri az Azure egyéni szolgáltatókat, [az Áttekintés az egyéni erőforrás-szolgáltatók](./custom-providers-overview.md).

Ebben az oktatóanyagban meg van osztva az alábbi lépéseket:

- Az egyéni műveletek és az egyéni erőforrások
- Egyéni erőforrások partíció storage-ban
- Egyéni szolgáltató RESTful-metódusok
- Integrálása a REST-alapú műveletek

Ebben az oktatóanyagban az alábbi oktatóanyagok hoz létre:

- [Az Azure Functions beállítása az Azure egyéni szolgáltatók](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Ebben az oktatóanyagban épít ki az előző oktatóanyagban. Az oktatóanyagban a lépések csak akkor fog működni, ha egy Azure-függvényt az egyéni szolgáltatók dolgozhat telepítése befejeződött.

## <a name="working-with-custom-actions-and-custom-resources"></a>Az egyéni műveletek és az egyéni erőforrások

Ebben az oktatóanyagban a funkció működjön, egy RESTful végpontot az egyéni szolgáltató frissítjük. Az Azure-beli erőforráshoz és művelethez mintájára készültek az alapszintű RESTful-specifikáció: PUT – egy új erőforrást hoz létre (példányok) GET - kérdezi le a meglévő erőforrást, DELETE – eltávolítja a meglévő erőforrást, a POST - eseményindító, és az (gyűjtemény) – felsorolja az összes létező erőforráson. A jelen oktatóanyag esetében fogjuk használni az Azure-táblák a tárolóként, de bármilyen adatbázis vagy tárolócsoport szolgáltatást használhatják.

## <a name="how-to-partition-custom-resources-in-storage"></a>Egyéni erőforrások partíció storage-ban

Egy RESTful szolgáltatás hozunk létre, mert a létrehozott erőforrásokat tárolása a storage kell. Az Azure Table storage igazolnia kell a partíció- és sorkulcsot kulcsok számára az adatok létrehozásához. Egyéni szolgáltatók számára az adatokat az egyéni szolgáltató kell particionálni. Egy bejövő kérésnek az egyéni szolgáltató küld, ha az egyéni szolgáltató felveszi a `x-ms-customproviders-requestpath` irányuló kimenő kérelem fejlécet a `endpoint`.

minta `x-ms-customproviders-requestpath` egy egyéni erőforrás fejléce:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

A fenti példa alapján `x-ms-customproviders-requestpath` fejléc, létrehozhatjuk a partitionKey és rowkey tulajdonságok esetén a tárolás, tegye a következőket:

Paraméter | Sablon | Leírás
---|---
partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | A partitionKey, hogy az adatok particionálása. A legtöbb esetben az adatok kell particionálni az egyéni szolgáltató példánya.
Rowkey tulajdonságok esetén | a(z) {myResourceType}: {myResourceName}' | A rowkey tulajdonságok esetén a az adatok egyedi azonosítója. A legtöbbször az erőforrás neve.

Emellett azt is kell hozzon létre egy új osztályt az egyéni erőforrás modellezésére. Ebben az oktatóanyagban hozzáadunk a `CustomResource` a függvény, amely egy általános osztály, amely bármelyik képkockák adatok osztályt:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Ez létrehoz egy alapszintű osztály alapján `TableEntity`, amely adatok tárolására szolgál. A `CustomResource` osztály örökli a két tulajdonság `TableEntity`: partitionKey és rowkey tulajdonságok esetén.

## <a name="support-custom-provider-restful-methods"></a>Egyéni szolgáltató RESTful-metódusok

> [!NOTE]
> A kódot közvetlenül az oktatóanyag a nem másol, ha a válasz tartalma legyen érvényes JSON, és beállítja a `Content-Type` , fejléc `application/json`.

Most, hogy a telepítő adatparticionálási, nézzük elnevezéssel az alapszintű CRUD-MŰVELETEKKEL és az eseményindító módszerek az egyéni erőforrások és az egyéni műveletek. Egyéni szolgáltatók proxyként, mivel a kérések és válaszok kell modellezése és kezeli a RESTful `endpoint`. Kövesse az alábbi kódrészletek az alapvető RESTful műveleteit:

### <a name="trigger-custom-action"></a>Egyéni művelet kiváltása

Egyéni szolgáltatók, az egyéni művelet indított el `POST` kérelmeket. Egyéni művelet igény szerint elfogadhatja a kérés törzse, amely tartalmazza a szükséges bemeneti paramétereket. A művelet kell, majd lépjen vissza a válasz signally művelet eredménye vissza, valamint azt a sikeres vagy sikertelen-e. Ebben az oktatóanyagban hozzáadunk a metódus `TriggerCustomAction` a függvényt:

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

A `TriggerCustomAction` metódus fogad egy bejövő kérésnek, és egyszerűen majd továbbítja a válasz egy sikeres állapotkód biztonsági másolatot. 

### <a name="create-custom-resource"></a>Hozzon létre egyéni erőforrás

Egyéni szolgáltatók, egy egyéni erőforrás jön létre keresztül `PUT` kérelmeket. Az egyéni szolgáltató fogad el egy JSON-kérés törzsének, amely az egyéni erőforrás tulajdonságai készletét tartalmazza. Erőforrások az Azure-ban, hajtsa végre a REST-alapú modell. Kell, hogy ugyanazon kérelem URL-cím erőforrás létrehozásához használt is képes lekérni, és törölje az erőforrást. Ebben az oktatóanyagban hozzáadunk a metódus `CreateCustomResource` új erőforrásokat hozhatnak létre:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

A `CreateCustomResource` módszer frissíti a bejövő kéréseket az Azure egyedi mezőket: `id`, `name`, és `type`. Ezek a legfelső szintű Azure-ban szolgáltatások által használt tulajdonságokat. Lehetővé teszi azok az egyéni szolgáltató integrálhatja más szolgáltatásokkal, például az Azure Policy, az Azure Resource Manager-sablonokkal és az Azure-Tevékenységnaplók.

Tulajdonság | Sample | Leírás
---|---|---
name | '{myCustomResourceName}' | Az egyéni erőforrás neve.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Az erőforrás-típus névtere.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | Az erőforrás-azonosítója.

A Tulajdonságok hozzáadásán azt is mentse a dokumentumot az Azure Table Storage. 

### <a name="retrieve-custom-resource"></a>Egyéni erőforrás lekérése

Egyéni szolgáltatók, egy egyéni erőforrás használatával beolvasott `GET` kérelmeket. Az egyéni szolgáltató fog *nem* fogadja el a JSON-kérelem törzse. Abban az esetben, `GET` kérelmek, a **végpont** kell használnia a `x-ms-customproviders-requestpath` fejléc vissza a már létrehozott erőforrást. Ebben az oktatóanyagban hozzáadunk a metódus `RetrieveCustomResource` beolvasni a meglévő erőforrások:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Az Azure-ban erőforrásokat egy REST-alapú modellt érdemes követnie. A kérelem URL-CÍMÉT, amely a létrehozott erőforrás is kell visszaadnia az erőforrást, ha egy `GET` kérések.

### <a name="remove-custom-resource"></a>Egyéni erőforrás eltávolítása

Egyéni szolgáltatók, egy egyéni erőforrást eltávolítanak keresztül `DELETE` kérelmeket. Az egyéni szolgáltató fog *nem* fogadja el a JSON-kérelem törzse. Abban az esetben, `DELETE` kérelmek, a **végpont** kell használnia a `x-ms-customproviders-requestpath` fejléc törli a már létrehozott erőforrást. Ebben az oktatóanyagban hozzáadunk a metódus `RemoveCustomResource` meglévő erőforrások eltávolítására:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Az Azure-ban erőforrásokat egy REST-alapú modellt érdemes követnie. A kérelem URL-CÍMÉT, amely a létrehozott erőforrás is kell törölni az erőforrást, ha egy `DELETE` kérések.

### <a name="list-all-custom-resources"></a>Minden egyéni erőforrások listázása

Egyéni szolgáltatók, a meglévő egyéni erőforrások listájának keresztül gyűjtemény számba vehetők `GET` kérelmeket. Az egyéni szolgáltató fog *nem* fogadja el a JSON-kérelem törzse. Gyűjtemény esetén `GET` kérelmek, a `endpoint` kell használnia a `x-ms-customproviders-requestpath` fejléc a létrehozott erőforrások számbavétele. Ebben az oktatóanyagban hozzáadunk a metódus `EnumerateAllCustomResources` a meglévő erőforrások számbavétele.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> A sorkulcs nagyobb, mint és kevesebb, mint az Azure Table szintaxis "startswith" lekérdezés végrehajtása a karakterláncokat. 

Az összes meglévő erőforrások listázása, hogy létrehozhat egy Azure Table lekérdezést, amely biztosítja, hogy az erőforrások találhatók az egyéni szolgáltató partíció. A lekérdezést, majd ellenőrzi, hogy ugyanazzal a elindítja a sorkulcs `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integrálása a REST-alapú műveletek

A REST-alapú módszerek hozzá vannak adva a funkciót, ha a fő is frissítjük `Run` metódust kell meghívni a funkciók különböző többi kezelésére kéri:

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for an custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
``` 

A frissített `Run` mód mostantól tartalmazza a `tableStorage` bemeneti kötést, amely hozzá lett adva a az Azure Table storage. Most már a metódus első részét olvassa a `x-ms-customproviders-requestpath` fejlécére, és használja a `Microsoft.Azure.Management.ResourceManager.Fluent` könyvtár az erőforrás-azonosító néven érték értelmezése A `x-ms-customproviders-requestpath` fejléc az egyéni szolgáltató által küldött, és a bejövő kérelem elérési útját jelöli meg. Elemzett erőforrás-Azonosítóját használja, azt most generálhat a partitionKey és rowkey tulajdonságok esetén az adatok található, vagy egyéni erőforrásokat tárolja.

A metódusok és osztályok hozzáadásán kell frissíteni, a használatával a függvény módszerek. A fájl elejéhez adja hozzá a következőket:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Ha kapott elveszett adott Ez az oktatóanyag során, a befejezett kódminta található a [egyéni szolgáltató C# RESTful-végpont referencia](./reference-custom-providers-csharp-endpoint.md). A függvény végrehajtása után mentse a függvény URL-CÍMÉT, amely segítségével aktiválja a függvényt, mivel a későbbi oktatóanyagokban fogja használni.

## <a name="next-steps"></a>További lépések

Ebben a cikkben azt készített egy RESTful-végpont használata az Azure egyéni szolgáltató `endpoint`. Nyissa meg a következő cikkben megtudhatja, hogyan hozhat létre egy egyéni szolgáltatót.

- [Oktatóanyag: Egy egyéni szolgáltató létrehozása](./tutorial-custom-providers-create.md)
