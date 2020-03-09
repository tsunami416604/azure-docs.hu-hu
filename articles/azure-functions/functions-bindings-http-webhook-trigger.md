---
title: HTTP-trigger Azure Functions
description: Megtudhatja, hogyan hívhat meg egy Azure-függvényt HTTP-n keresztül.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356103"
---
# <a name="azure-functions-http-trigger"></a>HTTP-trigger Azure Functions

A HTTP-trigger lehetővé teszi a függvények HTTP-kérelemmel való meghívását. HTTP-trigger használatával kiszolgáló nélküli API-kat hozhat létre, és válaszolhat a webhookokra.

A HTTP által aktivált függvények alapértelmezett visszatérési értéke a következő:

- `HTTP 204 No Content` üres törzstel a 2. x és újabb függvényeknél
- `HTTP 200 OK` üres törzstel az 1. x függvényben

A HTTP-válasz módosításához állítson be egy [kimeneti kötést](./functions-bindings-http-webhook-output.md).

További információ a HTTP-kötésekről: [Áttekintés](./functions-bindings-http-webhook.md) és [kimeneti kötési hivatkozás](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

`HttpRequest`helyett egyéni objektumhoz is köthető. Ez az objektum a kérelem törzsében jön létre, és JSON-ként van elemezve. Hasonlóképpen egy típus is átadható a HTTP-válasz kimeneti kötéseinek, és a válasz törzsként, valamint egy `200` állapotkódot is visszaküldhető.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

# <a name="python"></a>[Python](#tab/python)

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

# <a name="java"></a>[Java](#tab/java)

* [Paraméter olvasása a lekérdezési karakterláncból](#read-parameter-from-the-query-string)
* [Szövegtörzs beolvasása POST-kérelemből](#read-body-from-a-post-request)
* [Paraméter olvasása útvonalból](#read-parameter-from-a-route)
* [POJO-törzs olvasása POST-kérelemből](#read-pojo-body-from-a-post-request)

A következő példák a HTTP-trigger kötését mutatják be.

#### <a name="read-parameter-from-the-query-string"></a>Paraméter olvasása a lekérdezési karakterláncból

Ez a példa egy `id`nevű paramétert olvas be a lekérdezési karakterláncból, és felhasználja az ügyfélnek visszaadott JSON-dokumentum létrehozásához a tartalomtípus `application/json`.

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

Ez a példa egy POST-kérelem törzsét olvassa be `String`ként, és felhasználja az ügyfélnek visszaadott JSON-dokumentum létrehozásához a tartalomtípus `application/json`.

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

Ez a példa egy `id`nevű kötelező paramétert olvas be, valamint egy opcionális paramétert `name` az útvonal elérési útjából, és felhasználja őket egy, az ügyfélnek visszaadott JSON-dokumentum létrehozásához a tartalomtípus `application/json`. T

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

A `ToDoItem` osztályhoz tartozó kód az alábbi példában hivatkozott:

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

Ez a példa egy POST-kérelem törzsét olvassa be. A kérés törzse automatikusan le lesz szerializálva egy `ToDoItem` objektumba, és a rendszer visszaküldi az ügyfélnek a tartalom típusa `application/json`. A functions futtatókörnyezet szerializálja a `ToDoItem` paramétert, mivel az a `HttpMessageResponse.Builder` osztály `body` tulajdonságához van rendelve.

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

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

Az [ C# osztály könyvtáraiban](functions-dotnet-class-library.md) és a Java-ban a függvény konfigurálásához a `HttpTrigger` attribútum érhető el.

Beállíthatja az engedélyezési szintet és a HTTP-metódusokat az attribútumok konstruktorának paraméterei, a webhook típusa és egy útvonal-sablon számára. További információ ezekről a beállításokról: [konfiguráció](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Ez a példa bemutatja, hogyan használhatja a [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribútumot.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Teljes példaként tekintse meg az [trigger példáját](#example).

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

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

Teljes példaként tekintse meg az [trigger példáját](#example).

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `HttpTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **type** | n/a| Kötelező – `httpTrigger`értékre kell állítani. |
| **direction** | n/a| Kötelező – `in`értékre kell állítani. |
| **név** | n/a| Kötelező – a kérelem vagy a kérelem törzse függvény kódjában használt változó neve. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Meghatározza, hogy a függvény meghívásához szükség van-e a kulcsokra, ha vannak ilyenek. Az engedélyezési szint az alábbi értékek egyike lehet: <ul><li><code>anonymous</code>&mdash;nincs szükség API-kulcsra.</li><li><code>function</code>&mdash;egy függvény-specifikus API-kulcsot kell megadni. Ez az alapértelmezett érték, ha nincs megadva.</li><li><code>admin</code>&mdash;a főkulcs megadása kötelező.</li></ul> További információt az [engedélyezési kulcsok](#authorization-keys)című szakaszban talál. |
| **módszerek** |**Módszerek** | A függvény által válaszoló HTTP-metódusok tömbje. Ha nincs megadva, a függvény az összes HTTP-metódusra válaszol. Lásd: [a http-végpont testreszabása](#customize-the-http-endpoint). |
| **útvonal** | **Útvonal** | Meghatározza azt az útválasztási sablont, amely azt szabályozza, hogy a függvény milyen URL-címeket válaszol. Az alapértelmezett érték, ha nincs megadva, `<functionname>`. További információ: [a http-végpont testreszabása](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Csak az 1. x verziójú futtatókörnyezet esetében támogatott._<br/><br/>Konfigurálja a HTTP-triggert, amely [webhook](https://en.wikipedia.org/wiki/Webhook) -fogadóként működik a megadott szolgáltatónál. Ha ezt a tulajdonságot beállítja, ne állítsa be a `methods` tulajdonságot. A webhook típusa a következő értékek egyike lehet:<ul><li>a <code>genericJson</code>egy általános célú webhook-végpontot &mdash;egy adott szolgáltató logikája nélkül. Ez a beállítás csak a HTTP POST és a `application/json` tartalomtípus használatával korlátozza a kérelmeket.</li><li><code>github</code>&mdash;a függvény válaszol a [GitHub-webhookokra](https://developer.github.com/webhooks/). Ne használja a _authLevel_ tulajdonságot GitHub-webhookokkal. További információt a cikk későbbi, a GitHub-webhookok című szakaszában talál.</li><li><code>slack</code>&mdash;a függvény válaszol a [Slack webhookokra](https://api.slack.com/outgoing-webhooks). Ne használja a _authLevel_ tulajdonságot Slack webhookokkal. További információt a cikk későbbi, a Slack webhookok című szakaszában talál.</li></ul>|

## <a name="payload"></a>Adattartalom

Az trigger bemeneti típusa `HttpRequest` vagy egyéni típusként van deklarálva. Ha a `HttpRequest`lehetőséget választja, teljes hozzáférést kap a kérelem objektumhoz. Egyéni típus esetén a futásidejű megpróbálja elemezni a JSON-kérés törzsét az objektum tulajdonságainak beállításához.

## <a name="customize-the-http-endpoint"></a>A HTTP-végpont testreszabása

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

Ez a konfiguráció lehetővé teszi, hogy a függvény programkódja támogassa a címben, a _kategóriában_ és az _azonosítóban_szereplő két paramétert.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A csomópontban a functions Runtime a `context` objektumból biztosítja a kérés törzsét. További információ: [JavaScript trigger példa](#example).

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="using-route-parameters"></a>Útvonal-paraméterek használata

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

## <a name="working-with-client-identities"></a>Ügyfél-identitások használata

Ha a Function alkalmazás [app Service hitelesítés/engedélyezés](../app-service/overview-authentication-authorization.md)funkciót használja, megtekintheti a kód alapján a hitelesített ügyfelek adatait. Ez [az információ a platform által befecskendezett kérelem fejlécként](../app-service/app-service-authentication-how-to.md#access-user-claims)érhető el. 

Ezeket az információkat a kötési adatokból is elolvashatja. Ez a funkció csak a functions Runtime 2. x vagy újabb verziójában érhető el. Emellett jelenleg csak a .NET nyelveken érhető el.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="python"></a>[Python](#tab/python)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="java"></a>[Java](#tab/java)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

---

## <a name="authorization-keys"></a>Engedélyezési kulcsok

A függvények lehetővé teszik a kulcsok használatát, hogy a fejlesztés során megnehezíti a HTTP-függvény végpontjának elérését.  Ha a http-engedélyezési szint a HTTP-triggert használó függvényben `anonymous`értékre van állítva, a kérelmeknek tartalmaznia kell egy API-kulcsot a kérelemben. 

> [!IMPORTANT]
> Míg a kulcsok segíthetnek a HTTP-végpontok kiépítésében a fejlesztés során, nem céljuk a HTTP-triggerek védelme az éles környezetben. További információ: [http-végpont biztonságossá tétele éles](#secure-an-http-endpoint-in-production)környezetben.

> [!NOTE]
> A functions 1. x futtatókörnyezetben a webhook-szolgáltatók több módon is használhatják a kérelmeket, attól függően, hogy mit támogat a szolgáltató. Ezt a [webhookok és kulcsok](#webhooks-and-keys)tartalmazzák. A 2. x vagy újabb verzióban található functions futtatókörnyezet nem tartalmazza a webhook-szolgáltatók beépített támogatását.

#### <a name="authorization-scopes-function-level"></a>Engedélyezési hatókörök (Function-Level)

A függvény szintű kulcsok két engedélyezési hatókörrel rendelkeznek:

* **Function**: ezek a kulcsok csak azokra a függvényekre érvényesek, amelyekben definiálva vannak. API-kulcsként való használata esetén ezek csak a funkció elérését teszik lehetővé.

* **Gazdagép**: a gazdagép hatókörével rendelkező kulcsok használhatók a Function alkalmazásban található összes funkció eléréséhez. API-kulcsként való használata esetén ezek a függvények a Function alkalmazáson belüli bármelyik függvényhez hozzáférhetnek. 

A rendszer az egyes kulcsokat hivatkozásként nevezi el, és az alapértelmezett kulcs ("default") szerepel a függvény és a gazdagép szintjén. A függvények kulcsai elsőbbséget élveznek a gazdagép kulcsaival szemben. Ha két kulcs van definiálva ugyanazzal a névvel, a rendszer mindig a függvény kulcsát használja.

#### <a name="master-key-admin-level"></a>Főkulcs (rendszergazda szintű) 

Minden Function alkalmazáshoz tartozik egy `_master`nevű rendszergazdai szintű gazda-kulcs is. Amellett, hogy a gazdagép szintű hozzáférést biztosít az alkalmazás összes függvényéhez, a főkulcs rendszergazdai hozzáférést is biztosít a futásidejű REST API-khoz. Ezt a kulcsot nem lehet visszavonni. Ha `admin`engedélyezési szintjét állítja be, a kérelmeknek a főkulcsot kell használniuk; minden más kulcs engedélyezési hibát eredményez.

> [!CAUTION]  
> A főkulcs által biztosított Function app emelt szintű engedélyei miatt ne ossza meg ezt a kulcsot harmadik felekkel, vagy terjessze azt natív ügyfélalkalmazások számára. A rendszergazdai jogosultsági szint kiválasztásakor legyen körültekintő.

## <a name="obtaining-keys"></a>Kulcsok beszerzése

A kulcsok tárolása az Azure-beli Function-alkalmazás részeként történik, és a rendszer a nyugalmi állapotban van titkosítva. Ha meg szeretné tekinteni a kulcsokat, újakat kell létrehoznia, vagy új értékekre kell visszagörgetni a kulcsokat, navigáljon a [Azure Portal](https://portal.azure.com) valamelyik http-triggerrel elindított függvényéhez, és válassza a **kezelés**lehetőséget.

![A funkcióbillentyűk kezelése a portálon.](./media/functions-bindings-http-webhook/manage-function-keys.png)

A [Kulcskezelő API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)-kkal programozott módon szerezheti be a függvények kulcsait.

## <a name="api-key-authorization"></a>API-kulcs engedélyezése

A legtöbb HTTP-trigger sablonhoz API-kulcs szükséges a kérelemben. Így a HTTP-kérelem általában a következő URL-címhez hasonlít:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A kulcs a fentiekben `code`nevű lekérdezési karakterlánc-változóban is szerepelhet. Egy `x-functions-key` HTTP-fejlécben is szerepelhet. A kulcs értéke lehet a függvényhez definiált bármelyik funkcióbillentyű, vagy bármely gazdagép kulcsa.

Engedélyezheti a névtelen kérelmeket, amelyek nem igénylik a kulcsokat. Azt is megkövetelheti, hogy a főkulcs használatban legyen. Az alapértelmezett engedélyezési szintet a kötés JSON `authLevel` tulajdonságának használatával módosíthatja. További információ: [trigger-Configuration](#configuration).

> [!NOTE]
> A függvények helyi futtatásakor az engedélyezés le van tiltva a megadott engedélyezési szint beállításától függetlenül. Az Azure-ba való közzététel után a rendszer kikényszeríti a trigger `authLevel` beállítását. A kulcsokra továbbra is szükség van [, ha a tárolóban helyileg](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)fut.


## <a name="secure-an-http-endpoint-in-production"></a>HTTP-végpont biztonságossá tétele éles környezetben

Az üzemi végpontok éles környezetben történő teljes biztonságossá tételéhez érdemes megfontolni a következő Function szintű biztonsági beállítások egyikének megvalósítását:

* Kapcsolja be App Service hitelesítés/engedélyezés funkciót a Function alkalmazáshoz. A App Service platform lehetővé teszi, hogy az ügyfelek hitelesítéséhez Azure Active Directory (HRE) és számos harmadik féltől származó identitás-szolgáltatót használjon. Ezt a stratégiát használhatja a függvények egyéni engedélyezési szabályainak megvalósításához, és a függvény kódjából is dolgozhat a felhasználói adatokkal. További információ: [hitelesítés és engedélyezés Azure app Service](../app-service/overview-authentication-authorization.md) és [ügyfél-identitások használata](#working-with-client-identities).

* Az Azure API Management (APIM) használatával hitelesítheti a kérelmeket. A APIM számos API-biztonsági beállítást biztosít a bejövő kérelmekhez. További tudnivalók: [API Management hitelesítési házirendek](../api-management/api-management-authentication-policies.md). A APIM használatával beállíthatja, hogy a Function alkalmazás csak a APIM-példány IP-címéről fogadjon kérelmeket. További információt az [IP-címek korlátozásai](ip-addresses.md#ip-address-restrictions)című témakörben talál.

* A Function alkalmazás üzembe helyezése egy Azure App Service Environment (bevezetési). A bevezetési szolgáltatás dedikált üzemeltetési környezetet biztosít a függvények futtatásához. A bevezetője lehetővé teszi egyetlen előtér-átjáró konfigurálását, amely az összes bejövő kérelem hitelesítésére használható. További információ: [webalkalmazási tűzfal (WAF) konfigurálása app Service Environmenthoz](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Ha ezen alkalmazás-szintű biztonsági módszerek egyikét használja, a HTTP-triggert használó függvény engedélyezési szintjét `anonymous`értékre kell állítani.

## <a name="webhooks"></a>Webhookok

> [!NOTE]
> A webhook mód csak a functions futtatókörnyezet 1. x verziójához érhető el. Ez a változás a 2. x vagy újabb verziójú HTTP-eseményindítók teljesítményének javítása érdekében történt.

Az 1. x verzióban a webhook-sablonok további ellenőrzéseket biztosítanak a webhook hasznos adataihoz. A 2. x vagy újabb verzióban az alap HTTP-trigger továbbra is működik, és a webhookok ajánlott megközelítése. 

### <a name="github-webhooks"></a>GitHub-webhookok

A GitHub-webhookokra való reagáláshoz először hozza létre a függvényt egy HTTP-triggerrel, és állítsa a **webHookType** tulajdonságot `github`értékre. Ezután másolja a saját URL-címét és API-kulcsát a GitHub-adattár **Hozzáadás webhook** lapjára. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack-webhookok

A Slack webhook létrehoz egy jogkivonatot, és nem adja meg az Ön által megadott értéket, ezért egy függvény-specifikus kulcsot kell konfigurálnia a biztonsági másolatból a Tartalékidőből. Lásd: [engedélyezési kulcsok](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhookok és kulcsok

A webhook-engedélyezést a webhook fogadó összetevője kezeli, amely a HTTP-trigger része, és a mechanizmus a webhook típusától függően változik. Mindegyik mechanizmus egy kulcsra támaszkodik. Alapértelmezés szerint a "default" nevű funkcióbillentyű van használatban. Ha másik kulcsot szeretne használni, konfigurálja úgy a webhook-szolgáltatót, hogy a következő módszerek egyikével küldje el a kulcs nevét a kérelemben:

* **Lekérdezési karakterlánc**: a szolgáltató a `clientid` lekérdezési karakterlánc paraméterben adja át a kulcs nevét, például `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Kérelem fejléce**: a szolgáltató átadja a kulcs nevét a `x-functions-clientid` fejlécben.

## <a name="limits"></a>Korlátok

A HTTP-kérés hossza legfeljebb 100 MB (104 857 600 bájt), az URL-cím hossza pedig 4 KB (4 096 bájt) lehet. Ezeket a korlátokat a futtatókörnyezet [web. config fájljának](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config)`httpRuntime` eleme határozza meg.

Ha a HTTP-triggert használó függvény 230 másodpercen belül nem fejeződik be, akkor a [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) időtúllépést jelez, és http 502-hibát ad vissza. A függvény továbbra is futni fog, de nem tud HTTP-választ adni. A hosszú ideig futó függvények esetében javasoljuk, hogy kövesse az aszinkron mintákat, és olyan helyet küldjön vissza, amelyben pingelheti a kérés állapotát. További információ a függvények futtatásának időtartamáról: [skálázás és üzemeltetés – fogyasztási terv](functions-scale.md#timeout).


## <a name="next-steps"></a>Következő lépések

- [HTTP-válasz visszaadása függvényből](./functions-bindings-http-webhook-output.md)
