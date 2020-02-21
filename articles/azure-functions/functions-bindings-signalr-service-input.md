---
title: Azure Functions szignáló szolgáltatás bemeneti kötése
description: Megtudhatja, hogyan adhat vissza a Signaler szolgáltatás végpontjának URL-címét és a hozzáférési tokent Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77530262"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>A signaler szolgáltatás bemeneti kötése Azure Functions

Ahhoz, hogy az ügyfél csatlakozni tudjanak az Azure Signaler szolgáltatáshoz, le kell kérnie a szolgáltatási végpont URL-címét és egy érvényes hozzáférési tokent. A *SignalRConnectionInfo* bemeneti kötése létrehozza a signaler szolgáltatás végpontjának URL-címét és a szolgáltatáshoz való kapcsolódáshoz használt érvényes jogkivonatot. Mivel a jogkivonat időkorlátja korlátozott, és egy adott felhasználó egy kapcsolathoz való hitelesítésére használható, ne gyorsítótárazza a tokent, vagy ossza meg az ügyfelek között. A kötést használó HTTP-triggereket az ügyfelek használhatják a kapcsolati adatok lekérésére.

Ha további információra van szükség arról, hogy ez a kötés hogyan használható a Signaler ügyféloldali SDK által felhasználható "egyeztetés" függvény létrehozásához, tekintse meg a következő témakört: [Azure functions fejlesztés és konfiguráció cikk](../azure-signalr/signalr-concept-serverless-development-config.md) a Signaler szolgáltatással kapcsolatos fogalmak dokumentációjában.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-signalr-service.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely a bemeneti kötés használatával beolvassa a Signal-kapcsolati adatokat, és visszaadja a HTTP-n keresztül.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy Signal-kapcsolati adatbeviteli kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötés használatával adja vissza a kapcsolati adatokat.

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Signal-kapcsolati adatokat tartalmazó bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja a kapcsolati adatok visszaadásához.

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

A következő JavaScript-kódot:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy Signal-kapcsolati adatbeviteli kötést mutat be egy *function. JSON* fájlban és egy [Python-függvényben](functions-reference-python.md) , amely a kötés használatával adja vissza a kapcsolati adatokat.

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy [Java-függvényt](functions-reference-java.md) mutat be, amely a bemeneti kötés használatával szerzi be a jelző kapcsolati adatait, és visszaadja a HTTP protokollon keresztül.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Hitelesített tokenek

Ha a függvényt egy hitelesített ügyfél indítja el, felhasználói azonosító jogcímet adhat hozzá a generált jogkivonathoz. [App Service hitelesítés](../app-service/overview-authentication-authorization.md)használatával egyszerűen adhat hozzá hitelesítést egy Function alkalmazáshoz.

App Service a hitelesítés beállítja a `x-ms-client-principal-id` és `x-ms-client-principal-name` nevű HTTP-fejléceket, amelyek tartalmazzák a hitelesített felhasználó ügyfél-AZONOSÍTÓját és nevét.

# <a name="c"></a>[C#](#tab/csharp)

A kötés `UserId` tulajdonságát beállíthatja bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

A kötés `userId` tulajdonságát beállíthatja bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`.

Példa a function. JSON fájlra:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kötés `userId` tulajdonságát beállíthatja bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`.

Példa a function. JSON fájlra:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

A következő JavaScript-kódot:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

A kötés `userId` tulajdonságát beállíthatja bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`.

Példa a function. JSON fájlra:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

A kötés `userId` tulajdonságát beállíthatja bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Következő lépések

- [Szignáló szolgáltatás üzeneteinek küldése (kimeneti kötés)](./functions-bindings-signalr-service-output.md) 
