---
title: "Az Azure Functions HTTP és a webhook kötések |} Microsoft Docs"
description: "A HTTP és a webhook eseményindítók és kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, esemény feldolgozása, webhookokkal, dinamikus számítás-, kiszolgáló nélküli architektúra, HTTP, API REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.openlocfilehash: 3c3247592cbe2bc382d220264b0c646ee566b8a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Az Azure Functions HTTP és a webhook kötések
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk azt ismerteti, hogyan konfigurálását és a HTTP-eseményindítók és kötések az Azure Functions használatát.
Ezek segítségével az Azure Functions kiszolgáló nélküli API-k létrehozása, és webhookokkal reagálhat rájuk.

Az Azure Functions a következő kötéseket biztosít:
- Egy [HTTP-eseményindítóval](#httptrigger) lehetővé teszi, hogy a HTTP-kérelem a függvényt. A testre szabható válaszolni [webhookok](#hooktrigger).
- Egy [HTTP kimeneti kötése](#output) lehetővé teszi a válaszol a kérelemre.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>HTTP eseményindító
A HTTP-eseményindítóval végrehajtja a függvény egy HTTP-kérelemre adott válasz. Testre szabhatja, hogy egy adott URL-cím vagy HTTP-metódus válaszolni. Egy HTTP-eseményindítóval is beállítható úgy, hogy webhookok válaszolni. 

A funkciók portál használata esetén is elkezdheti rögtön az előre elkészített sablon használatával. Válassza ki **új függvény** és válassza az "API és Webhookokkal" lehetőséget a **forgatókönyv** legördülő menüből. Válassza ki a sablonok egyikét, és kattintson a **létrehozása**.

Alapértelmezés szerint HTTP-eseményindítóval válaszol egy HTTP 200 OK állapotot kódot és egy üres fő kérésre. A válasz módosításához konfigurálása egy [HTTP kimeneti kötése](#output)

### <a name="configuring-an-http-trigger"></a>Egy HTTP-eseményindítóval konfigurálása
Egy HTTP-eseményindítóval határozzák meg a JSON-objektum az az `bindings` tömbje function.json, a következő példában látható módon:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság  |Leírás  |
|---------|---------|
| **név** | Kötelező – a kérelem vagy kérelemtörzset függvény a kódban használt változó neve. Lásd: [egy HTTP-eseményindítóval kódból használata](#httptriggerusage). |
| **típusa** | Szükséges – kell állítani `httpTrigger`. |
| **iránya** | Szükséges – kell állítani `in`. |
| **authLevel** | Meghatározza, hogy mi kulcsok, ha van ilyen kell jelen lennie ahhoz, hogy a függvény meghívása a kérésre. Az érték a következő értékek egyike lehet: <ul><li><code>anonymous</code>&mdash;Nincs API-kulcsot meg kell adni.</li><li><code>function</code>&mdash;Funkcióspecifikus API-kulcs megadása kötelező. Ez az az alapértelmezett érték, ha nincs megadva.</li><li><code>admin</code>&mdash;A fő kulcsot meg kell adni.</li></ul> További információkért lásd: [kulcsoknál](#keys). |
| **módszerek** | A tömb, amelyre a függvény válaszol a HTTP-metódus. Ha nincs megadva, a függvény válaszol-e a HTTP-metódus. Lásd: [testreszabása a HTTP-végpont](#url). |
| **útvonal** | Meghatározza az útvonalsablonhoz szabályozása, amelyhez a kérés URL-címeket, a függvény válaszol. Az alapértelmezett érték, ha nincs megadva `<functionname>`. További információkért lásd: [testreszabása a HTTP-végpont](#url). |
| **webHookType** | Konfigurálja a HTTP-eseményindítóval egy webhook fogadójának a megadott szolgáltató nevében járhasson el. Ne használja a _módszerek_ tulajdonságot, ha ezt a beállítást használja. Az érték a következő értékek egyike lehet:<ul><li><code>genericJson</code>&mdash;Egy általános célú webhook végpont logika egy adott szolgáltató nélkül.</li><li><code>github</code>&mdash;A függvény válaszol-e a GitHub webhook. Ne használja a _authLevel_ tulajdonságot, ha ezt az értéket használja.</li><li><code>slack</code>&mdash;A függvény válaszol-e a Slack webhook. Ne használja a _authLevel_ tulajdonságot, ha ezt az értéket használja.</li></ul> További információkért lásd: [válaszol a webhookok](#hooktrigger). |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Egy HTTP-eseményindítóval kódból használata
C# és F # függvények, adjon meg lehet az eseményindító típusú deklarálhatnak `HttpRequestMessage` vagy egyéni .NET-típus. Ha úgy dönt, `HttpRequestMessage`, a request objektumon teljes hozzáférést kap. Egyéni .NET-típus funkciók megkísérli elemzése a JSON-kérés törzsében objektum tulajdonságainak beállításához. 

A Functions futtatókörnyezete Node.js funkciók, itt a kérés törzsében helyett a request objektumon. További információkért lásd: [HTTP-eseményindító minták](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>HTTP-válasz kimeneti kötése
Használja a HTTP kimeneti kötése a HTTP-kérést küldő válaszolni. A kötés egy HTTP-eseményindítóval igényel, és lehetővé teszi a válasz az eseményindító kérelemhez társított testreszabását. Ha HTTP kimeneti kötése van a nem Microsofttól származó, egy HTTP-eseményindítóval egy üres szövegtörzzsel HTTP 200 OK adja vissza. 

### <a name="configuring-an-http-output-binding"></a>HTTP konfigurálása kimeneti kötése
HTTP kimeneti kötése van definiálva egy JSON-objektum az az `bindings` tömbje function.json, a következő példában látható módon:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
A kötés támogatja-e a következő szükséges tulajdonságok:

|Tulajdonság  |Leírás  |
|---------|---------|
|**név** | A változó nevét, a válasz függvény kódban használt. Lásd: [HTTP használata kimeneti kötése kódból](#outputusage). |
| **típusa** |meg kell `http`. |
| **iránya** | meg kell `out`. |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>HTTP használata kimeneti kötése kódból
A kimeneti paraméter segítségével válaszol a http- vagy webhook hívók számára. A megfelelő nyelvet választ minták is használható. Például a válaszokat, lásd: [HTTP-eseményindító minták](#httptriggersample) és [Webhook eseményindító minták](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Válaszol a webhookok
Egy HTTP eseményindító a _webHookType_ tulajdonsággal konfigurált válaszolni [webhookok](https://en.wikipedia.org/wiki/Webhook). Az alapszintű konfigurációs "genericJson" beállítást használja. Ha csak a HTTP-n keresztül, POST és a kérelmek ez korlátozza a `application/json` tartalomtípus.

Az eseményindító továbbá testreszabható adott webhook-szolgáltatóra, például a [GitHub](https://developer.github.com/webhooks/) vagy [Slackhez](https://api.slack.com/outgoing-webhooks). Ha egy szolgáltató van megadva, a Functions futtatókörnyezete kezelik a szolgáltató ellenőrzési logika meg.  

### <a name="configuring-github-as-a-webhook-provider"></a>GitHub webhook szolgáltatóként konfigurálása
GitHub webhook válaszolni, először hozzon létre a függvény egy HTTP-eseményindítóval, és állítsa be a **webHookType** tulajdonságot `github`. Másolja a [URL-cím](#url) és [API-kulcs](#keys) azokat a **adja hozzá a webhook** a GitHub-tárház oldalán. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Egy vonatkozó példáért lásd: [hozzon létre egy függvényt a GitHub webhook által indított](functions-create-github-webhook-triggered-function.md).

### <a name="configuring-slack-as-a-webhook-provider"></a>A webhook szolgáltató Slackhez konfigurálása
A Slack webhook létrehoz egy jogkivonatot helyett, és adja meg azt, konfigurálnia kell egy funkcióspecifikus kulcsot a jogkivonatot a Slackhez, így Önnek. Lásd: [kulcsoknál](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>A HTTP-végpont testreszabása
Alapértelmezés szerint amikor egy HTTP-eseményindítóval, vagy a WebHook, a függvény létrehozása a függvény megcímezhető útvonal a következő formátumban:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Ez az útvonal nem kötelező testreszabható `route` a HTTP-eseményindítóval tulajdonsága bemeneti kötése. Tegyük fel, a következő *function.json* fájl határozza meg a `route` tulajdonság egy HTTP-eseményindító:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Ezt a konfigurációt használja, a függvény már megcímezhető helyett az eredeti útvonal a következő útvonal.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Ez lehetővé teszi, hogy a funkció támogatja a két paramétert a címben _kategória_ és _azonosító_. Akkor használhatja [webes API útvonal megkötés](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) a paraméterekkel. A következő C# funkciókódot mindkét paraméter használ.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Ez az azonos útvonal-paraméterek használata a Node.js-függvény kód.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

Alapértelmezés szerint az összes függvény útvonal előtagként *api*. Is testre szabhatja, vagy távolítsa el a előtag használatával a `http.routePrefix` tulajdonságot a *host.json* fájlt. A következő példában eltávolítjuk a *api* útvonal előtagja üres karakterlánc használja az-előtagot a *host.json* fájlt.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

Részletes információkat, hogyan lehet frissíteni a *host.json* fájl, a függvény, tekintse meg, [függvény alkalmazásfájlok frissítése](functions-reference.md#fileupdate). 

További tudnivalók egyéb konfigurálhatja a a *host.json* fájlba, lásd: [host.json hivatkozás](functions-host-json.md).


<a name="keys"></a>
## <a name="working-with-keys"></a>Kulcsok használata
HTTP-eseményindítók lehetővé teszik, hogy a nagyobb biztonság kulcsok használatára. Egy normál HTTP-eseményindítóval ezek API-kulcs, a kulcs szerepel a kérelem igénylő használhatja. Webhook kulcsok segítségével többféle módon, attól függően, hogy a szolgáltató támogatja a kérelmek engedélyezheti.

Kulcsok részeként a függvény alkalmazást az Azure-ban tároljuk, és inaktív titkosított. A kulcsok megtekintéséhez újakat hozhat létre, vagy kulcsok állni új értékek váltson a függvényt a portálon, és válassza ki a "Kezelése." 

A kulcsok két típusa van:
- **Kulcsok tárolására**: a függvény alkalmazáson belüli összes funkciók által megosztott ezeket a kulcsokat. Ha egy API-kulcsot, ezek a függvény alkalmazásban függvényeket elérését teszi lehetővé.
- **Funkcióbillentyűk**: ezek a kulcsok csak alkalmazása az adott funkciókhoz, amely alatt vannak definiálva. Ha egy API-kulcsot, ezek csak a hozzáférést a függvény.

Minden kulcs neve referenciaként, és nincs (a "alapértelmezett" nevű) alapértelmezett kulcs függvény és a gazdagép szintjén. A **főkulcs** egy alapértelmezett állomáskulcs neve "_master" definiált függvény alkalmazásokra vonatkozó. Ez a kulcs nem vonható vissza. A futtatókörnyezet API-k rendszergazdai hozzáférést biztosít. Használatával `"authLevel": "admin"` JSON igényli ezt a kulcsot használva jelenik meg a kérés; kötésében bármely más kulcs engedélyezési hiba eredményez.

> [!IMPORTANT]  
> A főkulccsal adott emelt szintű engedélyek, mert nem kell ezt a kulcsot megosztása a harmadik felek vagy natív ügyfélalkalmazásokban eloszthatják azt. Körültekintően járjon el, ha a rendszergazdai jogosultsági szint.

### <a name="api-key-authorization"></a>API-kulcs engedélyezési
Alapértelmezés szerint HTTP-eseményindítóval a HTTP-kérelmek API-kulcs szükséges. Ezért a HTTP-kérelmek általában a következőképpen néznek:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

A kulcs tartalmazhat egy lekérdezési karakterlánc-változóvá nevű `code`, a fentiek szerint, vagy a része egy `x-functions-key` HTTP-fejléc. A kulcsnak az értéke lehet bármely billentyűt, a függvény definiálva, vagy bármely állomás kulcsát.

Engedélyezheti a névtelen kérésekkel, amelyre nincs szükség a kulcsokat. A főkulcs használatát is megkövetelheti. Az alapértelmezett hitelesítési szint használatával módosíthatja a `authLevel` JSON kötésében tulajdonság. További információkért lásd: [HTTP-eseményindítóval](#httptrigger).

### <a name="keys-and-webhooks"></a>Kulcsok és webhookokkal
A webhook fogadó összetevőt, a HTTP-eseményindítóval részét Webhook engedélyezési kezeli, és a mechanizmus függ a webhook típusa. Minden egyes mechanizmus nem, a kulcs azonban támaszkodnak. Alapértelmezés szerint a függvény "alapértelmezett" nevű kulccsal. Egy másik kulcsot használ, konfigurálja a webhook szolgáltatót, amelyet a kulcs neve a kérés küldése a következő módszerek valamelyikével:

- **Lekérdezési karakterlánc**: A szolgáltató továbbítja a kulcs nevét a `clientid` lekérdezési karakterlánc, például a `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Kérelemfejléc**: A szolgáltató továbbítja a kulcs nevét a `x-functions-clientid` fejléc.

> [!NOTE]
> Funkcióbillentyűk élveznek állomások kulcsait. Ha két kulcsok vannak definiálva ugyanazzal a névvel, a függvény mindig kulccsal.


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>HTTP-eseményindító minták
Tegyük fel, hogy rendelkezik a következő HTTP-eseményindítóval a `bindings` function.json tömbje:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

A nyelvspecifikus mintát keres, tekintse meg a `name` paraméter, a lekérdezési karakterláncot vagy a HTTP-kérelem törzsét.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.js](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>A C# HTTP-eseményindító minta #
```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

Ahelyett, hogy a .NET-objektum is köthető **HttpRequestMessage**. Ez az objektum létrejön a szervezetnek a kérelem JSON-ként értelmezni. Egy típus hasonlóan átadhatók a HTTP-válasz kimeneti kötése és az adott válasz törzsének, és egy 200 állapotkód adja vissza.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>Az F # HTTP-eseményindító minta #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Kell egy `project.json` fájlt, amely a NuGet való hivatkozáshoz használja a `FSharp.Interop.Dynamic` és `Dynamitey` szerelvényeket, a következő:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Ez a függőségek fetch NuGet használ, és hivatkozik rájuk a parancsfájlban.

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>A Node.JS-ben HTTP-eseményindító minta
```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Webhook minták
Tegyük fel, hogy rendelkezik a következő webhook eseményindító a `bindings` function.json tömbje:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Tekintse meg a nyelvspecifikus mintát, amelyre bejelentkezik GitHub probléma megjegyzések.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>A C# Webhook minta #
```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Az F # Webhook minta #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>A node.js Webhook minta
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Következő lépések
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

