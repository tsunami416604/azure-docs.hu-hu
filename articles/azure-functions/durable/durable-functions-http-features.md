---
title: HTTP-funkciók a tartós funkciókban – Azure-funkciók
description: Ismerje meg az integrált HTTP-funkciókat az Azure Functions tartós funkciók bővítményében.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 29d837446960b7535b26284efdfab7a1c59ea968
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132498"
---
# <a name="http-features"></a>HTTP-szolgáltatások

A Durable Functions számos olyan funkcióval rendelkezik, amelyek megkönnyítik a tartós vezénylések és entitások HTTP-munkafolyamatokba való beépítését. Ez a cikk részletesen ismerteti ezeket a funkciókat.

## <a name="exposing-http-apis"></a>HTTP API-k felfedése

Vezénylések és entitások http-kérelmek használatával hívható és kezelhető. A Durable Functions bővítmény beépített HTTP API-kat ad elérhetővé. Emellett API-kat is biztosít a vezénylések és entitások http-aktivált függvényeken belüli kölcsönhatásban.

### <a name="built-in-http-apis"></a>Beépített HTTP API-k

A Durable Functions bővítmény automatikusan hozzáadja a HTTP API-k egy készletét az Azure Functions gazdagéphez. Ezekkel az API-kkal bármilyen kód írása nélkül kommunikálhat vezénylésekkel és entitásokkal.

A következő beépített HTTP API-k támogatottak.

* [Új vezénylési indítás](durable-functions-http-api.md#start-orchestration)
* [Lekérdezés vezénylési példánya](durable-functions-http-api.md#get-instance-status)
* [Vezénylési példány leállítása](durable-functions-http-api.md#terminate-instance)
* [Külső esemény küldése vezénylési eseményre](durable-functions-http-api.md#raise-event)
* [Vezénylési előzmények törlése](durable-functions-http-api.md#purge-single-instance-history)
* [Műveletesemény küldése entitásnak](durable-functions-http-api.md#signal-entity)
* [Entitás állapotának beszereznie](durable-functions-http-api.md#get-entity)
* [Entitások listájának lekérdezése](durable-functions-http-api.md#list-entities)

Tekintse meg a [HTTP API-k cikkteljes](durable-functions-http-api.md) leírását a tartós függvények bővítmény által elérhetővé tett összes beépített HTTP API-k.

### <a name="http-api-url-discovery"></a>HTTP API URL-felderítése

A [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client) elérhetővé teszi az API-kat, amelyek kényelmes HTTP-válasz hasznos adatokat generálhatnak. Létrehozhat például egy adott vezénylési példány felügyeleti API-kra mutató hivatkozásokat tartalmazó választ. A következő példák egy HTTP-trigger függvényt mutatnak be, amely bemutatja, hogyan használhatja ezt az API-t egy új vezénylési példányhoz:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

Egy orchestrator függvény indítása a korábban bemutatott HTTP-trigger függvények használatával bármely HTTP-ügyfél használatával elvégezhető. A következő cURL parancs elindít `DoWork`egy orchestrator függvényt, melynek neve:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Következő egy példa válasz egy vezénylési, amely az `abc123` azonosítója. Néhány részletet eltávolítottunk az egyértelműség érdekében.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Az előző példában minden egyes `Uri` mező, amely egy beépített HTTP API-nak felel meg. Ezekkel az API-khasználatával kezelheti a cél vezénylési példány.

> [!NOTE]
> A webhook URL-címek formátuma attól függ, hogy az Azure Functions gazdagép melyik verzióját futtatja. Az előző példa az Azure Functions 2.0-s gazdagép.

Az összes beépített HTTP API leírását a [HTTP API-k hivatkozási száma](durable-functions-http-api.md)tartalmazza.

### <a name="async-operation-tracking"></a>Aszinkron művelet nyomon követése

A korábban említett HTTP-válasz célja, hogy segítsen megvalósítani a hosszú ideig futó HTTP-aszinkron API-kat a tartós függvényekkel. Ezt a mintát néha *fogyasztói mintának is nevezik.* Az ügyfél/kiszolgáló folyamat a következőképpen működik:

1. Az ügyfél http-kérelmet ad ki egy hosszú ideig futó folyamat, például egy orchestrator függvény elindításához.
1. A cél HTTP-eseményindító http 202-es választ ad vissza egy "statusQueryGetUri" értékű Hely fejléccel.
1. Az ügyfél lekérdezi az URL-címet a Hely fejlécében. Az ügyfél továbbra is látja a HTTP 202 válaszokat egy helyfejléccel.
1. Ha a példány befejeződik vagy sikertelen, a Hely fejlécvégponthttp 200-at ad vissza.

Ez a protokoll lehetővé teszi a hosszú ideig futó folyamatok összehangolását olyan külső ügyfelekkel vagy szolgáltatásokkal, amelyek letudnak pollálni egy HTTP-végpontot, és követni tudják a Hely fejlécet. A minta ügyfél- és kiszolgálóimplementációi is be vannak építve a tartós függvények HTTP API-jaiba.

> [!NOTE]
> Alapértelmezés szerint az Azure Logic [Apps](https://azure.microsoft.com/services/logic-apps/) által biztosított összes HTTP-alapú művelet támogatja a szabványos aszinkron műveletmintát. Ez a képesség lehetővé teszi egy hosszú ideig futó tartós függvény beágyazását a Logic Apps munkafolyamat részeként. Az [Azure Logic Apps munkafolyamat-műveleteiben és a dokumentációban](../../logic-apps/logic-apps-workflow-actions-triggers.md)további részleteket talál az aszinkron HTTP-minták támogatásáról.

> [!NOTE]
> A vezénylési műveletekkel végzett interakciók bármely függvénytípusból, nem csak a HTTP-aktivált függvényekből végezhetők el.

A vezénylések és az entitások ügyfélAPI-k használatával történő kezeléséről a [Példánykezelés című cikkben](durable-functions-instance-management.md)olvashat bővebben.

## <a name="consuming-http-apis"></a>HTTP API-k fogyasztása

Ahogy azt az [orchestrator függvénykód-megkötések,](durable-functions-code-constraints.md)orchestrator függvények nem tudja az I/O közvetlenül. Ehelyett általában olyan [tevékenységfüggvényeket](durable-functions-types-features-overview.md#activity-functions) hívnak meg, amelyek I/O-műveleteket végeznek.

A Durable Functions 2.0-s verziójával kezdve a vezénylési műveletek natív módon felszámíthatják a HTTP API-kat a [vezénylési eseményindító kötés](durable-functions-bindings.md#orchestration-trigger)használatával.

A következő példakód egy kimenő HTTP-kérelmet készítő orchestrator függvényt jelenít meg:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

A "HTTP hívása" művelettel a következő műveleteket teheti meg az orchestrator függvényekben:

* A HTTP API-k at közvetlenül a vezénylési függvényekből hívhatja meg, később említett bizonyos korlátozásokkal.
* Automatikusan támogatja az ügyféloldali HTTP 202 állapotlekérdezési mintákat.
* Az [Azure felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) használatával engedélyezett HTTP-hívásokat kezdeményezhet más Azure-végpontokra.

A HTTP API-k közvetlenül az orchestrator függvények használatával a közös forgatókönyvek bizonyos készletének kényelmét szánja. Ezeket a funkciókat saját maga valósíthatja meg a tevékenységfüggvények használatával. Sok esetben a tevékenységfüggvények nagyobb rugalmasságot biztosíthatnak.

### <a name="http-202-handling"></a>HTTP 202 kezelés

A "hívás HTTP" API automatikusan megvalósíthatja a lekérdezési fogyasztói minta ügyféloldalát. Ha egy nevű API egy Hely fejléccel rendelkező HTTP 202 választ ad vissza, az orchestrator függvény automatikusan lekérdezi a Hely erőforrást, amíg nem 202-től eltérő választ nem kap. Ez a válasz lesz az orchestrator függvénykódnak visszaadott válasz.

> [!NOTE]
> Az Orchestrator függvényei natív módon támogatják a kiszolgálóoldali lekérdezési fogyasztói mintát is, ahogy azt az [Async műveletkövetés ismerteti.](#async-operation-tracking) Ez a támogatás azt jelenti, hogy az egyik függvényalkalmazásban a vezénylések könnyen koordinálhatják az orchestrator-függvényeket más függvényalkalmazásokban. Ez hasonló az [alveződési](durable-functions-sub-orchestrations.md) koncepcióhoz, de támogatja az alkalmazások közötti kommunikációt. Ez a támogatás különösen hasznos a mikroszolgáltatás-stílusú alkalmazásfejlesztéshez.

### <a name="managed-identities"></a>Felügyelt identitások

A Durable Functions natív módon támogatja az Azure Active Directory (Azure AD) jogkivonatokat engedélyező API-khívásait. Ez a támogatás [azure felügyelt identitások segítségével](../../active-directory/managed-identities-azure-resources/overview.md) szerezze be ezeket a jogkivonatokat.

A következő kód egy . A függvény hitelesített hívásokat kezdeményez a virtuális gépek újraindításához az Azure Resource Manager [REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)használatával.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

Az előző példában `tokenSource` a paraméter úgy van konfigurálva, hogy az [Azure Resource Manager](../../azure-resource-manager/management/overview.md)számára azure AD-tokeneket szerezzen be. A jogkivonatokat az erőforrás `https://management.core.windows.net`URI-ja azonosítja. A példa feltételezi, hogy az aktuális függvényalkalmazás vagy helyileg fut, vagy felügyelt identitású függvényalkalmazásként lett telepítve. A helyi identitás vagy a felügyelt identitás a megadott erőforráscsoportban `myRG`lévő virtuális gépek kezeléséhez szükséges engedéllyel rendelkezik.

Futásidőben a konfigurált jogkivonat-forrás automatikusan egy OAuth 2.0-s hozzáférési jogkivonatot ad vissza. A forrás ezután hozzáadja a jogkivonatot tulajdonosi jogkivonatként a kimenő kérelem engedélyezési fejlécéhez. Ez a modell a következő okok miatt tovább javítja az engedélyezési fejlécek HTTP-kérelmekhez való manuális hozzáadását:

* A tokenfrissítés kezelése automatikusan történik. Nem kell aggódnia a lejárt tokenek miatt.
* Jogkivonatokat soha nem tárolja a tartós vezénylési állapotban.
* A tokenek beszerzésének kezeléséhez nem kell kódot írnia.

Az [előre lefordított C# RestartVMs mintában](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)találhat egy teljesebb példát.

A felügyelt identitások nem korlátozódnak az Azure erőforrás-kezelésére. A felügyelt identitások segítségével hozzáférhet bármely API-hoz, amely elfogadja az Azure AD tulajdonosi jogkivonatokat, beleértve a Microsoft Azure-szolgáltatásait és a partnerek től származó webalkalmazásokat. A partner webalkalmazása akár egy másik függvényalkalmazás is lehet. Az Azure AD-hitelesítést támogató Microsoft Azure-szolgáltatások listáját az [Azure AD-hitelesítést támogató Azure-szolgáltatások](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)című témakörben található.

### <a name="limitations"></a>Korlátozások

A HTTP API-k hívásának beépített támogatása kényelmi szolgáltatás. Nem minden forgatókönyvesetén megfelelő.

Az orchestrator-függvények által küldött HTTP-kérelmek és azok válaszai szerializáltak és állandóak várólista-üzenetekként. Ez a várakozási idő biztosítja, hogy a HTTP-hívások [megbízhatóak és biztonságosak legyenek a vezénylési visszajátszáshoz.](durable-functions-orchestrations.md#reliability) A sorban állási viselkedésnek azonban vannak korlátai is:

* Minden HTTP-kérelem további késést tartalmaz egy natív HTTP-ügyfélhez képest.
* A várólistaüzenetekbe nem férő nagykérési vagy válaszüzenetek jelentősen csökkenthetik a vezénylési teljesítményt. Az üzenet hasznos tárhelyének blobtárolóba történő kiszervezésének többletterhelése teljesítménycsökkenést okozhat.
* Streamelés, adattömbök és bináris hasznos adatnem támogatott.
* A HTTP-ügyfél viselkedésének testreszabása korlátozott.

Ha a korlátozások bármelyike hatással lehet a használati esetre, fontolja meg inkább tevékenységfüggvények és nyelvspecifikus HTTP-ügyfélkódtárak használatával kimenő HTTP-hívások kezdeményezéséhez.

> [!NOTE]
> Ha Ön .NET fejlesztő, talán csodálkozhat, hogy ez a szolgáltatás miért használja a **DurableHttpRequest** és **durableHttpResponse** típusokat a beépített .NET **HttpRequestMessage** és **HttpResponseMessage** típusok helyett.
>
> Ez a tervezési választás szándékos. Ennek elsődleges oka az, hogy az egyéni típusok segítenek biztosítani, hogy a felhasználók ne tegyenek hibás feltételezéseket a belső HTTP-ügyfél támogatott viselkedésével kapcsolatban. A tartós függvényekre jellemző típusok lehetővé teszik az API-tervezés egyszerűsítését is. Ők is könnyebben elérhetővé speciális funkciók, mint a [felügyelt identitás integráció](#managed-identities) és a [közvélemény-kutatás fogyasztói minta](#http-202-handling). 

### <a name="extensibility-net-only"></a>Bővíthetőség (csak.NET)

Az [Azure Functions .NET függőségi injektálással](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection)testreszabhatoa a vezénylés belső HTTP-ügyféljének viselkedését. Ez a képesség hasznos lehet a kis viselkedési változások. Hasznos lehet a HTTP-ügyfél egységteszteléséhez is, mintaobjektumok befecskendezésével.

A következő példa bemutatja a függőségi injektálás használatával letiltani a TLS/SSL tanúsítvány érvényesítése vongazvófüggvények, amelyek meghívja a külső HTTP-végpontok.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a tartós entitásokról](durable-functions-entities.md)
