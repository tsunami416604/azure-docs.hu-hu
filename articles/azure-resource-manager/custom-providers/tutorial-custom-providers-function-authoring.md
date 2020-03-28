---
title: RESTful-végpont létrehozása
description: Ez az oktatóanyag bemutatja, hogyan lehet egy RESTful végpontot egyéni szolgáltatók számára. Részletezi, hogyan kell kezelni a kérelmeket és válaszokat a támogatott RESTful HTTP-metódusok.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7f6c51211ce0572797ade659b9316003502da1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650022"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>ReSTful végpont készítése egyéni szolgáltatók számára

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatókkal testreszabhatja a munkafolyamatokat az Azure-ban. Ez az oktatóanyag bemutatja, hogyan lehet egy egyéni szolgáltató RESTful végpont. Ha nem ismeri az Azure egyéni szolgáltatókat, tekintse [meg az egyéni erőforrás-szolgáltatók áttekintését.](overview.md)

> [!NOTE]
> Ez az oktatóanyag az [Azure Functions beállítása az Azure egyéni szolgáltatókhoz](./tutorial-custom-providers-function-setup.md)című oktatóanyagra épül. Az oktatóanyag néhány lépése csak akkor működik, ha egy Azure-függvényalkalmazás egyéni szolgáltatókkal való együttműködésre van beállítva.

## <a name="work-with-custom-actions-and-custom-resources"></a>Egyéni műveletek és egyéni erőforrások kal való kapcsolat

Ebben az oktatóanyagban frissíti a függvényalkalmazást, hogy az egyéni szolgáltató RESTful végpontjaként működjön. Az Azure-beli erőforrások és műveletek a következő alapvető RESTful specifikáció kúra után vannak modellezve:

- **PUT**: Új erőforrás létrehozása
- **GET (például)**: Meglévő erőforrás lekérése
- **DELETE**: Meglévő erőforrás eltávolítása
- **POST**: Művelet aktiválása
- **GET (gyűjtemény)**: Az összes meglévő erőforrás listázása

 Ebben az oktatóanyagban az Azure Table storage-t használja. De minden adatbázis vagy tárolási szolgáltatás működhet.

## <a name="partition-custom-resources-in-storage"></a>Egyéni erőforrások particionálása a tárolóban

Mivel RESTful szolgáltatást hoz létre, a létrehozott erőforrásokat tárolnia kell. Az Azure Table storage, létre kell hoznia a partíció és a sor kulcsok at az adatokhoz. Egyéni szolgáltatók esetében az adatokat az egyéni szolgáltatóra kell particionálni. Amikor egy bejövő kérelmet küld az egyéni szolgáltatónak, az egyéni szolgáltató hozzáadja a fejlécet a `x-ms-customproviders-requestpath` kimenő kérelmekhez a végponthoz.

A következő példa `x-ms-customproviders-requestpath` egy egyéni erőforrás fejlécét mutatja be:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

A példa fejléce `x-ms-customproviders-requestpath` alapján létrehozhatja a *partitionKey* és *rowKey* paramétereket a tárolóhoz az alábbi táblázatban látható módon:

Paraméter | Sablon | Leírás
---|---|---
*partíciókulcs* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | A *partitionKey* paraméter megadja az adatok particionálásának módját. Az adatokat általában az egyéni szolgáltatópéldány particionálja.
*rowKey (sorkulcs)* | `{myResourceType}:{myResourceName}` | A *rowKey* paraméter az adatok egyedi azonosítóját adja meg. Az azonosító általában az erőforrás neve.

Az egyéni erőforrás modellezéséhez új osztályt is létre kell hoznia. Ebben az oktatóanyagban a következő **CustomResource-osztályt** adja hozzá a függvényalkalmazáshoz:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** egy egyszerű, általános osztály, amely elfogadja a bemeneti adatokat. Ez a **TableEntity**, amely az adatok tárolására használt. A **CustomResource** osztály két tulajdonságot örököl a **TableEntity**- **partitionKey** és **rowKey**tulajdonságtól.

## <a name="support-custom-provider-restful-methods"></a>Egyéni szolgáltatóre hivatkozó RESTful metódusok támogatása

> [!NOTE]
> Ha nem közvetlenül ebből az oktatóanyagból másolja a kódot, a válasz `Content-Type` tartalomának érvényes JSON-nak kell lennie, amely a fejlécet állítja a programra. `application/json`

Most, hogy beállította az adatok particionálását, hozza létre az alapvető CRUD és az egyéni erőforrások és egyéni műveletek metódusok. Mivel az egyéni szolgáltatók proxyként működnek, a RESTful végpontnak modelleznie kell és kezelnie kell a kérést és a választ. A következő kódrészletek bemutatják, hogyan kell kezelni az alapvető RESTful műveleteket.

### <a name="trigger-a-custom-action"></a>Egyéni művelet aktiválása

Az egyéni szolgáltatók esetében az egyéni művelet postai kérelmeken keresztül aktiválódik. Az egyéni művelet opcionálisan elfogadhatja a bemeneti paraméterek készletét tartalmazó kéréstörzset. A művelet ezután egy olyan választ ad vissza, amely jelzi a művelet eredményét, és azt, hogy sikerült-e vagy sem.

Adja hozzá a következő **TriggerCustomAction** metódust a függvényalkalmazáshoz:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
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

A **TriggerCustomAction** metódus elfogadja a bejövő kérelmet, és egyszerűen visszhangozza vissza a választ egy állapotkódot.

### <a name="create-a-custom-resource"></a>Egyéni erőforrás létrehozása

Egyéni szolgáltatók számára egy egyéni erőforrás jön létre a PUT-kérelmeken keresztül. Az egyéni szolgáltató elfogadja a JSON-kérelem törzse, amely tartalmazza az egyéni erőforrás tulajdonságait. Az Azure-beli erőforrások egy RESTful modellt követnek. Ugyanazzal a kérelem URL-címével hozhat létre, kérhet le vagy törölhet egy erőforrást.

Új erőforrások létrehozásához adja hozzá a következő **CreateCustomResource** metódust:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
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

A **CreateCustomResource** metódus frissíti a bejövő kérelmet, hogy tartalmazza az Azure-specifikus mezők **azonosítója**, **neve**, és **írja be.** Ezek a mezők az Azure-beli szolgáltatások által használt legfelső szintű tulajdonságok. Lehetővé teszik, hogy az egyéni szolgáltató együttműködjön más szolgáltatásokkal, például az Azure Policy,Azure Resource Manager templates és az Azure Activity Log.

Tulajdonság | Példa | Leírás
---|---|---
**név** | {myCustomResourceName} | Az egyéni erőforrás neve
**Típus** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | Az erőforrástípusú névtér
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | Az erőforrás azonosítója

A tulajdonságok hozzáadása mellett a JSON-dokumentumot is mentette az Azure Table storage-ba.

### <a name="retrieve-a-custom-resource"></a>Egyéni erőforrás beolvasása

Egyéni szolgáltatók esetében az egyéni erőforrás lekérése a GET-kérelmeken keresztül történik. Az egyéni szolgáltató *nem* fogadja el a JSON-kérelem törzsét. Get kérelmek esetén a végpont `x-ms-customproviders-requestpath` a fejléc et használja a már létrehozott erőforrás visszaadására.

Adja hozzá a következő **RetrieveCustomResource** metódust a meglévő erőforrások lekéréséhez:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
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

Az Azure-ban az erőforrások egy RESTful modellt követnek. Az erőforrást létrehozó kérelem URL-címe is visszaadja az erőforrást, ha get kérést hajt végre.

### <a name="remove-a-custom-resource"></a>Egyéni erőforrás eltávolítása

Egyéni szolgáltatók esetében az egyéni erőforrás törlődik a DELETE kérelmeken keresztül. Az egyéni szolgáltató *nem* fogadja el a JSON-kérelem törzsét. DELETE kérelem esetén a végpont `x-ms-customproviders-requestpath` a fejléc et használja a már létrehozott erőforrás törléséhez.

A meglévő erőforrások eltávolításához adja hozzá a következő **RemoveCustomResource** metódust:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
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

Az Azure-ban az erőforrások egy RESTful modellt követnek. Az erőforrást létrehozó kérelem URL-címe törli az erőforrást is, ha DELETE kérést hajt végre.

### <a name="list-all-custom-resources"></a>Az összes egyéni erőforrás listázása

Egyéni szolgáltatók esetén a gyűjteménygetési kérelmek használatával számba veregetheti a meglévő egyéni erőforrások listáját. Az egyéni szolgáltató *nem* fogadja el a JSON-kérelem törzsét. Get-kérelmek gyűjteménye esetén a végpont `x-ms-customproviders-requestpath` a fejléc segítségével számba veszi a már létrehozott erőforrásokat.

Adja hozzá a következő **EnumerateAllCustomResources** metódust a meglévő erőforrások számbavételéhez:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
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
> A RowKey QueryComparisons.GreaterThan és QueryComparisons.LessThan az Azure Table storage szintaxisa a karakterláncok "startswith" lekérdezésének végrehajtásához.

Az összes meglévő erőforrások listázásához hozzon létre egy Azure Table storage-lekérdezést, amely biztosítja, hogy az erőforrások léteznek az egyéni szolgáltató partícióalatt. A lekérdezés ezután ellenőrzi, hogy `{myResourceType}` a sorkulcs ugyanazzal az értékkel kezdődik-e.

## <a name="integrate-restful-operations"></a>ReSTful műveletek integrálása

Miután az összes RESTful metódus hozzá van adva a függvényalkalmazáshoz, frissítse a fő **Futtatás** metódust, amely meghívja a függvényeket a különböző REST-kérelmek kezeléséhez:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
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
        // Action request for a custom action.
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

A frissített **futtatási** metódus most már tartalmazza az Azure Table storage-hoz hozzáadott *tableStorage* bemeneti kötést. A metódus első része `x-ms-customproviders-requestpath` beolvassa a `Microsoft.Azure.Management.ResourceManager.Fluent` fejlécet, és a tár segítségével értelmezi az értéket erőforrás-azonosítóként. A `x-ms-customproviders-requestpath` fejlécet az egyéni szolgáltató küldi el, és megadja a bejövő kérelem elérési útját.

Az elemzött erőforrás-azonosító használatával létrehozhatja a **partíciókulcs** és **a rowKey** értékeket az adatok keresnek, vagy egyéni erőforrások tárolására.

A metódusok és osztályok hozzáadása után frissítenie kell a függvényalkalmazás **metódusait.** Adja hozzá a következő kódot a C# fájl tetejéhez:

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

Ha eltéved az oktatóanyag bármely pontján, megtalálhatja a teljes kódmintát az [egyéni C# RESTful végponthivatkozásban.](./reference-custom-providers-csharp-endpoint.md) Miután befejezte a függvényalkalmazást, mentse a függvényalkalmazás URL-címét. Ezt fel lehet használni, hogy indítsa el a függvény alkalmazást a későbbi oktatóanyagok.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy RESTful végpontot készített egy Azure-beli egyéni szolgáltatói végponthoz. Ha meg szeretné tudni, hogyan hozhat létre egyéni szolgáltatót, olvassa el az [Oktatóanyag: Egyéni szolgáltató létrehozása](./tutorial-custom-providers-create.md)című cikket.
