---
title: Kapcsolatok kezelése az Azure Functionsben
description: Ismerje meg, hogyan kerülheti el a teljesítményproblémákat az Azure Functionsben statikus kapcsolati ügyfelek használatával.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276451"
---
# <a name="manage-connections-in-azure-functions"></a>Kapcsolatok kezelése az Azure Functionsben

Függvények egy függvényalkalmazásban erőforrásokat osztanak meg. Ezek közé a megosztott erőforrások közé tartoznak a kapcsolatok: HTTP-kapcsolatok, adatbázis-kapcsolatok és a szolgáltatásokhoz, például az Azure Storage-hoz való kapcsolatok. Ha számos függvény fut egyidejűleg, lehetséges, hogy elfogy a rendelkezésre álló kapcsolatok. Ez a cikk bemutatja, hogyan kódolhatja a függvényeket, hogy ne használjon több kapcsolatot, mint amire szükségük van.

## <a name="connection-limit"></a>Kapcsolati korlát

A rendelkezésre álló kapcsolatok száma részben azért korlátozott, mert egy függvényalkalmazás [sandbox környezetben](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)fut. A kódra vonatkozó korlátozások egyike a kimenő kapcsolatok számának korlátozása, amely jelenleg 600 aktív (összesen 1200) kapcsolat példányonként. Ha eléri ezt a korlátot, a függvények futásidejű `Host thresholds exceeded: Connections`írja a következő üzenetet a naplók: . További információt a [Functions szolgáltatás korlátai](functions-scale.md#service-limits)című témakörben talál.

Ez a korlát példányonként. Amikor a méretezési vezérlő további kérelmek kezeléséhez adja hozzá a [függvényalkalmazás-példányokat,](functions-scale.md#how-the-consumption-and-premium-plans-work) minden példány nak van egy független kapcsolati korlátja. Ez azt jelenti, hogy nincs globális kapcsolati korlát, és sokkal több, mint 600 aktív kapcsolat tal rendelkezhet az összes aktív példányon.

Hibaelhárításkor győződjön meg arról, hogy engedélyezte az Application Insights a függvényalkalmazáshoz. Az Application Insights lehetővé teszi a függvényalkalmazások, például a végrehajtások metrikák megtekintését. További információ: [Telemetriai adatok megtekintése az Application Insightsban.](functions-monitoring.md#view-telemetry-in-application-insights)  

## <a name="static-clients"></a>Statikus ügyfelek

Annak elkerülése érdekében, hogy a szükségesnél több kapcsolatot tartson fenn, használja fel újra az ügyfélpéldányokat ahelyett, hogy újakat hozna létre az egyes függvénymeghívásokkal. Azt javasoljuk, hogy használja fel újra az ügyfélkapcsolatokat minden olyan nyelven, amelybe a függvényt beírhatja. Például a .NET-ügyfelek, például a [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)és az Azure Storage-ügyfelek kezelhetik a kapcsolatokat, ha egyetlen statikus ügyfelet használ.

Az alábbiakban néhány irányelvet kell követnie, ha egy Azure Functions-alkalmazásban szolgáltatásspecifikus ügyfelet használ:

- *Ne* hozzon létre új ügyfelet minden függvénymeghívással.
- *Hozzon* létre egy statikus ügyfelet, amelyet minden függvény meghívása használhat.
- *Fontolja meg* egy közös segítő osztályegyetlen statikus ügyfél létrehozását, ha különböző funkciók ugyanazt a szolgáltatást használják.

## <a name="client-code-examples"></a>Példák ügyfélkódra

Ez a szakasz bemutatja az ügyfelek létrehozásának és használatának ajánlott a függvénykódból történő létrehozásához és használatához.

### <a name="httpclient-example-c"></a>HttpClient példa (C#)

Íme egy példa a C# függvénykódra, amely statikus [HttpClient-példányt](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) hoz létre:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

A .NET rendszerben a [HttpClient használatával](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) kapcsolatban gyakori kérdés a "Meg kell-e szabadulnom az ügyfelemtől?" Általában, akkor dobja az `IDisposable` objektumokat, amelyek végre, ha befejezte a használatát. De nem dobja a statikus ügyfél, mert még nem végzett a használata, amikor a funkció véget ér. Azt szeretné, hogy a statikus ügyfél az alkalmazás időtartama alatt éljen.

### <a name="http-agent-examples-javascript"></a>Http-ügynökpéldák (JavaScript)

Mivel jobb kapcsolatkezelési beállításokat biztosít, [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) a natív osztályt kell használnia a nem natív módszerek helyett, például a `node-fetch` modult. A kapcsolatparaméterei az `http.agent` osztály beállításaival vannak konfigurálva. A HTTP-ügynökkel elérhető részletes beállításokat az [Új ügynök(\[beállítások\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)című témakörben található.

A `http.globalAgent` globális osztály `http.request()` által használt rendelkezik az összes ilyen értékek a megfelelő alapértelmezett. A functionsben a kapcsolatkorlátok konfigurálásának ajánlott módja a maximális szám globális beállítása. A következő példa a függvényalkalmazás szoftvercsatornáinak maximális számát állítja be:

```js
http.globalAgent.maxSockets = 200;
```

 A következő példa új HTTP-kérelmet hoz létre csak az adott kérelemhez egyéni HTTP-ügynökkel:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Példa a DocumentClient kódjára (C#)

[A DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) egy Azure Cosmos DB-példányhoz csatlakozik. Az Azure Cosmos DB dokumentációja azt javasolja, hogy [az alkalmazás teljes élettartama alatt használjon egy singleton Azure Cosmos DB-ügyfelet.](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage) A következő példa egy függvényben végzett példa:

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

### <a name="cosmosclient-code-example-javascript"></a>Példa cosmosClient-kódra (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) csatlakozik egy Azure Cosmos DB-példány. Az Azure Cosmos DB dokumentációja azt javasolja, hogy [az alkalmazás teljes élettartama alatt használjon egy singleton Azure Cosmos DB-ügyfelet.](../cosmos-db/performance-tips.md#sdk-usage) A következő példa egy függvényben végzett példa:

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

A függvénykód a .[SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) Ez az alapul szolgáló szolgáltató jaint a ADO.NET támaszkodó adatkeretek, például [az Entitás keretrendszer.](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) A [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) és [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) kapcsolatokkal ellentétben ADO.NET alapértelmezés szerint a kapcsolatkészletezést valósítja meg. Mivel azonban továbbra is kifogyhatnak a kapcsolatokból, optimalizálnia kell az adatbázissal létesített kapcsolatokat. További információt az [SQL Server kapcsolatkészletezés (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)című témakörben talál.

> [!TIP]
> Egyes adatkeretrendszerek, például az entitáskeretrendszer, általában egy konfigurációs fájl **ConnectionStrings** szakaszából kapnak kapcsolati karakterláncokat. Ebben az esetben explicit módon hozzá kell adnia az SQL-adatbázis kapcsolati karakterláncait a függvényalkalmazás-beállítások **kapcsolati karakterláncok** gyűjteményéhez és a [helyi.settings.json fájlhoz](functions-run-local.md#local-settings-file) a helyi projektben. Ha az [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) egy példányát hozza létre a függvénykódban, a kapcsolati karakterlánc értékét a többi kapcsolatalkalmazás-beállításokban kell tárolnia. **Application settings**

## <a name="next-steps"></a>További lépések

További információ arról, hogy miért javasoljuk a statikus ügyfeleket, [lásd: Nem megfelelő példányosítási antipattern](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

További Azure Functions-teljesítménytippekért [olvassa el Az Azure Functions teljesítményének és megbízhatóságának optimalizálása című témakört.](functions-best-practices.md)
