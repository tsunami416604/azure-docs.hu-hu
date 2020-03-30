---
title: Az Azure Functions HTTP-eseményindítója
description: Ismerje meg, hogyan hívhat meg egy Azure-függvényt HTTP-n keresztül.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277595"
---
# <a name="azure-functions-http-trigger"></a>Az Azure Functions HTTP-eseményindítója

A HTTP-eseményindító lehetővé teszi egy HTTP-kérelemmel rendelkező függvény meghívását. Http-eseményindító használatával kiszolgáló nélküli API-kat hozhat létre, és válaszolhat a webhookokra.

A HTTP által aktivált függvényalapértelmezett visszatérési értéke a következő:

- `HTTP 204 No Content`üres testtel a 2.x vagy magasabb függvényekben
- `HTTP 200 OK`üres testtel az 1.x függvényekben

A HTTP-válasz módosításához állítson be [egy kimeneti kötést.](./functions-bindings-http-webhook-output.md)

A HTTP-kötésekről további információt az [áttekintés](./functions-bindings-http-webhook.md) és a [kimenetkötéshivatkozás tartalmaz.](./functions-bindings-http-webhook-output.md)

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keres egy `name` paramétert. Figyelje meg, hogy a visszatérési érték a kimeneti kötéshez használatos, de a visszatérési érték attribútum nem szükséges.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy trigger kötést mutat be egy *function.json* fájlban, és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény megkeresi `name` a paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében.

Itt a *function.json* fájl:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kódot, `HttpRequest`amely kötődik:

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

A helyett `HttpRequest`egyéni objektumokhoz is köthető. Ez az objektum a kérelem törzséből jön létre, és JSON-ként van elemezve. Hasonlóképpen egy típus átadható a HTTP-válasz kimeneti kötés, és `200` visszaadja a válasz törzse, valamint egy állapotkód.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy *function.json* fájlban lévő eseményindító-kötést és egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely a kötést használja. A függvény megkeresi `name` a paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében.

Itt a *function.json* fájl:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

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

A következő példa egy trigger kötést mutat be egy *function.json* fájlban és egy [Python-függvényt,](functions-reference-python.md) amely a kötést használja. A függvény megkeresi `name` a paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében.

Itt a *function.json* fájl:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a Python kód:

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
* [Törzs olvasása POST-kérelemből](#read-body-from-a-post-request)
* [Paraméter olvasása útvonalból](#read-parameter-from-a-route)
* [POJO-törzs olvasása POST-kérelemből](#read-pojo-body-from-a-post-request)

A következő példák a HTTP-eseményindító kötését mutatják be.

#### <a name="read-parameter-from-the-query-string"></a>Paraméter olvasása a lekérdezési karakterláncból

Ez a példa beolvas egy paramétert, amelynek neve `id`a lekérdezési karakterláncból, és `application/json`arra használja, hogy létrehoz egy JSON-dokumentumot, amelyet az ügyfélnek a tartalomtípussal ad vissza.

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

#### <a name="read-body-from-a-post-request"></a>Törzs olvasása POST-kérelemből

Ez a példa a POST-kérelem `String`törzsét olvassa be , és azt az ügyfélnek visszaadott JSON-dokumentum létrehozásához használja. `application/json`

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

Ez a példa beolvas `id`egy kötelező `name` paramétert, a névvel elnevezett és egy nem kötelező paramétert `application/json`az útvonalútvonalból, és azokat használja az ügyfélnek visszaadott JSON-dokumentum létrehozásához, tartalomtípussal. T

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

Itt van a `ToDoItem` kód az osztály, hivatkozott ebben a példában:

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

Ez a példa egy POST-kérelem törzsét olvassa be. A kérelem törzse automatikusan leválik egy `ToDoItem` objektumba, és visszakerül `application/json`az ügyfélnek a tartalomtípussal. A `ToDoItem` paramétert a Functions futásidejű szerializálódik, mivel `HttpMessageResponse.Builder` az `body` osztály tulajdonságához van rendelve.

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

A [C# osztálykönyvtárak](functions-dotnet-class-library.md) ban `HttpTrigger` és a Java-ban az attribútum elérhető a függvény konfigurálásához.

Beállíthatja az engedélyezési szintet és a megengedett HTTP-metódusokat az attribútumkonstruktor paraméterekben, a webhook-típusban és az útvonalsablonban. Ezekről a beállításokról a [konfiguráció](#configuration)című témakörben olvashat bővebben.

# <a name="c"></a>[C #](#tab/csharp)

Ez a példa bemutatja a [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribútum használatát.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Egy teljes példa, lásd az [eseményindító példa](#example).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

Ez a példa bemutatja a [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) attribútum használatát.

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

Egy teljes példa, lásd az [eseményindító példa](#example).

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `HttpTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
| **Típus** | n/a| Kötelező - a `httpTrigger`beállításnak a beállítására kell. |
| **direction** | n/a| Kötelező - a `in`beállításnak a beállítására kell. |
| **név** | n/a| Kötelező – a kérés vagy kérés törzsének függvénykódjában használt változónév. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel (Hitelesítésszint)** |Azt határozza meg, hogy milyen kulcsoknak kell jelen lenniük a kérelemben a függvény meghívásához. Az engedélyezési szint a következő értékek egyike lehet: <ul><li><code>anonymous</code>&mdash;Nincs szükség API-kulcsra.</li><li><code>function</code>&mdash;Egy függvény-specifikus API-kulcs szükséges. Ez az alapértelmezett érték, ha nincs megadva.</li><li><code>admin</code>&mdash;A főkulcs szükséges.</li></ul> További információt az engedélyezési [kulcsokról](#authorization-keys)szóló szakaszban talál. |
| **Módszerek** |**Metódusok** | A függvény által válaszolandó HTTP-metódusok tömbje. Ha nincs megadva, a függvény minden HTTP-metódusra válaszol. Lásd: [A HTTP-végpont testreszabása](#customize-the-http-endpoint). |
| **Útvonal** | **Útválasztás** | Meghatározza az útvonalsablont, amely azt szabályozza, hogy a függvény mely kérési URL-címeket válaszolja meg. Ha nincs megadva, `<functionname>`az alapértelmezett érték a . További információt [a HTTP-végpont testreszabása](#customize-the-http-endpoint)című témakörben talál. |
| **webHookType** | **WebHookType típus** | _Csak az 1.x verziójú futásidőben támogatott._<br/><br/>A HTTP-eseményindító konfigurálása [webhook-fogadóként](https://en.wikipedia.org/wiki/Webhook) működik a megadott szolgáltató számára. Ne állítsa be `methods` a tulajdonságot, ha ezt a tulajdonságot állítja be. A webhook-típus a következő értékek egyike lehet:<ul><li><code>genericJson</code>&mdash;Általános célú webhook-végpont logika nélkül egy adott szolgáltató. Ez a beállítás csak a HTTP POST `application/json` protokollt használó és a tartalomtípussal rendelkezőkre korlátozza a kérelmeket.</li><li><code>github</code>&mdash;A függvény válaszol a [GitHub webhooks](https://developer.github.com/webhooks/). Ne használja az _authLevel_ tulajdonságot a GitHub webhookokkal. További információkért tekintse meg a GitHub webhooks szakasz a cikk későbbi részében.</li><li><code>slack</code>&mdash;A függvény válaszol [a Slack webhooks](https://api.slack.com/outgoing-webhooks). Ne használja az _authLevel_ tulajdonságot a Slack webhooks használatával. További információkért tekintse meg a Slack webhooks szakasz a cikk későbbi részében.</li></ul>|

## <a name="payload"></a>Hasznos teher

Az eseményindító bemeneti típusa `HttpRequest` deklarálva vagy egyéni típusként deklarálva van. Ha úgy `HttpRequest`dönt, teljes hozzáférést kap a kérelemobjektumhoz. Egyéni típus esetén a futásidejű megpróbálja elemezni a JSON-kérelem törzsét az objektum tulajdonságainak beállításához.

## <a name="customize-the-http-endpoint"></a>A HTTP-végpont testreszabása

Alapértelmezés szerint, ha egy HTTP-eseményindítóhoz hoz létre függvényt, a függvény az űrlap útvonalával címezhető:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Ezt az útvonalat a `route` HTTP-eseményindító bemeneti kötésén található választható tulajdonság használatával szabhatja testre. A következő *function.json* fájl például `route` egy HTTP-eseményindító tulajdonságát határozza meg:

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

Ezzel a konfigurációval a függvény mostmár címezhető a következő útvonal helyett az eredeti útvonalon.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Ez a konfiguráció lehetővé teszi, hogy a függvénykód két paramétert támogasson a címben, _a kategóriában_ és az _id-ben._

# <a name="c"></a>[C #](#tab/csharp)

Bármilyen webes [API-útvonalmegkötést](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) használhat a paraméterekkel. A következő C# függvénykód mindkét paramétert használja.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Bármilyen webes [API-útvonalmegkötést](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) használhat a paraméterekkel. A következő C# függvénykód mindkét paramétert használja.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A Csomópontban a Functions futásidejű biztosítja `context` a kérelem törzsét az objektumból. További információt a [JavaScript-eseményindító példában](#example)talál.

A következő példa bemutatja, hogyan `context.bindingData`lehet beolvasni az útvonalparamétereit a alkalmazásból.

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

A függvény végrehajtási környezete a rendszer `func.HttpRequest`a rendszerre deklarált paraméteren keresztül teszi elérhetővé. Ez a példány lehetővé teszi, hogy egy függvény hozzáférjen az adatútvonal-paraméterekhez, a lekérdezési karakterlánc-értékekhez és metódusokhoz, amelyek lehetővé teszik a HTTP-válaszok visszaadását.

A definiálás után az útvonalparaméterek a metódus `route_params` hívásával érhetők el a függvény számára.

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

A függvény végrehajtási környezete az `HttpTrigger` attribútumban deklarált tulajdonságok. Az attribútum lehetővé teszi az útvonalparaméterek, az engedélyezési szintek, a HTTP-műveletek és a bejövő kérelempéldány meghatározását.

Az útvonalparaméterek az `HttpTrigger` attribútumon keresztül vannak definiálva.

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

Alapértelmezés szerint az összes függvényútvonal *api*api előtaggal van rögzítve. Az előtagot a `http.routePrefix` [host.json](functions-host-json.md) fájlban lévő tulajdonsággal is testreszabhatja vagy eltávolíthatja. A következő példa eltávolítja az *API-útvonal* előtagot egy üres karakterlánc használatával a *host.json* fájlban lévő előtaghoz.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Útvonalparaméterek használata

A függvény mintáját `route` meghatározó útvonalparaméterek minden kötéshez elérhetők. Ha például egy útvonalat definiált, `"route": "products/{id}"` akkor egy táblatárolási kötés `{id}` használhatja a paraméter értékét a kötési konfigurációban.

A következő konfiguráció `{id}` azt mutatja, hogy a `rowKey`paraméter hogyan kerül átadásra a kötés .

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

## <a name="working-with-client-identities"></a>Az ügyfélidentitások közös feladata

Ha a függvényalkalmazás [az App Service hitelesítése / engedélyezése](../app-service/overview-authentication-authorization.md)szolgáltatást használja, a kódból megtekintheti a hitelesített ügyfelek adatait. Ez az információ [a platform által bevitt kérésfejlécek](../app-service/app-service-authentication-how-to.md#access-user-claims)formájában érhető el. 

Ezt az információt a kötési adatokból is elolvashatja. Ez a funkció csak a 2.x-es vagy újabb verzióban futó Funkciók számára érhető el. Jelenleg is csak .NET nyelvekesetén érhető el.

# <a name="c"></a>[C #](#tab/csharp)

A hitelesített ügyfelekre vonatkozó információk [jogfelelősként](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)érhetők el. A ClaimsPrincipal a kérelemkörnyezet részeként érhető el, ahogy az a következő példában látható:

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

Másik lehetőségként a ClaimsPrincipal egyszerűen beilleszthető további paraméterként a függvényaláírásba:

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A hitelesített ügyfelekre vonatkozó információk [jogfelelősként](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)érhetők el. A ClaimsPrincipal a kérelemkörnyezet részeként érhető el, ahogy az a következő példában látható:

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

Másik lehetőségként a ClaimsPrincipal egyszerűen beilleszthető további paraméterként a függvényaláírásba:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A hitelesített felhasználó [HTTP-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="python"></a>[Python](#tab/python)

A hitelesített felhasználó [HTTP-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="java"></a>[Java](#tab/java)

A hitelesített felhasználó [HTTP-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

---

## <a name="authorization-keys"></a>Engedélyezési kulcsok

A Függvények lehetővé teszik a kulcsok használatát, hogy a fejlesztés során megnehezítse a HTTP-függvényvégpontok elérését.  Kivéve, ha a HTTP-engedélyezési szint `anonymous`egy HTTP-aktivált függvény van beállítva, a kérelmek tartalmaznia kell egy API-kulcsot a kérelemben. 

> [!IMPORTANT]
> Míg a kulcsok segíthetnek a HTTP-végpontok elhomályosításában a fejlesztés során, nem célja, hogy biztosítsa a HTTP-eseményindító éles környezetben. További információ: [Http-végpont védelme éles környezetben.](#secure-an-http-endpoint-in-production)

> [!NOTE]
> A Functions 1.x futásidőben a webhook-szolgáltatók a szolgáltatók a szolgáltatók segítségével a kérelmek et különböző módokon engedélyezhetik, attól függően, hogy a szolgáltató mit támogat. Ez borítja [Webhooks és kulcsok](#webhooks-and-keys). A 2.x-es és újabb verziójú Functions futásidejű nem tartalmazza a webhook-szolgáltatók beépített támogatását.

#### <a name="authorization-scopes-function-level"></a>Engedélyezési hatókörök (függvényszintű)

A függvényszintű kulcsokhoz két engedélyezési hatókör tartozik:

* **Függvény**: Ezek a billentyűk csak azokra a funkciókra vonatkoznak, amelyek alatt definiálva vannak. API-kulcsként használva ezek csak a függvényhez való hozzáférést engedélyezik.

* **Host**: A gazdahatókörrel rendelkező kulcsok a függvényalkalmazás összes funkciójának elérésére használhatók. API-kulcsként használva ezek lehetővé teszik a hozzáférést a függvényalkalmazás bármely függvényéhez. 

Minden kulcs neve hivatkozásra van elnevezve, és a függvény és a gazdagép szintjén van egy alapértelmezett kulcs (az "alapértelmezett") . A függvénybillentyűk elsőbbséget élveznek a gazdabillentyűkkel szemben. Ha két kulcs azonos névvel van definiálva, a függvénykulcs mindig használatos lesz.

#### <a name="master-key-admin-level"></a>Főkulcs (rendszergazdai szintű) 

Minden függvényalkalmazásrendelkezik egy rendszergazdai `_master`szintű állomáskulccsal is, amelynek neve . Amellett, hogy gazdagépszintű hozzáférést biztosít az alkalmazás összes függvényéhez, a főkulcs felügyeleti hozzáférést biztosít a futásidejű REST API-khoz is. Ez a kulcs nem vonható vissza. A rendszer engedélyezési szintjének beállításakor a `admin`kérelmeknek a főkulcsot kell használniuk; bármely más kulcs engedélyezési hibát eredményez.

> [!CAUTION]  
> A főkulcs által biztosított emelt szintű engedélyek miatt ne ossza meg ezt a kulcsot harmadik felekkel, és ne terjessze azt natív ügyfélalkalmazásokban. Legyen óvatos, amikor kiválasztja a rendszergazdai engedélyezési szint.

## <a name="obtaining-keys"></a>Kulcsok beszerzése

A kulcsok a függvényalkalmazás részeként tárolódnak az Azure-ban, és inkulálva vannak titkosítva. A kulcsok megtekintéséhez hozzon létre újakat, vagy forgassa a kulcsokat az új értékekhez, keresse meg az [Azure](https://portal.azure.com) Portalon a HTTP-aktivált funkciók egyikét, és válassza a **Kezelés lehetőséget.**

![A függvénykulcsok kezelése a portálon.](./media/functions-bindings-http-webhook/manage-function-keys.png)

A funkciókulcsokat programozott módon szerezheti be [a kulcskezelési API-k](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)használatával.

## <a name="api-key-authorization"></a>API-kulcs engedélyezése

A legtöbb HTTP-eseményindító sablonhoz API-kulcs szükséges a kérelemben. Így a HTTP-kérés általában a következő URL-címnek tűnik:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A kulcs szerepelhet a lekérdezési karakterlánc változó neve , a fentiek szerint. `code` `x-functions-key` Http-fejlécben is szerepelhet. A kulcs értéke lehet a függvényhez definiált bármely függvénykulcs vagy bármely állomáskulcs.

Engedélyezheti a névtelen kérelmeket, amelyekhez nincs szükség kulcsokra. A főkulcs használatához is megkövetelheti. Az alapértelmezett engedélyezési szintet a `authLevel` kötési JSON tulajdonságával módosíthatja. További információ: [Trigger - configuration](#configuration).

> [!NOTE]
> A függvények helyi futtatásakor az engedélyezés a megadott engedélyezési szint beállítástól függetlenül le van tiltva. Az Azure-ban `authLevel` való közzététel után az eseményindító ban lévő beállítás érvénybe lép. A kulcsokra továbbra is szükség van, ha helyileg fut [egy tárolóban.](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)


## <a name="secure-an-http-endpoint-in-production"></a>HTTP-végpont biztosítása éles környezetben

A függvényvégpontok teljes körű védelméhez érdemes az alábbi függvényalkalmazás-szintű biztonsági beállítások egyikét végrehajtani:

* Kapcsolja be az App Service hitelesítését / engedélyezését a függvényalkalmazásban. Az App Service platform lehetővé teszi az Azure Active Directory (AAD) és számos külső identitásszolgáltató használatát az ügyfelek hitelesítéséhez. Ezzel a stratégiával egyéni engedélyezési szabályokat valósíthat meg a függvényekhez, és a függvénykódból származó felhasználói adatokkal dolgozhat. További információ: [Hitelesítés és engedélyezés az Azure App Service szolgáltatásban](../app-service/overview-authentication-authorization.md) és [az ügyfélidentitások használata című témakörben.](#working-with-client-identities)

* Az Azure API Management (APIM) használatával hitelesítheti a kérelmeket. Az APIM számos API-biztonsági lehetőséget biztosít a bejövő kérelmekhez. További információ: [API Management authentication policies](../api-management/api-management-authentication-policies.md). Az APIM-mel konfigurálhatja a függvényalkalmazást úgy, hogy csak az APIM-példány IP-címéről fogadja a kéréseket. További információ: [IP-címkorlátozások](ip-addresses.md#ip-address-restrictions).

* Telepítse a függvényalkalmazást egy Azure App Service Environment (ASE) környezetbe. Az ASE dedikált üzemeltetési környezetet biztosít a funkciók futtatásához. Az ASE lehetővé teszi egy előtér-átjáró konfigurálását, amely az összes bejövő kérelem hitelesítéséhez használható. További információt a [WebApplication Firewall (WAF) konfigurálása az App Service Environment szolgáltatáshoz című témakörben talál.](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

A függvényalkalmazás-szintű biztonsági módszerek egyikének használatakor a HTTP által aktivált `anonymous`függvényengedélyezési szintet a-ra kell állítania.

## <a name="webhooks"></a>Webhookok

> [!NOTE]
> A Webhook mód csak a Functions futásidejű 1.x-es verziójához érhető el. Ez a módosítás a HTTP-eseményindítók teljesítményének javítása érdekében történt a 2.x-es és újabb verzióban.

Az 1.x-es verzióban a webhook-sablonok további érvényesítést biztosítanak a webhook-rakományok számára. A 2.x-es és újabb verzióban az alap HTTP-eseményindító továbbra is működik, és a webhookok ajánlott megközelítése. 

### <a name="github-webhooks"></a>GitHub webhookok

A GitHub webhooks válaszul először hozza létre a függvényt egy `github`HTTP-eseményindítóval, és állítsa a **webHookType** tulajdonságot . Ezután másolja az URL-cím és az API-kulcs a **Hozzá webhook** lap a GitHub-tárház. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Laza webhookok

A Tartaléklatos webhook létrehoz egy jogkivonatot, ahelyett, hogy megadná, ezért konfigurálnia kell egy függvény-specifikus kulcsot a Tartalékjogból származó jogkivonattal. Lásd: [Engedélyezési kulcsok](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhookok és kulcsok

A webhook-engedélyezést a webhook-fogadó összetevő kezeli, amely a HTTP-eseményindító része, és a mechanizmus a webhook típusától függően változik. Minden mechanizmus egy kulcsra támaszkodik. Alapértelmezés szerint az "alapértelmezett" nevű függvénykulcs használatos. Másik kulcs használatához konfigurálja úgy a webhook-szolgáltatót, hogy a kulcsnevet a kéréssel együtt küldje el az alábbi módok egyikén:

* **Lekérdezési karakterlánc**: A szolgáltató átadja `clientid` a kulcs nevet `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`a lekérdezési karakterlánc paraméterben, például .
* **Kérelemfejléc**: A szolgáltató átadja a `x-functions-clientid` kulcs nevét a fejlécben.

## <a name="limits"></a>Korlátok

A HTTP-kérelem hossza 100 MB (104 857 600 bájt), az URL-cím hossza pedig 4 KB (4096 bájt). Ezeket a korlátokat `httpRuntime` a runtime [Web.config fájljának](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config)eleme határozza meg.

Ha egy függvény, amely a HTTP-eseményindító nem fejeződik be 230 másodpercen belül, az [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) időtúlórával, és egy HTTP 502-es hibát ad vissza. A függvény továbbra is fut, de nem tud http-választ visszaadni. A hosszú ideig futó függvények esetében azt javasoljuk, hogy kövesse az aszinkron mintákat, és adja vissza azt a helyet, ahol pingelheti a kérelem állapotát. Arról, hogy egy függvény mennyi ideig futtatható, olvassa el a [Méretezés és üzemeltetés – Felhasználási terv című témakört.](functions-scale.md#timeout)


## <a name="next-steps"></a>További lépések

- [HTTP-válasz visszaadása függvényből](./functions-bindings-http-webhook-output.md)
