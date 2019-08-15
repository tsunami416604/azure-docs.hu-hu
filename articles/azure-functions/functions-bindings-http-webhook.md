---
title: HTTP-eseményindítók és-kötések Azure Functions
description: Ismerje meg, hogyan használhatók a HTTP-eseményindítók és-kötések a Azure Functionsban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functions, Event Processing, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra, HTTP, API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 4aadac343e023e68432741c1f1231bc0ec9fe0ea
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990186"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>HTTP-eseményindítók és-kötések Azure Functions

Ez a cikk azt ismerteti, hogyan használhatók a HTTP-eseményindítók és a kimeneti kötések Azure Functionsban.

A HTTP-triggerek testreszabhatók a webhookokra való reagáláshoz. [](https://en.wikipedia.org/wiki/Webhook)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

A cikkben található kód a 2. x függvényt, amely a .NET Core-t használja. Az 1. x szintaxissal kapcsolatos további információkért tekintse meg az [1. x függvények sablonjait](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A HTTP-kötések a [Microsoft. Azure. webjobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet csomagban, az 1. x verzióban érhetők el. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A HTTP-kötések a [Microsoft. Azure. webjobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet csomagban, 3. x verzióban érhetők el. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Eseményindító

A HTTP-trigger lehetővé teszi a függvények HTTP-kérelemmel való meghívását. HTTP-trigger használatával kiszolgáló nélküli API-kat hozhat létre, és válaszolhat a webhookokra.

Alapértelmezés szerint a http-triggerek a HTTP 200-es értéket adja vissza egy üres törzstel az 1. x vagy a HTTP 204 nem tartalmaz üres törzstel rendelkező tartalmat a 2. x függvényben. A válasz módosításához konfigurálja a [http kimeneti kötést](#output).

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, `name` amely egy paramétert keres a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében. Figyelje meg, hogy a rendszer a visszaadott értéket használja a kimeneti kötéshez, de a visszatérési érték attribútum nem kötelező.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl](functions-reference-csharp.md) -függvényt, amely a kötést használja. A függvény a `name` paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keresi.

Íme a *function.json* fájlt:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Az alábbi C# parancsfájl- `HttpRequest`kód a következőhöz kötődik:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

A helyett `HttpRequest`egyéni objektumhoz is köthető. Ez az objektum a kérelem törzsében jön létre, és JSON-ként van elemezve. Hasonlóképpen egy típus is átadható a HTTP-válasz kimeneti kötésnek, és a válasz törzsként, valamint egy 200 állapotkódot is visszaküldhető.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>Eseményindító - F# példa

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban [ F# ](functions-reference-fsharp.md) , valamint egy olyan függvényt, amely a kötést használja. A függvény a `name` paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keresi.

Íme a *function.json* fájlt:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a F# kódot:

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

Szüksége van egy `project.json` fájlra, amely a NuGet használatával `FSharp.Interop.Dynamic` hivatkozik a és `Dynamitey` szerelvényekre, ahogy az a következő példában látható:

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

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript](functions-reference-node.md) -függvényt, amely a kötést használja. A függvény a `name` paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keresi.

Íme a *function.json* fájlt:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
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

### <a name="trigger---python-example"></a>Trigger – Python-példa

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python](functions-reference-python.md) -függvényt, amely a kötést használja. A függvény a `name` paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keresi.

Íme a *function.json* fájlt:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-examples"></a>Trigger – Java-példák

* [Paraméter olvasása a lekérdezési karakterláncból](#read-parameter-from-the-query-string-java)
* [Szövegtörzs beolvasása POST-kérelemből](#read-body-from-a-post-request-java)
* [Paraméter olvasása útvonalból](#read-parameter-from-a-route-java)
* [POJO-törzs olvasása POST-kérelemből](#read-pojo-body-from-a-post-request-java)

Az alábbi példák a HTTP-trigger kötését mutatják be egy *function. JSON* fájlban, valamint a kötést használó megfelelő [Java](functions-reference-java.md) -függvényeket. 

Íme a *function.json* fájlt:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string-java"></a>Paraméter olvasása a lekérdezési karakterláncból (Java)  

Ez a példa egy nevű ```id```paramétert olvas be ```application/json```a lekérdezési karakterláncból, és felhasználja az ügyfélnek visszaadott JSON-dokumentum létrehozásához. 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>Szövegtörzs beolvasása POST-kérelemből (Java)  

Ez a példa egy post-kérelem ```String```törzsét olvassa be ```application/json```, és felhasználja az ügyfélnek visszaadott JSON-dokumentum létrehozásához.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>Paraméter olvasása útvonalból (Java)  

Ez a példa egy kötelező paramétert ( ```id```Name) és egy opcionális ```name``` paramétert olvas be ```application/json```az útvonal elérési útjából, és felhasználja őket az ügyfélnek visszaadott JSON-dokumentum tartalmának kiépítéséhez. T?

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>POJO-törzs olvasása POST-kérelemből (Java)  

Itt látható a következő példában hivatkozott ```ToDoItem``` osztály kódja:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Ez a példa egy POST-kérelem törzsét olvassa be. A kérés törzse automatikusan le lesz szerializálva egy ```ToDoItem``` objektumba, és a rendszer visszaadja az ügyfélnek a ```application/json```tartalomtípussal. A ```ToDoItem``` (z) paramétert a functions futtatókörnyezet szerializálja, mivel az ```body``` ```HttpMessageResponse.Builder``` osztály tulajdonságához van rendelve.

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribútumot.

Megadhatja az engedélyezési szintet és a megengedett HTTP-metódusokat az attribútumok konstruktorának paraméterei között, és a webhook típusa és az útválasztási sablon tulajdonságok is rendelkezésre állnak. További információ ezekről a beállításokról: [trigger-Configuration](#trigger---configuration). Itt egy `HttpTrigger` attribútum szerepel a metódus-aláírásban:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Egy teljes példa: [eseményindító – C#-példa](#trigger---c-example).

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `HttpTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **type** | n/a| Kötelező – a `httpTrigger`következőre kell beállítani:. |
| **direction** | n/a| Kötelező – a `in`következőre kell beállítani:. |
| **name** | n/a| Kötelező – a kérelem vagy a kérelem törzse függvény kódjában használt változó neve. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Meghatározza, hogy a függvény meghívásához szükség van-e a kulcsokra, ha vannak ilyenek. Az engedélyezési szint az alábbi értékek egyike lehet: <ul><li><code>anonymous</code>&mdash;Nincs szükség API-kulcsra.</li><li><code>function</code>&mdash;Function-specifikus API-kulcs szükséges. Ez az alapértelmezett érték, ha nincs megadva.</li><li><code>admin</code>&mdash;A főkulcs megadása kötelező.</li></ul> További információt az [engedélyezési kulcsok](#authorization-keys)című szakaszban talál. |
| **methods** |**Methods** | A függvény által válaszoló HTTP-metódusok tömbje. Ha nincs megadva, a függvény az összes HTTP-metódusra válaszol. Lásd: [a http-végpont testreszabása](#customize-the-http-endpoint). |
| **route** | **Route** | Meghatározza azt az útválasztási sablont, amely azt szabályozza, hogy a függvény milyen URL-címeket válaszol. Az alapértelmezett érték, `<functionname>`ha nincs megadva. További információ: [a http-végpont testreszabása](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Csak az 1. x verziójú futtatókörnyezet esetében támogatott._<br/><br/>Konfigurálja a HTTP-triggert, amely [webhook](https://en.wikipedia.org/wiki/Webhook) -fogadóként működik a megadott szolgáltatónál. Ha ezt a `methods` tulajdonságot beállítja, ne állítsa be a tulajdonságot. A webhook típusa a következő értékek egyike lehet:<ul><li><code>genericJson</code>&mdash;Egy általános célú webhook-végpont egy adott szolgáltató logikája nélkül. Ez a beállítás csak a http post és a `application/json` tartalomtípus használatával korlátozza a kérelmeket.</li><li><code>github</code>&mdash;A függvény válaszol a [GitHub](https://developer.github.com/webhooks/)-webhookokra. Ne használja a _authLevel_ tulajdonságot GitHub-webhookokkal. További információt a cikk későbbi, a GitHub-webhookok című szakaszában talál.</li><li><code>slack</code>&mdash;A függvény válaszol a [Slack](https://api.slack.com/outgoing-webhooks)webhookokra. Ne használja a _authLevel_ tulajdonságot Slack webhookokkal. További információt a cikk későbbi, a Slack webhookok című szakaszában talál.</li></ul>|

## <a name="trigger---usage"></a>Eseményindító - használat

A C# és F# a függvények esetében deklarálhatja az indító bemenet `HttpRequest` típusát vagy egyéni típusként. Ha úgy dönt `HttpRequest`, teljes hozzáférést kap a kérelem objektumhoz. Egyéni típus esetén a futásidejű megpróbálja elemezni a JSON-kérés törzsét az objektum tulajdonságainak beállításához.

JavaScript-függvények esetén a functions futtatókörnyezet a kérési objektum helyett a kérelem törzsét biztosítja. További információ: [JavaScript trigger példa](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>A HTTP-végpont testreszabása

Alapértelmezés szerint a HTTP-triggerekhez tartozó függvény létrehozásakor a függvény címezhető az űrlap egy útvonalával:

    http://<yourapp>.azurewebsites.net/api/<funcname>

Ezt az útvonalat a http-trigger bemeneti `route` kötésének opcionális tulajdonságával is testreszabhatja. Példaként a következő *function. JSON* fájl definiál egy `route` tulajdonságot egy http-triggerhez:

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

Ennek a konfigurációnak a használatával a függvény mostantól a következő útvonalon érhető el az eredeti útvonal helyett.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Ez lehetővé teszi, hogy a függvény programkódja támogassa a címben, a _kategóriában_ és az _azonosítóban_szereplő két paramétert. A paraméterekkel bármilyen [webes API Route](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) -korlátozást használhat. A következő C# függvény kód mindkét paraméter használatát teszi lehetővé.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

Itt látható a Node. js-függvény kódja, amely ugyanazt az útválasztási paramétert használja.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

Alapértelmezés szerint az összes függvény útvonala az *API*-val előtaggal van ellátva. Az előtagot a `http.routePrefix` [Host. JSON](functions-host-json.md) fájlban található tulajdonság használatával is testreszabhatja vagy eltávolíthatja. Az alábbi példa eltávolítja az *API* Route előtagot egy üres karakterlánc használatával a *Host. JSON* fájlban lévő előtaghoz.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Ügyfél-identitások használata

Ha a Function alkalmazás [app Service hitelesítés/engedélyezés](../app-service/overview-authentication-authorization.md)funkciót használja, megtekintheti a kód alapján a hitelesített ügyfelek adatait. Ez [az információ a platform által befecskendezett kérelem fejlécként](../app-service/app-service-authentication-how-to.md#access-user-claims)érhető el. 

Ezeket az információkat a kötési adatokból is elolvashatja. Ez a funkció csak a functions 2. x futtatókörnyezetben érhető el. Emellett jelenleg csak a .NET nyelveken érhető el.

.NET-nyelveken ez az információ [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)érhető el. A ClaimsPrincipal a kérelem környezetének részeként érhető el, ahogy az az alábbi példában is látható:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Azt is megteheti, hogy a ClaimsPrincipal egyszerűen szerepelhet további paraméterként a függvény aláírása során:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>Engedélyezési kulcsok

A függvények lehetővé teszik a kulcsok használatát, hogy a fejlesztés során megnehezíti a HTTP-függvény végpontjának elérését.  A szabványos HTTP-triggerek esetében előfordulhat, hogy ilyen API-kulcsra van szükség a kérelemben. 

> [!IMPORTANT]
> Míg a kulcsok segíthetnek a HTTP-végpontok kiépítésében a fejlesztés során, nem céljuk a HTTP-triggerek védelme az éles környezetben. További információ: [http-végpont biztonságossá tétele éles](#secure-an-http-endpoint-in-production)környezetben.

> [!NOTE]
> A functions 1. x futtatókörnyezetben a webhook-szolgáltatók több módon is használhatják a kérelmeket, attól függően, hogy mit támogat a szolgáltató. Ezt a webhookok [és kulcsok](#webhooks-and-keys)tartalmazzák. A 2. x verziójú futtatókörnyezet nem tartalmaz beépített támogatást a webhook-szolgáltatók számára.

A kulcsok két típusa létezik:

* **Gazdagép kulcsai**: Ezeket a kulcsokat a Function alkalmazásban található összes függvény megosztja. API-kulcsként való használata esetén ezek a függvények a Function alkalmazáson belüli bármelyik függvényhez hozzáférhetnek.
* **Függvények kulcsai**: Ezek a kulcsok csak azokra a függvényekre érvényesek, amelyekben definiálva vannak. API-kulcsként való használata esetén ezek csak a funkció elérését teszik lehetővé.

A rendszer az egyes kulcsokat hivatkozásként nevezi el, és az alapértelmezett kulcs ("default") szerepel a függvény és a gazdagép szintjén. A függvények kulcsai elsőbbséget élveznek a gazdagép kulcsaival szemben. Ha két kulcs van definiálva ugyanazzal a névvel, a rendszer mindig a függvény kulcsát használja.

Minden Function alkalmazásnak van egy speciálisfőkulcsa is. Ez a kulcs egy nevű `_master`gazdagép-kulcs, amely rendszergazdai hozzáférést biztosít a futásidejű API-khoz. Ezt a kulcsot nem lehet visszavonni. Ha az engedélyezési szintjét `admin`állítja be, a kérelmeknek a főkulcsot kell használniuk; minden más kulcs engedélyezési hibát eredményez.

> [!CAUTION]  
> A főkulcs által biztosított Function app emelt szintű engedélyei miatt ne ossza meg ezt a kulcsot harmadik felekkel, vagy terjessze azt natív ügyfélalkalmazások számára. A rendszergazdai jogosultsági szint kiválasztásakor legyen körültekintő.

### <a name="obtaining-keys"></a>Kulcsok beszerzése

A kulcsok tárolása az Azure-beli Function-alkalmazás részeként történik, és a rendszer a nyugalmi állapotban van titkosítva. Ha meg szeretné tekinteni a kulcsokat, újakat kell létrehoznia, vagy új értékekre kell visszagörgetni a kulcsokat, navigáljon a [Azure Portal](https://portal.azure.com) valamelyik http-triggerrel elindított függvényéhez, és válassza a **kezelés**lehetőséget.

![A funkcióbillentyűk kezelése a portálon.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Nem érhető el támogatott API a funkcióbillentyűk programozott beszerzéséhez.

### <a name="api-key-authorization"></a>API-kulcs engedélyezése

A legtöbb HTTP-trigger sablonhoz API-kulcs szükséges a kérelemben. Így a HTTP-kérelem általában a következő URL-címhez hasonlít:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A kulcs egy nevű `code`lekérdezési karakterlánc-változóban is szerepelhet, a fentiek szerint. Egy `x-functions-key` HTTP-fejlécben is szerepelhet. A kulcs értéke lehet a függvényhez definiált bármelyik funkcióbillentyű, vagy bármely gazdagép kulcsa.

Engedélyezheti a névtelen kérelmeket, amelyek nem igénylik a kulcsokat. Azt is megkövetelheti, hogy a főkulcs legyen használatban. Az alapértelmezett engedélyezési szintet a kötés JSON `authLevel` tulajdonságának használatával módosíthatja. További információ: [trigger-Configuration](#trigger---configuration).

> [!NOTE]
> A függvények helyi futtatásakor az engedélyezés le van tiltva a megadott hitelesítési szint beállításától függetlenül. Az Azure-ba való közzététel `authLevel` után a rendszer kikényszeríti az trigger beállítását.



### <a name="secure-an-http-endpoint-in-production"></a>HTTP-végpont biztonságossá tétele éles környezetben

Az üzemi végpontok éles környezetben történő teljes biztonságossá tételéhez érdemes megfontolni a következő Function szintű biztonsági beállítások egyikének megvalósítását:

* Kapcsolja be App Service hitelesítés/engedélyezés funkciót a Function alkalmazáshoz. A App Service platform lehetővé teszi, hogy a Azure Active Directory (HRE) és számos harmadik féltől származó személyazonosság-szolgáltató használatával hitelesítse ügyfeleit. Ezt használhatja a függvények egyéni engedélyezési szabályainak megvalósításához, és a függvény kódjából is dolgozhat a felhasználói adatokkal. További információ: [hitelesítés és engedélyezés Azure app Service](../app-service/overview-authentication-authorization.md) és [ügyfél-identitások használata](#working-with-client-identities).

* Az Azure API Management (APIM) használatával hitelesítheti a kérelmeket. A APIM számos API-biztonsági beállítást biztosít a bejövő kérelmekhez. További tudnivalók: [API Management hitelesítési házirendek](../api-management/api-management-authentication-policies.md). A APIM használatával beállíthatja, hogy a Function alkalmazás csak a APIM-példány IP-címéről fogadjon kérelmeket. További információt az [IP-címek korlátozásai](ip-addresses.md#ip-address-restrictions)című témakörben talál.

* A Function alkalmazás üzembe helyezése egy Azure App Service Environment (bevezetési). A bevezetési szolgáltatás dedikált üzemeltetési környezetet biztosít a függvények futtatásához. A bevezetője lehetővé teszi egyetlen előtér-átjáró konfigurálását, amely az összes bejövő kérelem hitelesítésére használható. További információ: webalkalmazási [tűzfal (WAF) konfigurálása app Service Environmenthoz](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Ha ezen alkalmazás-szintű biztonsági módszerek egyikét használja, állítsa be a HTTP által aktivált függvény hitelesítési szintjét a `anonymous`következőre:.

### <a name="webhooks"></a>webhooks

> [!NOTE]
> A webhook mód csak a functions futtatókörnyezet 1. x verziójához érhető el. Ez a változás a 2. x verzióban a HTTP-eseményindítók teljesítményének javítása érdekében történt.

Az 1. x verzióban a webhook-sablonok további ellenőrzéseket biztosítanak a webhook hasznos adataihoz. A 2. x verzióban az alap HTTP-trigger továbbra is működik, és a webhookok ajánlott megközelítése. 

#### <a name="github-webhooks"></a>GitHub-webhookok

A GitHub-webhookokra való reagáláshoz először hozza létre a függvényt egy HTTP-triggerrel , és állítsa `github`be a webHookType tulajdonságot a következőre:. Ezután másolja a saját URL-címét és API-kulcsát a GitHub-adattár **Hozzáadás webhook** lapjára. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Slack-webhookok

A Slack webhook létrehoz egy jogkivonatot, és nem adja meg az Ön által megadott értéket, ezért egy függvény-specifikus kulcsot kell konfigurálnia a biztonsági másolatból a Tartalékidőből. Lásd: [engedélyezési kulcsok](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhookok és kulcsok

A webhook-engedélyezést a webhook fogadó összetevője kezeli, amely a HTTP-trigger része, és a mechanizmus a webhook típusától függően változik. Mindegyik mechanizmus egy kulcsra támaszkodik. Alapértelmezés szerint a "default" nevű funkcióbillentyű van használatban. Ha másik kulcsot szeretne használni, konfigurálja úgy a webhook-szolgáltatót, hogy a következő módszerek egyikével küldje el a kulcs nevét a kérelemben:

* **Lekérdezési karakterlánc**: A szolgáltató a `clientid` lekérdezési karakterlánc paraméterében adja át a kulcs nevét, `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`például:.
* **Kérelem fejléce**: A szolgáltató átadja a kulcs nevét a `x-functions-clientid` fejlécben.

## <a name="trigger---limits"></a>Trigger – korlátok

A HTTP-kérés hossza legfeljebb 100 MB (104 857 600 bájt), az URL-cím hossza pedig 4 KB (4 096 bájt) lehet. Ezeket a korlátokat a futtatókörnyezet `httpRuntime` [web. config fájljának](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)eleme határozza meg.

Ha a HTTP-triggert használó függvény nem fejeződött be körülbelül 2,5 percen belül, az átjáró időtúllépést jelez, és HTTP 502-hibát ad vissza. A függvény továbbra is futni fog, de nem tud HTTP-választ adni. A hosszú ideig futó függvények esetében javasoljuk, hogy kövesse az aszinkron mintákat, és olyan helyet küldjön vissza, amelyben pingelheti a kérés állapotát. További információ a függvények futtatásának időtartamáról: [skálázás és üzemeltetés – fogyasztási terv](functions-scale.md#timeout).

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [Host. JSON](functions-host-json.md) fájl olyan beállításokat tartalmaz, amelyek a http-trigger viselkedését vezérlik.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Output

A http-kimeneti kötés használatával válaszolhat a HTTP-kérelem feladójának. Ehhez a kötéshez HTTP-trigger szükséges, és lehetővé teszi az trigger kérelméhez tartozó válasz testreszabását. Ha nincs megadva HTTP-kimeneti kötés, a http-trigger a HTTP 200 OK értéket adja vissza üres törzstel az 1. x függvényben, vagy a HTTP 204 nem tartalmaz üres törzstel rendelkező tartalmat a 2. x függvényben.

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat a *function. JSON* fájlban beállított kötési konfigurációs tulajdonságokat ismerteti. Az C# osztályok könyvtáraiban nincsenek a *function. JSON* -tulajdonságokkal egyező attribútumok.

|Tulajdonság  |Leírás  |
|---------|---------|
| **type** |Meg kell `http`. |
| **direction** | Meg kell `out`. |
|**name** | A válasz kódjában használt változó neve, vagy `$return` a visszatérési érték használata. |

## <a name="output---usage"></a>Kimenet – használat

HTTP-válasz küldéséhez használja a Language-standard Response mintákat. A C# vagy C# a szkriptben állítsa vissza a függvény `IActionResult` visszatérési típusát vagy `Task<IActionResult>`. A C#-ben a Return Value attribútum nem kötelező.

A válaszokat például az [trigger példája](#trigger---example)tartalmazza.

## <a name="next-steps"></a>További lépések

[Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
