---
title: "Az Azure Functions HTTP és a webhook kötések"
description: "A HTTP és a webhook eseményindítók és kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, esemény feldolgozása, webhookokkal, dinamikus számítás-, kiszolgáló nélküli architektúra, HTTP, API REST"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: 608f5ec2fb4b8fa374778cb4f506f1d25eb7642b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Az Azure Functions HTTP és a webhook kötések

Ez a cikk ismerteti az Azure Functions kötések HTTP használata. Az Azure Functions támogatja a HTTP eseményindítók és a kimeneti kötések.

Egy HTTP-eseményindítóval testre szabható válaszolni [webhookok](https://en.wikipedia.org/wiki/Webhook). A webhook eseményindító csak egy JSON-adattartalmat fogad, és érvényesíti a JSON-NÁ. A webhook eseményindító, amelyek megkönnyítik az egyes szolgáltatók, köztük a Githubon és a Slackhez webhookok kezeli különleges verziója van.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="trigger"></a>Eseményindító

A HTTP-eseményindítóval lehetővé teszi a HTTP-kérelem a függvényt. Egy HTTP-eseményindítóval kiszolgáló nélküli API-k létrehozása, és webhookokkal megválaszolásához használhatja. 

Alapértelmezés szerint HTTP-eseményindítóval válaszol egy HTTP 200 OK állapotot kódot és egy üres fő kérésre. A válasz módosításához konfigurálása egy [HTTP kimeneti kötése](#http-output-binding).

## <a name="trigger---example"></a>Eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Eseményindító - C# – példa

A következő példa azt mutatja be egy [C# függvény](functions-dotnet-class-library.md) , amely megkeresi a `name` paraméter, a lekérdezési karakterláncot vagy a HTTP-kérelem törzsét.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

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

### <a name="trigger---c-script-example"></a>Eseményindító - C# parancsfájl – példa

A következő példa bemutatja az eseményindító kötés egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A funkció megkeresi a `name` paraméter, a lekérdezési karakterláncot vagy a HTTP-kérelem törzsét.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

Ez a C# parancsfájlkód, amelyhez van kötve `HttpRequestMessage`:

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

Ahelyett, hogy egyéni objektumot köthető `HttpRequestMessage`. Ez az objektum létrejön a szervezetnek a kérelem JSON-ként értelmezni. Egy típus hasonlóan átadhatók a HTTP-válasz kimeneti kötése és az adott válasz törzsének, és egy 200 állapotkód adja vissza.

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

### <a name="trigger---f-example"></a>Eseményindító - F # – példa

A következő példa bemutatja az eseményindító kötés egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A funkció megkeresi a `name` paraméter, a lekérdezési karakterláncot vagy a HTTP-kérelem törzsét.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A F # kód itt látható:

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

Kell egy `project.json` fájlt, amely a NuGet való hivatkozáshoz használja a `FSharp.Interop.Dynamic` és `Dynamitey` szerelvényeket, a következő példában látható módon:

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

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript – példa

A következő példa bemutatja az eseményindító kötés egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A funkció megkeresi a `name` paraméter, a lekérdezési karakterláncot vagy a HTTP-kérelem törzsét.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

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
     
## <a name="trigger---webhook-example"></a>Eseményindító - webhook – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#webhook---c-example)
* [C# script (.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely egy HTTP 200 elküldi egy általános JSON irányuló kérelemre adott válasz.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook - C# parancsfájl – példa

A következő példa bemutatja a kötés webhook eseményindító egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény GitHub probléma megjegyzések naplózza.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

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

### <a name="webhook---f-example"></a>Webhook - F # – példa

A következő példa bemutatja a kötés webhook eseményindító egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény GitHub probléma megjegyzések naplózza.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A F # kód itt látható:

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

### <a name="webhook---javascript-example"></a>Webhook - JavaScript example

A következő példa bemutatja a kötés webhook eseményindító egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény GitHub probléma megjegyzések naplózza.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribútummal, a NuGet-csomag [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http).

Állíthatja be a hitelesítési szint és engedélyezett HTTP-metódus attribútum konstruktorparaméterek, és nincsenek a webhook típusát és útvonal-sablon tulajdonságait. A beállításokkal kapcsolatban további információkért lásd: [eseményindító - konfigurációs](#trigger---configuration). Íme egy `HttpTrigger` metódus-aláírás attribútum:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Tekintse meg a teljes például [eseményindító - C# példa](#trigger---c-example).

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `HttpTrigger` attribútum.


|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **típusa** | n/a| Szükséges – kell állítani `httpTrigger`. |
| **direction** | n/a| Szükséges – kell állítani `in`. |
| **name** | n/a| Kötelező – a kérelem vagy kérelemtörzset függvény a kódban használt változó neve. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Meghatározza, hogy mi kulcsok, ha van ilyen kell jelen lennie ahhoz, hogy a függvény meghívása a kérésre. A jogosultsági szintet a következő értékek egyike lehet: <ul><li><code>anonymous</code>&mdash;Nincs API-kulcsot meg kell adni.</li><li><code>function</code>&mdash;Funkcióspecifikus API-kulcs megadása kötelező. Ez az az alapértelmezett érték, ha nincs megadva.</li><li><code>admin</code>&mdash;A fő kulcsot meg kell adni.</li></ul> További információkért lásd: a szakasz [engedélyezési kulcsok](#authorization-keys). |
| **módszerek** |**Módszerek** | A tömb, amelyre a függvény válaszol a HTTP-metódus. Ha nincs megadva, a függvény válaszol-e a HTTP-metódus. Lásd: [testre szabhatja a http-végpont](#trigger---customize-the-http-endpoint). |
| **útvonal** | **Útvonal** | Meghatározza az útvonalsablonhoz szabályozása, amelyhez a kérés URL-címeket, a függvény válaszol. Az alapértelmezett érték, ha nincs megadva `<functionname>`. További információkért lásd: [testre szabhatja a http-végpont](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Konfigurálja a HTTP-eseményindítóval járhasson el egy [webhook](https://en.wikipedia.org/wiki/Webhook) fogadójának a megadott szolgáltatón. Ne állítsa be a `methods` tulajdonságot, ha a tulajdonság értékét. A webhook típus a következő értékek egyike lehet:<ul><li><code>genericJson</code>&mdash;Egy általános célú webhook végpont logika egy adott szolgáltató nélkül. Ez a beállítás megtiltja kérelmek Ha csak a HTTP-n keresztül, POST, hogy az a `application/json` tartalomtípus.</li><li><code>github</code>&mdash;A függvény válaszol-e a [GitHub webhook](https://developer.github.com/webhooks/). Ne használja a _authLevel_ GitHub webhook tulajdonságot. További információkért lásd a GitHub webhook című cikkben.</li><li><code>slack</code>&mdash;A függvény válaszol-e a [Slack-webhookok](https://api.slack.com/outgoing-webhooks). Ne használja a _authLevel_ Slack webhookok tulajdonságot. További információkért lásd: a Slack webhookok című cikkben.</li></ul>|

## <a name="trigger---usage"></a>Eseményindító - használat

C# és F # függvények, adjon meg lehet az eseményindító típusú deklarálhatnak `HttpRequestMessage` vagy olyan egyéni típusra. Ha úgy dönt, `HttpRequestMessage`, a request objektumon teljes hozzáférést kap. Olyan egyéni típusra funkciók megkísérli elemzése a JSON-kérés törzsében objektum tulajdonságainak beállításához. 

A Functions futtatókörnyezete JavaScript-funkcióként, itt a kérés törzsében helyett a request objektumon. További információkért lásd: a [JavaScript eseményindító példa](#trigger---javascript-example).

### <a name="github-webhooks"></a>GitHub webhook

GitHub webhook válaszolni, először hozzon létre a függvény egy HTTP-eseményindítóval, és állítsa be a **webHookType** tulajdonságot `github`. Másolja az URL-CÍMÉT és API-kulcsát azokat a **adja hozzá a webhook** a GitHub-tárház oldalán. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Példaként tekintse meg a [GitHub-webhookok által aktivált függvények létrehozását](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Slack webhookok

A Slack webhook létrehoz egy jogkivonatot helyett, és adja meg azt, konfigurálnia kell egy funkcióspecifikus kulcsot a jogkivonatot a Slackhez, így Önnek. Lásd: [engedélyezési kulcsok](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>A HTTP-végpont testreszabása

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

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

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

Ez az útvonal paramétereket használó Node.js függvény kódot.

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

Alapértelmezés szerint az összes függvény útvonal előtagként *api*. Is testre szabhatja, vagy távolítsa el a előtag használatával a `http.routePrefix` tulajdonságot a [host.json](functions-host-json.md) fájlt. A következő példában eltávolítjuk a *api* útvonal előtagja üres karakterlánc használja az-előtagot a *host.json* fájlt.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Engedélyezési kulcsok

HTTP-eseményindítók lehetővé teszik, hogy a nagyobb biztonság kulcsok használatára. Egy normál HTTP-eseményindítóval ezek API-kulcs, a kulcs szerepel a kérelem igénylő használhatja. Webhook kulcsok segítségével többféle módon, attól függően, hogy a szolgáltató támogatja a kérelmek engedélyezheti.

Kulcsok részeként a függvény alkalmazást az Azure-ban tároljuk, és inaktív titkosított. A kulcsok megtekintéséhez újakat hozhat létre, vagy kulcsok állni új értékek váltson a függvényt a portálon, és válassza ki a "Kezelése." 

A kulcsok két típusa van:

- **Kulcsok tárolására**: a függvény alkalmazáson belüli összes funkciók által megosztott ezeket a kulcsokat. Ha egy API-kulcsot, ezek a függvény alkalmazásban függvényeket elérését teszi lehetővé.
- **Funkcióbillentyűk**: ezek a kulcsok csak alkalmazása az adott funkciókhoz, amely alatt vannak definiálva. Ha egy API-kulcsot, ezek csak a hozzáférést a függvény.

Minden kulcs neve referenciaként, és nincs (a "alapértelmezett" nevű) alapértelmezett kulcs függvény és a gazdagép szintjén. Funkcióbillentyűk élveznek állomások kulcsait. Ha két kulcsok vannak definiálva ugyanazzal a névvel, a függvény mindig kulccsal.

A **főkulcs** egy alapértelmezett állomáskulcs neve "_master" definiált függvény alkalmazásokra vonatkozó. Ez a kulcs nem vonható vissza. A futtatókörnyezet API-k rendszergazdai hozzáférést biztosít. Használatával `"authLevel": "admin"` JSON igényli ezt a kulcsot használva jelenik meg a kérés; kötésében bármely más kulcs engedélyezési hiba eredményez.

> [!IMPORTANT]  
> A főkulccsal adott emelt szintű engedélyek, mert nem kell ezt a kulcsot megosztása a harmadik felek vagy natív ügyfélalkalmazásokban eloszthatják azt. Körültekintően járjon el, ha a rendszergazdai jogosultsági szint.

### <a name="api-key-authorization"></a>API-kulcs engedélyezési

Alapértelmezés szerint HTTP-eseményindítóval a HTTP-kérelmek API-kulcs szükséges. Ezért a HTTP-kérelmek általában a következőképpen néznek:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

A kulcs tartalmazhat egy lekérdezési karakterlánc-változóvá nevű `code`, a fentiek szerint, vagy a része egy `x-functions-key` HTTP-fejléc. A kulcsnak az értéke lehet bármely billentyűt, a függvény definiálva, vagy bármely állomás kulcsát.

Engedélyezheti a névtelen kérésekkel, amelyre nincs szükség a kulcsokat. A főkulcs használatát is megkövetelheti. Az alapértelmezett hitelesítési szint használatával módosíthatja a `authLevel` JSON kötésében tulajdonság. További információkért lásd: [eseményindító - konfigurációs](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Kulcsok és webhookokkal

A webhook fogadó összetevőt, a HTTP-eseményindítóval részét Webhook engedélyezési kezeli, és a mechanizmus függ a webhook típusa. Minden egyes mechanizmus nem, a kulcs azonban támaszkodnak. Alapértelmezés szerint a függvény "alapértelmezett" nevű kulccsal. Egy másik kulcsot használ, konfigurálja a webhook szolgáltatót, amelyet a kulcs neve a kérés küldése a következő módszerek valamelyikével:

- **Lekérdezési karakterlánc**: A szolgáltató továbbítja a kulcs nevét a `clientid` lekérdezési karakterlánc, például a `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Kérelemfejléc**: A szolgáltató továbbítja a kulcs nevét a `x-functions-clientid` fejléc.

## <a name="trigger---limits"></a>Eseményindító - korlátok

A HTTP-kérelem hosszabb 100 kilobájt (102,400) korlátozott, és az URL-cím hossza legfeljebb 4 KB-os (4096) bájt. Ezek a korlátozások vannak megadva a `httpRuntime` elem a futtatókörnyezet [Web.config fájl](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config).

Ha egy függvény által használt nem körülbelül 2,5 perc, az átjáró lesz időkorláton belül végrehajtani az a HTTP-eseményindítóval, és HTTP 502-es hibát ad vissza. A függvény futása folytatódik, de nem lehet egy HTTP-válasz visszaadása. Hosszan futó feladatokat az azt javasoljuk, hogy hajtsa végre az aszinkron minták, és adott vissza egy helyet, ahol megpingelheti a kérés állapotát. Mennyi ideig futhat egy függvény kapcsolatos információkért lásd: [méretezés és a tároló - fogyasztás megtervezése](functions-scale.md#consumption-plan). 

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [host.json](functions-host-json.md) fájl HTTP-eseményindító viselkedését vezérlő beállításokat tartalmaz.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Kimenet

Használja a HTTP kimeneti kötése a HTTP-kérést küldő válaszolni. A kötés egy HTTP-eseményindítóval igényel, és lehetővé teszi a válasz az eseményindító kérelemhez társított testreszabását. Ha HTTP kimeneti kötése van a nem Microsofttól származó, egy HTTP-eseményindítóval egy üres szövegtörzzsel HTTP 200 OK adja vissza. 

## <a name="output---configuration"></a>Kimeneti - konfiguráció

A C# osztály tárak nincsenek kimeneti-specifikus kötés konfigurációs tulajdonságok. Küldjön egy HTTP-válasz, végezze el a függvény visszatérési típusa `HttpResponseMessage` vagy `Task<HttpResponseMessage>`.

Egyéb nyelvek HTTP kimeneti kötése a JSON-objektumként van definiálva a `bindings` tömbje function.json, a következő példában látható módon:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájlt.

|Tulajdonság  |Leírás  |
|---------|---------|
| **típusa** |meg kell `http`. |
| **direction** | meg kell `out`. |
|**name** | A változó nevét, a válasz függvény kódban használt. |

## <a name="output---usage"></a>Kimeneti - használat

A kimeneti paraméter segítségével válaszol a http- vagy webhook hívók számára. A megfelelő nyelvet választ minták is használható. Például a válaszokat, tekintse meg a [eseményindító példa](#trigger---example) és a [webhook példa](#trigger---webhook-example).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
