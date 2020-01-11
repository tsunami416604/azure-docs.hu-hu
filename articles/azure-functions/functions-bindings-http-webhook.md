---
title: HTTP-eseményindítók és-kötések Azure Functions
description: Ismerje meg, hogyan használhatók a HTTP-eseményindítók és-kötések a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 40456b2a756d5ae2241b54ff65f675004c22f0a2
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890352"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>HTTP-eseményindítók és-kötések Azure Functions

Ez a cikk azt ismerteti, hogyan használhatók a HTTP-eseményindítók és a kimeneti kötések Azure Functionsban.

A HTTP-triggerek testreszabhatók a [webhookokra](https://en.wikipedia.org/wiki/Webhook)való reagáláshoz.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

A cikkben található kód a .NET Core-ot használó függvények alapértelmezett szintaxisát használja, amely a functions 2. x vagy újabb verziójában használatos. Az 1. x szintaxissal kapcsolatos további információkért tekintse meg az [1. x függvények sablonjait](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

A HTTP-kötések a [Microsoft. Azure. webjobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet csomagban, az 1. x verzióban érhetők el. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A HTTP-kötések a [Microsoft. Azure. webjobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet csomagban, 3. x verzióban érhetők el. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Eseményindító

A HTTP-trigger lehetővé teszi a függvények HTTP-kérelemmel való meghívását. HTTP-trigger használatával kiszolgáló nélküli API-kat hozhat létre, és válaszolhat a webhookokra.

Alapértelmezés szerint a HTTP-triggerek a HTTP 200-es értéket adja vissza, az 1. x függvényben üres törzstel, vagy a HTTP 204 nem tartalmaz üres törzstel rendelkező tartalmat a 2. x és újabb függvényeknél. A válasz módosításához konfigurálja a [http kimeneti kötést](#output).

## <a name="trigger---example"></a>Trigger – példa

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy `name` paramétert keres a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében. Figyelje meg, hogy a rendszer a visszaadott értéket használja a kimeneti kötéshez, de a visszatérési érték attribútum nem kötelező.

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy `name` paramétert keres a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében.

Itt látható a *function. JSON* fájl:

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

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Az alábbi C# parancsfájl-kód a `HttpRequest`hoz kötődik:

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

`HttpRequest`helyett egyéni objektumhoz is köthető. Ez az objektum a kérelem törzsében jön létre, és JSON-ként van elemezve. Hasonlóképpen egy típus is átadható a HTTP-válasz kimeneti kötésnek, és a válasz törzsként, valamint egy 200 állapotkódot is visszaküldhető.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy `name` paramétert keres a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében.

Itt látható a *function. JSON* fájl:

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

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény egy `name` paramétert keres a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében.

Itt látható a *function. JSON* fájl:

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

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

# <a name="javatabjava"></a>[Java](#tab/java)

* [Paraméter olvasása a lekérdezési karakterláncból](#read-parameter-from-the-query-string)
* [Szövegtörzs beolvasása POST-kérelemből](#read-body-from-a-post-request)
* [Paraméter olvasása útvonalból](#read-parameter-from-a-route)
* [POJO-törzs olvasása POST-kérelemből](#read-pojo-body-from-a-post-request)

Az alábbi példák a HTTP-trigger kötését mutatják be egy *function. JSON* fájlban, valamint a kötést használó megfelelő [Java-függvényeket](functions-reference-java.md) . 

Itt látható a *function. JSON* fájl:

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

#### <a name="read-parameter-from-the-query-string"></a>Paraméter olvasása a lekérdezési karakterláncból

Ez a példa egy ```id```nevű paramétert olvas be a lekérdezési karakterláncból, és felhasználja az ügyfélnek visszaadott JSON-dokumentum létrehozásához a tartalomtípus ```application/json```. 

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

#### <a name="read-body-from-a-post-request"></a>Szövegtörzs beolvasása POST-kérelemből

Ez a példa egy POST-kérelem törzsét olvassa be ```String```ként, és felhasználja az ügyfélnek visszaadott JSON-dokumentum létrehozásához a tartalomtípus ```application/json```.

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

#### <a name="read-parameter-from-a-route"></a>Paraméter olvasása útvonalból

Ez a példa egy ```id```nevű kötelező paramétert olvas be, valamint egy opcionális paramétert ```name``` az útvonal elérési útjából, és felhasználja őket egy, az ügyfélnek visszaadott JSON-dokumentum létrehozásához a tartalomtípus ```application/json```. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>POJO-törzs olvasása POST-kérelemből

A ```ToDoItem``` osztályhoz tartozó kód az alábbi példában hivatkozott:

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

Ez a példa egy POST-kérelem törzsét olvassa be. A kérés törzse automatikusan le lesz szerializálva egy ```ToDoItem``` objektumba, és a rendszer visszaküldi az ügyfélnek a tartalom típusa ```application/json```. A functions futtatókörnyezet szerializálja a ```ToDoItem``` paramétert, mivel az a ```HttpMessageResponse.Builder``` osztály ```body``` tulajdonságához van rendelve.

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

---

## <a name="trigger---attributes"></a>Trigger – attribútumok

Az [ C# osztály könyvtáraiban](functions-dotnet-class-library.md) és a Java-ban a függvény konfigurálásához a `HttpTrigger` attribútum érhető el.

Beállíthatja az engedélyezési szintet és a HTTP-metódusokat az attribútumok konstruktorának paraméterei, a webhook típusa és egy útvonal-sablon számára. További információ ezekről a beállításokról: [trigger-Configuration](#trigger---configuration).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ez a példa bemutatja, hogyan használhatja a [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribútumot.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Teljes példaként tekintse meg az [trigger példáját](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

Ez a példa bemutatja, hogyan használhatja a [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) attribútumot.

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Teljes példaként tekintse meg az [trigger példáját](#trigger---example).

---

## <a name="trigger---configuration"></a>Trigger – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `HttpTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **type** | –| Kötelező – `httpTrigger`értékre kell állítani. |
| **direction** | –| Kötelező – `in`értékre kell állítani. |
| **név** | –| Kötelező – a kérelem vagy a kérelem törzse függvény kódjában használt változó neve. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Meghatározza, hogy a függvény meghívásához szükség van-e a kulcsokra, ha vannak ilyenek. Az engedélyezési szint az alábbi értékek egyike lehet: <ul><li><code>anonymous</code>&mdash;nincs szükség API-kulcsra.</li><li><code>function</code>&mdash;egy függvény-specifikus API-kulcsot kell megadni. Ez az alapértelmezett érték, ha nincs megadva.</li><li><code>admin</code>&mdash;a főkulcs megadása kötelező.</li></ul> További információt az [engedélyezési kulcsok](#authorization-keys)című szakaszban talál. |
| **módszerek** |**Módszerek** | A függvény által válaszoló HTTP-metódusok tömbje. Ha nincs megadva, a függvény az összes HTTP-metódusra válaszol. Lásd: [a http-végpont testreszabása](#customize-the-http-endpoint). |
| **útvonal** | **Útvonal** | Meghatározza azt az útválasztási sablont, amely azt szabályozza, hogy a függvény milyen URL-címeket válaszol. Az alapértelmezett érték, ha nincs megadva, `<functionname>`. További információ: [a http-végpont testreszabása](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Csak az 1. x verziójú futtatókörnyezet esetében támogatott._<br/><br/>Konfigurálja a HTTP-triggert, amely [webhook](https://en.wikipedia.org/wiki/Webhook) -fogadóként működik a megadott szolgáltatónál. Ha ezt a tulajdonságot beállítja, ne állítsa be a `methods` tulajdonságot. A webhook típusa a következő értékek egyike lehet:<ul><li>a <code>genericJson</code>egy általános célú webhook-végpontot &mdash;egy adott szolgáltató logikája nélkül. Ez a beállítás csak a HTTP POST és a `application/json` tartalomtípus használatával korlátozza a kérelmeket.</li><li><code>github</code>&mdash;a függvény válaszol a [GitHub-webhookokra](https://developer.github.com/webhooks/). Ne használja a _authLevel_ tulajdonságot GitHub-webhookokkal. További információt a cikk későbbi, a GitHub-webhookok című szakaszában talál.</li><li><code>slack</code>&mdash;a függvény válaszol a [Slack webhookokra](https://api.slack.com/outgoing-webhooks). Ne használja a _authLevel_ tulajdonságot Slack webhookokkal. További információt a cikk későbbi, a Slack webhookok című szakaszában talál.</li></ul>|

## <a name="trigger---usage"></a>Trigger – használat

Az trigger bemeneti típusa `HttpRequest` vagy egyéni típusként van deklarálva. Ha a `HttpRequest`lehetőséget választja, teljes hozzáférést kap a kérelem objektumhoz. Egyéni típus esetén a futásidejű megpróbálja elemezni a JSON-kérés törzsét az objektum tulajdonságainak beállításához.

### <a name="customize-the-http-endpoint"></a>A HTTP-végpont testreszabása

Alapértelmezés szerint a HTTP-triggerekhez tartozó függvény létrehozásakor a függvény címezhető az űrlap egy útvonalával:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Ezt az útvonalat a HTTP-trigger bemeneti kötésének opcionális `route` tulajdonságával is testreszabhatja. Példaként a következő *function. JSON* fájl definiál egy `route` tulajdonságot egy http-triggerhez:

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
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Ez lehetővé teszi, hogy a függvény programkódja támogassa a címben, a _kategóriában_ és az _azonosítóban_szereplő két paramétert.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A paraméterekkel bármilyen [webes API Route-korlátozást](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) használhat. A következő C# függvény kód mindkét paraméter használatát teszi lehetővé.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

A paraméterekkel bármilyen [webes API Route-korlátozást](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) használhat. A következő C# függvény kód mindkét paraméter használatát teszi lehetővé.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A csomópontban a functions Runtime a `context` objektumból biztosítja a kérés törzsét. További információ: [JavaScript trigger példa](#trigger---example).

Az alábbi példa bemutatja, hogyan olvashatja el a `context.bindingData`útvonal-paramétereit.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

A függvény végrehajtási környezete `func.HttpRequest`ként deklarált paraméterrel elérhető. Ez a példány lehetővé teszi a függvény számára az adatútvonal-paraméterek, a lekérdezési karakterlánc-értékek és a HTTP-válaszok visszaadását lehetővé tevő metódusok elérését.

A meghatározást követően az útvonal paraméterei elérhetők a függvény számára a `route_params` metódus meghívásával.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="javatabjava"></a>[Java](#tab/java)

A függvény végrehajtási környezete a `HttpTrigger` attribútumban deklarált tulajdonságok. Az attribútum lehetővé teszi az útválasztási paraméterek, az engedélyezési szintek, a HTTP-műveletek és a bejövő kérelmek példányának definiálását.

Az útvonal paramétereinek meghatározása a `HttpTrigger` attribútum használatával történik.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Alapértelmezés szerint az összes függvény útvonala az *API*-val előtaggal van ellátva. Az előtagot a [Host. JSON](functions-host-json.md) fájl `http.routePrefix` tulajdonságának használatával is testreszabhatja vagy eltávolíthatja. Az alábbi példa eltávolítja az *API* Route előtagot egy üres karakterlánc használatával a *Host. JSON* fájlban lévő előtaghoz.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="using-route-parameters"></a>Útvonal-paraméterek használata

A függvény `route` mintázatát definiáló útválasztási paraméterek minden kötés számára elérhetők. Ha például egy olyan útvonal van meghatározva, amely `"route": "products/{id}"`, akkor a tábla tárolási kötése használhatja a `{id}` paraméter értékét a kötési konfigurációban.

A következő konfiguráció azt mutatja be, hogyan adja át a `{id}` paramétert a kötés `rowKey`.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```


### <a name="working-with-client-identities"></a>Ügyfél-identitások használata

Ha a Function alkalmazás [app Service hitelesítés/engedélyezés](../app-service/overview-authentication-authorization.md)funkciót használja, megtekintheti a kód alapján a hitelesített ügyfelek adatait. Ez [az információ a platform által befecskendezett kérelem fejlécként](../app-service/app-service-authentication-how-to.md#access-user-claims)érhető el. 

Ezeket az információkat a kötési adatokból is elolvashatja. Ez a funkció csak a functions Runtime 2. x vagy újabb verziójában érhető el. Emellett jelenleg csak a .NET nyelveken érhető el.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A hitelesített ügyfelekkel kapcsolatos információk [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)érhetők el. A ClaimsPrincipal a kérelem környezetének részeként érhető el, ahogy az az alábbi példában is látható:

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

A hitelesített ügyfelekkel kapcsolatos információk [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)érhetők el. A ClaimsPrincipal a kérelem környezetének részeként érhető el, ahogy az az alábbi példában is látható:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="pythontabpython"></a>[Python](#tab/python)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="javatabjava"></a>[Java](#tab/java)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

---

### <a name="authorization-keys"></a>Engedélyezési kulcsok

A függvények lehetővé teszik a kulcsok használatát, hogy a fejlesztés során megnehezíti a HTTP-függvény végpontjának elérését.  A szabványos HTTP-triggerek esetében előfordulhat, hogy ilyen API-kulcsra van szükség a kérelemben. 

> [!IMPORTANT]
> Míg a kulcsok segíthetnek a HTTP-végpontok kiépítésében a fejlesztés során, nem céljuk a HTTP-triggerek védelme az éles környezetben. További információ: [http-végpont biztonságossá tétele éles](#secure-an-http-endpoint-in-production)környezetben.

> [!NOTE]
> A functions 1. x futtatókörnyezetben a webhook-szolgáltatók több módon is használhatják a kérelmeket, attól függően, hogy mit támogat a szolgáltató. Ezt a [webhookok és kulcsok](#webhooks-and-keys)tartalmazzák. A 2. x vagy újabb verzióban található functions futtatókörnyezet nem tartalmazza a webhook-szolgáltatók beépített támogatását.

A kulcsok két típusa létezik:

* **Gazdagép kulcsai**: ezeket a kulcsokat a Function alkalmazásban található összes függvény megosztja. API-kulcsként való használata esetén ezek a függvények a Function alkalmazáson belüli bármelyik függvényhez hozzáférhetnek.
* **Funkcióbillentyűk**: ezek a kulcsok csak azokra a függvényekre érvényesek, amelyekben definiálva vannak. API-kulcsként való használata esetén ezek csak a funkció elérését teszik lehetővé.

A rendszer az egyes kulcsokat hivatkozásként nevezi el, és az alapértelmezett kulcs ("default") szerepel a függvény és a gazdagép szintjén. A függvények kulcsai elsőbbséget élveznek a gazdagép kulcsaival szemben. Ha két kulcs van definiálva ugyanazzal a névvel, a rendszer mindig a függvény kulcsát használja.

Minden Function alkalmazásnak van egy speciális **főkulcsa**is. Ez a kulcs egy `_master`nevű gazdagép-kulcs, amely rendszergazdai hozzáférést biztosít a futásidejű API-khoz. Ezt a kulcsot nem lehet visszavonni. Ha `admin`engedélyezési szintjét állítja be, a kérelmeknek a főkulcsot kell használniuk; minden más kulcs engedélyezési hibát eredményez.

> [!CAUTION]  
> A főkulcs által biztosított Function app emelt szintű engedélyei miatt ne ossza meg ezt a kulcsot harmadik felekkel, vagy terjessze azt natív ügyfélalkalmazások számára. A rendszergazdai jogosultsági szint kiválasztásakor legyen körültekintő.

### <a name="obtaining-keys"></a>Kulcsok beszerzése

A kulcsok tárolása az Azure-beli Function-alkalmazás részeként történik, és a rendszer a nyugalmi állapotban van titkosítva. Ha meg szeretné tekinteni a kulcsokat, újakat kell létrehoznia, vagy új értékekre kell visszagörgetni a kulcsokat, navigáljon a [Azure Portal](https://portal.azure.com) valamelyik http-triggerrel elindított függvényéhez, és válassza a **kezelés**lehetőséget.

![A funkcióbillentyűk kezelése a portálon.](./media/functions-bindings-http-webhook/manage-function-keys.png)

A [Kulcskezelő API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)-kkal programozott módon szerezheti be a függvények kulcsait.

### <a name="api-key-authorization"></a>API-kulcs engedélyezése

A legtöbb HTTP-trigger sablonhoz API-kulcs szükséges a kérelemben. Így a HTTP-kérelem általában a következő URL-címhez hasonlít:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A kulcs a fentiekben `code`nevű lekérdezési karakterlánc-változóban is szerepelhet. Egy `x-functions-key` HTTP-fejlécben is szerepelhet. A kulcs értéke lehet a függvényhez definiált bármelyik funkcióbillentyű, vagy bármely gazdagép kulcsa.

Engedélyezheti a névtelen kérelmeket, amelyek nem igénylik a kulcsokat. Azt is megkövetelheti, hogy a főkulcs legyen használatban. Az alapértelmezett engedélyezési szintet a kötés JSON `authLevel` tulajdonságának használatával módosíthatja. További információ: [trigger-Configuration](#trigger---configuration).

> [!NOTE]
> A függvények helyi futtatásakor az engedélyezés le van tiltva a megadott engedélyezési szint beállításától függetlenül. Az Azure-ba való közzététel után a rendszer kikényszeríti a trigger `authLevel` beállítását. A kulcsokra továbbra is szükség van [, ha a tárolóban helyileg](functions-create-function-linux-custom-image.md#run-the-image-locally)fut.


### <a name="secure-an-http-endpoint-in-production"></a>HTTP-végpont biztonságossá tétele éles környezetben

Az üzemi végpontok éles környezetben történő teljes biztonságossá tételéhez érdemes megfontolni a következő Function szintű biztonsági beállítások egyikének megvalósítását:

* Kapcsolja be App Service hitelesítés/engedélyezés funkciót a Function alkalmazáshoz. A App Service platform lehetővé teszi, hogy az ügyfelek hitelesítéséhez Azure Active Directory (HRE) és számos harmadik féltől származó identitás-szolgáltatót használjon. Ezt használhatja a függvények egyéni engedélyezési szabályainak megvalósításához, és a függvény kódjából is dolgozhat a felhasználói adatokkal. További információ: [hitelesítés és engedélyezés Azure app Service](../app-service/overview-authentication-authorization.md) és [ügyfél-identitások használata](#working-with-client-identities).

* Az Azure API Management (APIM) használatával hitelesítheti a kérelmeket. A APIM számos API-biztonsági beállítást biztosít a bejövő kérelmekhez. További tudnivalók: [API Management hitelesítési házirendek](../api-management/api-management-authentication-policies.md). A APIM használatával beállíthatja, hogy a Function alkalmazás csak a APIM-példány IP-címéről fogadjon kérelmeket. További információt az [IP-címek korlátozásai](ip-addresses.md#ip-address-restrictions)című témakörben talál.

* A Function alkalmazás üzembe helyezése egy Azure App Service Environment (bevezetési). A bevezetési szolgáltatás dedikált üzemeltetési környezetet biztosít a függvények futtatásához. A bevezetője lehetővé teszi egyetlen előtér-átjáró konfigurálását, amely az összes bejövő kérelem hitelesítésére használható. További információ: [webalkalmazási tűzfal (WAF) konfigurálása app Service Environmenthoz](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Ha ezen alkalmazás-szintű biztonsági módszerek egyikét használja, a HTTP-triggert használó függvény engedélyezési szintjét `anonymous`értékre kell állítani.

### <a name="webhooks"></a>Webhookok

> [!NOTE]
> A webhook mód csak a functions futtatókörnyezet 1. x verziójához érhető el. Ez a változás a 2. x vagy újabb verziójú HTTP-eseményindítók teljesítményének javítása érdekében történt.

Az 1. x verzióban a webhook-sablonok további ellenőrzéseket biztosítanak a webhook hasznos adataihoz. A 2. x vagy újabb verzióban az alap HTTP-trigger továbbra is működik, és a webhookok ajánlott megközelítése. 

#### <a name="github-webhooks"></a>GitHub-webhookok

A GitHub-webhookokra való reagáláshoz először hozza létre a függvényt egy HTTP-triggerrel, és állítsa a **webHookType** tulajdonságot `github`értékre. Ezután másolja a saját URL-címét és API-kulcsát a GitHub-adattár **Hozzáadás webhook** lapjára. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Slack-webhookok

A Slack webhook létrehoz egy jogkivonatot, és nem adja meg az Ön által megadott értéket, ezért egy függvény-specifikus kulcsot kell konfigurálnia a biztonsági másolatból a Tartalékidőből. Lásd: [engedélyezési kulcsok](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhookok és kulcsok

A webhook-engedélyezést a webhook fogadó összetevője kezeli, amely a HTTP-trigger része, és a mechanizmus a webhook típusától függően változik. Mindegyik mechanizmus egy kulcsra támaszkodik. Alapértelmezés szerint a "default" nevű funkcióbillentyű van használatban. Ha másik kulcsot szeretne használni, konfigurálja úgy a webhook-szolgáltatót, hogy a következő módszerek egyikével küldje el a kulcs nevét a kérelemben:

* **Lekérdezési karakterlánc**: a szolgáltató a `clientid` lekérdezési karakterlánc paraméterben adja át a kulcs nevét, például `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Kérelem fejléce**: a szolgáltató átadja a kulcs nevét a `x-functions-clientid` fejlécben.

## <a name="trigger---limits"></a>Trigger – korlátok

A HTTP-kérés hossza legfeljebb 100 MB (104 857 600 bájt), az URL-cím hossza pedig 4 KB (4 096 bájt) lehet. Ezeket a korlátokat a futtatókörnyezet [web. config fájljának](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)`httpRuntime` eleme határozza meg.

Ha a HTTP-triggert használó függvény 230 másodpercen belül nem fejeződik be, akkor a [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) időtúllépést jelez, és http 502-hibát ad vissza. A függvény továbbra is futni fog, de nem tud HTTP-választ adni. A hosszú ideig futó függvények esetében javasoljuk, hogy kövesse az aszinkron mintákat, és olyan helyet küldjön vissza, amelyben pingelheti a kérés állapotát. További információ a függvények futtatásának időtartamáról: [skálázás és üzemeltetés – fogyasztási terv](functions-scale.md#timeout).

## <a name="output"></a>Kimenet

A http-kimeneti kötés használatával válaszolhat a HTTP-kérelem feladójának. A kötéshez HTTP-triggerre van szükség. Lehetővé teszi a trigger kéréséhez társított válasz testreszabását. Ha nincs megadva HTTP-kimeneti kötés, a http-trigger a HTTP 200 OK értéket adja vissza egy üres törzstel az 1. x függvényben, vagy a HTTP 204 nem tartalmaz üres törzstel rendelkező tartalmat a 2. x és újabb függvényeknél.

## <a name="output---configuration"></a>Kimenet – konfiguráció

A következő táblázat a *function. JSON* fájlban beállított kötési konfigurációs tulajdonságokat ismerteti. Az C# osztályok könyvtáraiban nincsenek a *function. JSON* -tulajdonságokkal egyező attribútumok.

|Tulajdonság  |Leírás  |
|---------|---------|
| **type** |`http`értékre kell állítani. |
| **direction** | `out`értékre kell állítani. |
| **név** | A válasz kódjában használt változó neve, vagy `$return` a visszatérési érték használatához. |

## <a name="output---usage"></a>Kimenet – használat

HTTP-válasz küldéséhez használja a Language-standard Response mintákat. Vagy C# C# parancsfájl esetén a függvény visszatérési típusának `IActionResult` vagy `Task<IActionResult>`kell lennie. A C#-ben a Return Value attribútum nem kötelező.

A válaszokat például az [trigger példája](#trigger---example)tartalmazza.

## <a name="hostjson-settings"></a>gazdagép. JSON-beállítások

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. A következő példa a Host. JSON fájlt tartalmazza, csak a 2. x + beállításokat a kötéshez. További információ a 2. x verzióban található globális konfigurációs beállításokról: a [Host. JSON dokumentációja Azure functions](functions-host-json.md).

> [!NOTE]
> Az 1. x függvények Host. JSON fájljának hivatkozását lásd: [Host. JSON-dokumentáció Azure functions 1. x-hez](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
| customHeaders|Nincs|Lehetővé teszi egyéni fejlécek beállítását a HTTP-válaszban. Az előző példa hozzáadja a `X-Content-Type-Options` fejlécet a válaszhoz, hogy elkerülje a tartalomtípusok elemzését. |
|dynamicThrottlesEnabled|igaz<sup>\*</sup>|Ha engedélyezve van, ez a beállítás hatására a kérelmek feldolgozási folyamata rendszeres időközönként ellenőrzi a rendszerteljesítmény-számlálókat, például a kapcsolatok/szálak/folyamatok/memória/CPU/etc értéket. ha ezek a számlálók egy beépített magas küszöbértéken (80%) vannak, a rendszer a kérelmeket a 429 "túl elfoglalt" válaszként utasítja el, amíg a számláló (k) be nem fejeződik a<br/><sup>\*</sup> A használati terv alapértelmezett értéke `true`. A dedikált csomag alapértelmezett értéke `false`.|
|hsts|nincs engedélyezve|Ha a `isEnabled` `true`re van állítva, a [.net Core HTTP-alapú szigorú átviteli biztonsági (HSTS) viselkedése](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) kényszerítve van, ahogy az a [`HstsOptions` osztályban](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)van meghatározva. A fenti példában a [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) tulajdonságot is 10 napra állítja be. A `hsts` támogatott tulajdonságai a következők: <table><tr><th>Tulajdonság</th><th>Leírás</th></tr><tr><td>excludedHosts</td><td>Az állomásnevek olyan karakterlánc-tömbje, amelyhez nincs hozzáadva a HSTS fejléce.</td></tr><tr><td>Altartományok belefoglalása</td><td>Logikai érték, amely azt jelzi, hogy engedélyezve van-e a Strict-Transport-Security fejléc includeSubDomain paramétere.</td></tr><tr><td>maxAge</td><td>Karakterlánc, amely a Strict-Transport-Security fejléc Max-Age paraméterét határozza meg.</td></tr><tr><td>preload</td><td>Logikai érték, amely azt jelzi, hogy engedélyezve van-e a Strict-Transport-Security fejléc Preload paramétere.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|A párhuzamosan végrehajtott HTTP-függvények maximális száma. Ez lehetővé teszi a párhuzamosság szabályozását, ami segíthet az erőforrások kihasználtságának kezelésében. Előfordulhat például, hogy olyan HTTP-függvénnyel rendelkezik, amely sok rendszererőforrást (memóriát/processzort/szoftvercsatornát) használ, így problémát okoz, ha a párhuzamosság túl magas. Vagy lehet, hogy olyan függvényt használ, amely a kimenő kéréseket egy harmadik féltől származó szolgáltatásnak teszi elérhetővé, és a hívásoknak korlátozott arányban kell lenniük. Ezekben az esetekben a szabályozás alkalmazása segíthet. <br/><sup>*</sup> A felhasználási terv alapértelmezett értéke 100. Egy dedikált csomag alapértelmezett értéke nem kötött (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|A függőben lévő kérések maximális száma, amelyek egy adott időpontban vannak tárolva. Ez a korlát olyan kérelmeket tartalmaz, amelyek várólistára kerülnek, de nem indult el, valamint folyamatban van a végrehajtás. Az ezen a korláton túli bejövő kérelmek elutasítása egy 429 "túl elfoglalt" választ tartalmaz. Ez lehetővé teszi, hogy a hívók időalapú újrapróbálkozási stratégiákat alkalmazzanak, és segítséget nyújt a kérelmek maximális késésének szabályozásához is. Ez csak a parancsfájl-gazdagép végrehajtási útvonalán belüli üzenetsor-kezelőt vezérli. Más várólisták, például a ASP.NET kérelmek várólistája továbbra is érvényben marad, és ezt a beállítást nem érinti. <br/><sup>\*</sup> A felhasználási terv alapértelmezett értéke 200. Egy dedikált csomag alapértelmezett értéke nem kötött (`-1`).|
|routePrefix|api-t|Az útvonal előtagja, amely az összes útvonalra vonatkozik. Az alapértelmezett előtag eltávolításához használjon üres karakterláncot. |


## <a name="next-steps"></a>Következő lépések

[További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
