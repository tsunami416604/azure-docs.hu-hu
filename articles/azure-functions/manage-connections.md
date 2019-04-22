---
title: Az Azure Functions kapcsolatok kezelése
description: Megtanulhatja, hogyan teljesítményproblémákat okozhat az Azure Functions ügyfelek statikus kapcsolat használatával.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 4e9bd4e9ea467446c2814cdb8956a40b1503b027
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784925"
---
# <a name="manage-connections-in-azure-functions"></a>Az Azure Functions kapcsolatok kezelése

A függvényalkalmazás függvénye ossza meg erőforrásait. Megosztott erőforrások közötti kapcsolatok a következők: HTTP-kapcsolatokat, adatbázis-kapcsolatok és például az Azure Storage-szolgáltatásokhoz való kapcsolatot. Számos függvényt egy időben futnak, esetén lehetséges, hogy elfogy a rendelkezésre álló kapcsolatok. Ez a cikk bemutatja, hogyan lehet, kerülje a több kapcsolat van szükségük, mint a functions-kód.

## <a name="connection-limit"></a>Kapcsolathoz megadott korlátot

A rendelkezésre álló kapcsolatok száma korlátozva, részben mivel a függvényalkalmazás egy [próbakörnyezetben](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). A védőfal ró a kód korlátozások egyike egy [korlát (jelenleg a 600 aktív kapcsolatok és 1200 kapcsolatainak száma összesen) kapcsolatok száma](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) példányonként. Ha eléri a korlátot, a functions futtatókörnyezete létrehoz egy naplófájlt a következő üzenettel: `Host thresholds exceeded: Connections`.

Ezt a határt egy példány van.  Ha a [méretezési vezérlő hozzáad függvény alkalmazáspéldány](functions-scale.md#how-the-consumption-and-premium-plans-work) további kérések kezelésére, mindegyik példány rendelkezik egy független kapcsolathoz megadott korlátot. Azt jelenti, hogy a nem globális kapcsolat korlátozott, és sokkal több mint 600 aktív kapcsolatok is rendelkezik az összes aktív példányok között.

Amikor hibaelhárítás, győződjön meg arról, hogy a függvényalkalmazás számára engedélyezte az Application Insights. Az Application Insights lehetővé teszi a funkció alkalmazásokhoz, például végrehajtások metrikákat tekinthet meg. További információkért lásd: [telemetriai adatok megtekintése az Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statikus ügyfelek

Rendelkezés a szükségesnél több kapcsolatot elkerüléséhez ügyfélpéldányok helyett az egyes függvény meghívási újakat hozna létre újból. Azt javasoljuk, hogy szakember újból felhasználja az ügyfélkapcsolatokat, bármely nyelven, előfordulhat, hogy a függvény az írást. Ha például a .NET-ügyfelek például a [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), és az Azure Storage-ügyfelek kapcsolatok kezelése egyetlen, statikus ügyfél használatakor.

Az alábbiakban néhány irányelv szükséges a következő szolgáltatásspecifikus ügyfél használ az Azure Functions-alkalmazások:

- *Ne* hozzon létre egy új ügyfél minden függvény meghívási együtt.
- *Tegye* hozzon létre statikus ügyfél, amely minden függvény meghívási használhatja.
- *Érdemes lehet* statikus ügyfél létrehozása a megosztott segítőosztály, ha a különböző függvényeket használja ugyanazt a szolgáltatást.

## <a name="client-code-examples"></a>Ügyfél hitelesítésikód-példák

Ez a szakasz azt ismerteti, létrehozása és használata az ügyfelek a függvénykódot egy ajánlott eljárásai.

### <a name="httpclient-example-c"></a>Példa HttpClient (C#)

Íme egy példa C# kódot, amely létrehozza egy statikus függvényt [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) példány:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Általános kérdése [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) a .NET-ben a "Kell I tud megszabadulni fejlesztek?" Általában az objektumok megvalósító eldobásakor `IDisposable` befejezése használja őket. Meg nem dobható el a statikus ügyfél, mert nem kész, de amikor befejeződik a függvényt használja. Azt szeretné, hogy a statikus ügyfél élő az alkalmazás időtartamára.

### <a name="http-agent-examples-javascript"></a>HTTP-ügynök példák (JavaScript)

Jobb kapcsolat felügyeleti lehetőségeket biztosít, mivel a natív használata javasolt [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) osztály helyett nem natív módszerek, például a `node-fetch` modul. A lehetőségek között vannak konfigurálva a kapcsolat paramétereit a `http.agent` osztály. A részletes lehetőségekről elérhető HTTP-ügynökkel rendelkező [új ügynök (\[beállítások\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

A globális `http.globalAgent` osztály által használt `http.request()` összes ezeket az értékeket saját értékeit. Az ajánlott módszer a kapcsolat korlátai funkciók konfigurálására, hogy globálisan állítson be maximális számot. Az alábbi példa állítja be a függvényalkalmazás sockets maximális száma:

```js
http.globalAgent.maxSockets = 200;
```

 Az alábbi példa egy egyéni HTTP-ügynök csak az adott kérelmet hoz létre egy új HTTP-kérelem:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient példakód (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) egy Azure Cosmos DB-példányhoz csatlakozik. Az Azure Cosmos DB-dokumentáció azt javasolja, hogy Ön [egyedülálló Azure Cosmos DB-ügyfél használata az alkalmazás teljes élettartama](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Az alábbi példa bemutatja egy függvény adatelemzésre, amely egy minta:

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

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient példakód (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) egy Azure Cosmos DB-példányhoz csatlakozik. Az Azure Cosmos DB-dokumentáció azt javasolja, hogy Ön [egyedülálló Azure Cosmos DB-ügyfél használata az alkalmazás teljes élettartama](../cosmos-db/performance-tips.md#sdk-usage). Az alábbi példa bemutatja egy függvény adatelemzésre, amely egy minta:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient kapcsolatok

A függvénykód használhatja a .NET-keretrendszer adatszolgáltatója az SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) relációs SQL-adatbázishoz való csatlakozást. Ez egyben az alapul szolgáló szolgáltató által használt, ADO.NET, például a data-keretrendszerek [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Ellentétben [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) és [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) kapcsolatkészletezést alapértelmezés szerint valósítja meg az ADO.NET-kapcsolatok esetén. De kívül kapcsolatok továbbra is futtathatja, mert az adatbázishoz való csatlakozás, optimalizálja. További információkért lásd: [SQL Server készletezési (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Bizonyos adatok keretrendszerek, például Entity Framework, általában a kapcsolati karakterláncok beolvasása a **kapcsolati Sztringjei** szakasz egy konfigurációs fájl. Ebben az esetben explicit módon kell hozzáadnia az SQL adatbázis-kapcsolati karakterláncok a **kapcsolati karakterláncok** a függvényalkalmazás-beállításokat, majd a gyűjteményt a [local.settings.json fájljában](functions-run-local.md#local-settings-file) a helyi projektben. Ha hoz létre egy példányát [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) a függvénykódban, tárolja a kapcsolati karakterlánc értékét a **Alkalmazásbeállítások** a kapcsolatokkal.

## <a name="next-steps"></a>További lépések

Miért javasoljuk, hogy az ügyfelek statikus kapcsolatos további információkért lásd: [nem megfelelő példányosítás kizárási minta](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

További Azure Functions teljesítményének tippek: [teljesítményének és megbízhatóságának az Azure Functions optimalizálása](functions-best-practices.md).
