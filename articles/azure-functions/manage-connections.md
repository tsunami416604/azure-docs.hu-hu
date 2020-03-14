---
title: Kapcsolatok kezelése Azure Functionsban
description: Megtudhatja, hogyan kerülheti el a teljesítménnyel kapcsolatos problémákat a Azure Functions a statikus kapcsolódási ügyfelek használatával.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276451"
---
# <a name="manage-connections-in-azure-functions"></a>Kapcsolatok kezelése Azure Functionsban

Functions-alkalmazásokban lévő függvények erőforrásainak megosztása. A megosztott erőforrások közé tartoznak a kapcsolatok: HTTP-kapcsolatok, adatbázis-kapcsolatok, valamint olyan szolgáltatásokhoz való kapcsolódások, mint például az Azure Storage. Ha egyszerre több függvény fut, lehetséges, hogy elfogynak az elérhető kapcsolatok. Ez a cikk ismerteti a függvények kódolását, hogy elkerülje a szükségesnél több kapcsolat használatát.

## <a name="connection-limit"></a>Kapcsolatok korlátja

A rendelkezésre álló kapcsolatok száma részben korlátozott, mert egy Function alkalmazás egy sandbox- [környezetben](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)fut. Az egyik korlátozás, amelyet a homokozó a kódban kiszab, korlátozza a kimenő kapcsolatok számát, amely jelenleg 600 aktív (1 200 összesen) kapcsolatra vonatkozik. Ha eléri ezt a korlátot, a functions Runtime a következő üzenetet írja a naplókba: `Host thresholds exceeded: Connections`. További információt a [functions szolgáltatás korlátai](functions-scale.md#service-limits)című témakörben talál.

Ez a korlát/példány. Ha a [méretezési vezérlő](functions-scale.md#how-the-consumption-and-premium-plans-work) felvette a több kérés kezelésére szolgáló Function app-példányokat, minden példányhoz tartozik egy független kapcsolódási korlát. Ez azt jelenti, hogy nincs globális kapcsolati korlát, és az összes aktív példányon több mint 600 aktív kapcsolat lehet.

Hibaelhárítás esetén győződjön meg arról, hogy engedélyezte a Application Insights használatát a Function alkalmazáshoz. Application Insights segítségével megtekintheti a függvények alkalmazásainak (például a végrehajtások) mérőszámait. További információ: [telemetria megtekintése Application Insightsban](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statikus ügyfelek

Ha el szeretné kerülni, hogy a szükségesnél több kapcsolat ne legyen használatban, ne hozzon létre újakat az egyes függvények meghívásával. Javasoljuk, hogy az ügyfélkapcsolatokat minden olyan nyelven újra használja, amelyet a függvényének megírásával használhat. Például a .NET-ügyfelek, például a [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), a [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)és az Azure Storage-ügyfelek kezelhetik a kapcsolatokat, ha egyetlen, statikus ügyfelet használ.

Az alábbiakban néhány útmutatást talál, ha a szolgáltatásra jellemző ügyfelet használ egy Azure Functions alkalmazásban:

- *Ne hozzon* létre új ügyfelet minden függvény meghívásával.
- *Hozzon* létre egyetlen, statikus ügyfelet, amelyet minden funkció használhat.
- Ha a különböző függvények ugyanazt a szolgáltatást használják, *érdemes lehet* egyetlen, statikus ügyfelet létrehozni egy megosztott segítő osztályban.

## <a name="client-code-examples"></a>Példák az ügyfél kódjára

Ez a szakasz az ügyfelek a függvény kódjából történő létrehozásához és használatához ajánlott eljárásokat mutatja be.

### <a name="httpclient-example-c"></a>HttpClient példa (C#)

Az alábbi példa egy statikus C# [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) -példányt létrehozó függvény kódját:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

A .NET-beli [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) kapcsolatos gyakori kérdés, hogy "érdemes megsemmisíteni az ügyfelem?" Általánosságban elmondható, hogy olyan objektumokat távolít el, amelyek a használat során `IDisposable` implementálják. Azonban nem távolítja el a statikus ügyfelet, mert a függvény befejeződése után nem használja. Azt szeretné, hogy a statikus ügyfél az alkalmazás időtartamára éljünk.

### <a name="http-agent-examples-javascript"></a>HTTP-ügynökre vonatkozó példák (JavaScript)

Mivel jobb ügyfélkapcsolat-kezelési lehetőségeket biztosít, a natív [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) osztályt kell használnia a nem natív metódusok helyett, például a `node-fetch` modult. A kapcsolatok paramétereinek beállítása a `http.agent` osztály beállításain keresztül történik. A HTTP-ügynökkel elérhető részletes beállításokért lásd: [új ügynök (\[beállítások\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

A `http.request()` által használt globális `http.globalAgent` osztály mindegyik értéke a megfelelő alapértelmezett értékre van állítva. A függvényekben a kapcsolatok korlátainak konfigurálásához ajánlott módszer a maximális szám megadása globálisan. A következő példa a függvény alkalmazáshoz tartozó szoftvercsatornák maximális számát állítja be:

```js
http.globalAgent.maxSockets = 200;
```

 Az alábbi példa egy új HTTP-kérést hoz létre egyéni HTTP-ügynökkel a kérelemhez:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient – példa (C#)

A [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) egy Azure Cosmos db-példányhoz csatlakozik. Az Azure Cosmos DB dokumentációja azt javasolja, hogy [az alkalmazás élettartama során egy egyedi Azure Cosmos db ügyfelet használjon](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Az alábbi példa egy függvényt ábrázol egy mintát:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient – példa (JavaScript)
A [CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) egy Azure Cosmos db-példányhoz csatlakozik. Az Azure Cosmos DB dokumentációja azt javasolja, hogy [az alkalmazás élettartama során egy egyedi Azure Cosmos db ügyfelet használjon](../cosmos-db/performance-tips.md#sdk-usage). Az alábbi példa egy függvényt ábrázol egy mintát:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient-kapcsolatok

A függvény kódja a .NET-keretrendszer adatszolgáltatóját használhatja SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) számára, hogy kapcsolatot hozzon egy SQL-alapú kapcsolati adatbázissal. Ez a ADO.NET alapuló adatkeretrendszerek alapjául szolgáló szolgáltató is, például [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). A [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) és a [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) kapcsolattól eltérően a ADO.NET alapértelmezés szerint implementálja a kapcsolatok készletezését. Mivel azonban továbbra is kifogyhat a kapcsolatok, érdemes optimalizálni a kapcsolatokat az adatbázissal. További információ: [SQL Server kapcsolatok készletezése (ADO.net)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Egyes adatkeretrendszerek (például a Entity Framework) általában a konfigurációs fájl **ConnectionStrings** szakaszából kapják meg a kapcsolatok karakterláncait. Ebben az esetben explicit módon fel kell vennie az SQL Database-kapcsolódási karakterláncokat a Function app-beállítások és a helyi projekt [Local. Settings. JSON fájljának](functions-run-local.md#local-settings-file) **kapcsolatok karakterlánc** -gyűjteményéből. Ha a [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) egy példányát hozza létre, a kapcsolati sztring értékét a többi kapcsolattal együtt kell tárolnia az **Alkalmazásbeállítások** között.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy a statikus ügyfelek miért javasoltak: [helytelen példány-létrehozási minta](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

További Azure Functions teljesítménnyel kapcsolatos tippeket a [Azure functions teljesítményének és megbízhatóságának optimalizálása](functions-best-practices.md)című témakörben talál.
