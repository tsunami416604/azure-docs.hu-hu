---
title: Az Azure Functions SignalR szolgáltatás bemeneti kötése
description: Ismerje meg, hogy adja vissza a SignalR szolgáltatás végpont URL-címét és hozzáférési jogkivonatot az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530262"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>SignalR szolgáltatás bemeneti kötése az Azure Functionshez

Ahhoz, hogy egy ügyfél csatlakozhatna az Azure SignalR szolgáltatáshoz, le kell kérnie a szolgáltatás végponturl-címét és egy érvényes hozzáférési jogkivonatot. A *SignalRConnectionInfo* bemeneti kötés létrehozza a SignalR service végpont URL-címét és egy érvényes jogkivonatot, amely a szolgáltatáshoz való csatlakozáshoz használatos. Mivel a jogkivonat időkorlátos, és egy adott felhasználó hitelesítésére használható egy kapcsolathoz, nem szabad gyorsítótárazni a jogkivonatot, és nem szabad megosztani a jogkivonatot az ügyfelek között. A kötést használó HTTP-eseményindítót az ügyfelek használhatják a kapcsolatadatainak beolvasására.

Ha többet szeretne tudni arról, hogy ez a kötés hogyan használható a SignalR ügyfél SDK által felhasználható "egyeztetési" függvény létrehozásához, tekintse meg az [Azure Functions fejlesztési és konfigurációs cikkét](../azure-signalr/signalr-concept-serverless-development-config.md) a SignalR-szolgáltatás fogalmai dokumentációjában.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](functions-bindings-signalr-service.md)olvashat.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely a bemeneti kötés használatával beszerzi a SignalR-kapcsolat adatait, és http-n keresztül adja vissza azt.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy SignalR-kapcsolat ionbemeneti kötését mutatja be egy *function.json* fájlban, és egy [C# Script függvényt,](functions-reference-csharp.md) amely a kötést használja a kapcsolatadatainak adására.

A *function.json* fájlban az adatok kötése:

Példa function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt a C# Script kód:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy SignalR-kapcsolat ionbemeneti bemeneti kötését mutatja be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja a kapcsolatadatainak adására.

A *function.json* fájlban az adatok kötése:

Példa function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt a JavaScript-kód:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

A következő példa egy SignalR-kapcsolat info bemeneti kötését mutatja be egy *function.json* fájlban, és egy [Python függvényt,](functions-reference-python.md) amely a kötést használja a kapcsolatadatainak adására.

A *function.json* fájlban az adatok kötése:

Példa function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt a Python kód:

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

A következő példa egy [Java függvényt](functions-reference-java.md) mutat be, amely a bemeneti kötés használatával beszerzi a SignalR-kapcsolat adatait, és http-n keresztül adja vissza azt.

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

Ha a függvényt egy hitelesített ügyfél váltja ki, hozzáadhat egy felhasználói azonosító jogcímet a létrehozott jogkivonathoz. Az [App Service-hitelesítés](../app-service/overview-authentication-authorization.md)segítségével egyszerűen hozzáadhat hitelesítést egy függvényalkalmazáshoz.

Az App Service-hitelesítés `x-ms-client-principal-id` beállítja a névvel ellátott HTTP-fejléceket, amelyek `x-ms-client-principal-name` tartalmazzák a hitelesített felhasználó ügyfélügyfél-egyszerű azonosítóját és nevét.

# <a name="c"></a>[C #](#tab/csharp)

A kötés `UserId` tulajdonságát a fejlécből a fejlécből állíthatja `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`be egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: vagy a .

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A kötés `userId` tulajdonságát a fejlécből a fejlécből állíthatja `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`be egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: vagy a .

Példa function.json:

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

Itt a C# Script kód:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A kötés `userId` tulajdonságát a fejlécből a fejlécből állíthatja `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`be egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: vagy a .

Példa function.json:

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

Itt a JavaScript-kód:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

A kötés `userId` tulajdonságát a fejlécből a fejlécből állíthatja `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`be egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: vagy a .

Példa function.json:

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

Itt a Python kód:

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

A kötés `userId` tulajdonságát a fejlécből a fejlécből állíthatja `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`be egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: vagy a .

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

## <a name="next-steps"></a>További lépések

- [SignalR szolgáltatás üzenetek küldése (kimenetkötés)](./functions-bindings-signalr-service-output.md) 
