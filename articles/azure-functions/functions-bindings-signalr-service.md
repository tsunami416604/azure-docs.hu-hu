---
title: Azure Functions szignáló szolgáltatás kötései
description: Megtudhatja, hogyan használhatja a Signaler szolgáltatás kötéseit Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: d5ded7200c438fc734e1af0a42ca6071196bd41c
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120337"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-szolgáltatáskötések az Azure Functionshöz

Ez a cikk bemutatja, hogyan hitelesítheti és küldheti el a valós idejű üzeneteket az [Azure signaler szolgáltatáshoz](https://azure.microsoft.com/services/signalr-service/) csatlakozó ügyfeleknek a Azure functions-ben a Signal Service-kötések használatával. Az Azure Functions támogatja a SignalR-szolgáltatás bemeneti és kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A Signaler szolgáltatás kötései a [Microsoft. Azure. webjobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet csomagban találhatók, 1. verzió. *. A csomag forráskódja az [Azure-functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

A Signaler Azure Functions szolgáltatás konfigurálásával és használatával kapcsolatos további részletekért tekintse meg a [Azure functions fejlesztés és konfigurálás az Azure signaler szolgáltatással](../azure-signalr/signalr-concept-serverless-development-config.md)című témakört.

### <a name="annotations-library-java-only"></a>Jegyzetek könyvtára (csak Java)

Ahhoz, hogy használni lehessen a Signaler szolgáltatás megjegyzéseit a Java-függvényekben, hozzá kell adnia egy függőséget az *Azure-functions-Java-Library-signaler* összetevőhöz (1,0-es vagy újabb verzió) a Pom. xml fájlhoz.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>Input (Bemenet)

Ahhoz, hogy az ügyfél csatlakozni tudjanak az Azure Signaler szolgáltatáshoz, le kell kérnie a szolgáltatási végpont URL-címét és egy érvényes hozzáférési tokent. A *SignalRConnectionInfo* bemeneti kötése létrehozza a signaler szolgáltatás végpontjának URL-címét és a szolgáltatáshoz való kapcsolódáshoz használt érvényes jogkivonatot. Mivel a jogkivonat időkorlátja korlátozott, és egy adott felhasználó egy kapcsolathoz való hitelesítésére használható, ne gyorsítótárazza a tokent, vagy ossza meg az ügyfelek között. A kötést használó HTTP-triggereket az ügyfelek használhatják a kapcsolati adatok lekérésére.

Ha további információra van szükség arról, hogy ez a kötés hogyan használható a Signaler ügyféloldali SDK által felhasználható "egyeztetés" függvény létrehozásához, tekintse meg a következő témakört: [Azure functions fejlesztés és konfiguráció cikk](../azure-signalr/signalr-concept-serverless-development-config.md) a Signaler szolgáltatással kapcsolatos fogalmak dokumentációjában.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

### <a name="authenticated-tokens"></a>Hitelesített tokenek

Ha a függvényt egy hitelesített ügyfél indítja el, felhasználói azonosító jogcímet adhat hozzá a generált jogkivonathoz. [App Service hitelesítés](../app-service/overview-authentication-authorization.md)használatával egyszerűen adhat hozzá hitelesítést egy Function alkalmazáshoz.

App Service a hitelesítés beállítja a `x-ms-client-principal-id` és `x-ms-client-principal-name` nevű HTTP-fejléceket, amelyek tartalmazzák a hitelesített felhasználó ügyfél-AZONOSÍTÓját és nevét.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

## <a name="output"></a>Kimenet

A *signaler* kimeneti kötés használatával küldjön egy vagy több üzenetet az Azure Signaler szolgáltatással. Küldhet üzenetet az összes csatlakoztatott ügyfélnek, vagy csak az adott felhasználó számára hitelesített csatlakoztatott ügyfelek számára is közvetítheti.

Azt is megteheti, hogy felügyeli azokat a csoportokat, amelyekhez a felhasználó tartozik.

### <a name="broadcast-to-all-clients"></a>Közvetítés az összes ügyfél számára

Az alábbi példa egy olyan függvényt mutat be, amely az összes csatlakoztatott ügyfél kimeneti kötését használva üzenetet küld. A *cél* az egyes ügyfeleken meghívott metódus neve. Az *argumentumok* az ügyfél metódusának átadandó nulla vagy több objektum tömbje.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-user"></a>Küldés felhasználó számára

Csak a felhasználó számára hitelesített kapcsolatokra küldhet üzenetet, ha beállítja a *felhasználói azonosítót* a jelző üzenetben.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-group"></a>Küldés egy csoportba

Az üzenetet csak olyan kapcsolatokhoz küldheti, amelyeket a rendszer hozzáad egy csoporthoz a *csoport nevének* a jelző üzenetben való beállításával.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="group-management"></a>Csoportfelügyelet

A signaler szolgáltatás lehetővé teszi a felhasználóknak a csoportokba való felvételét. Ezután üzeneteket küldhetnek egy csoportnak. A felhasználó csoporttagság kezeléséhez használhatja a `SignalR` kimeneti kötést.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Ahhoz, hogy a `ClaimsPrincipal` megfelelően kötve legyen, konfigurálnia kell a hitelesítési beállításokat a Azure Functionsban.

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

Példa *function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Futtassa a. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

Példa *function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Futtassa a. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Ahhoz, hogy a `ClaimsPrincipal` megfelelően kötve legyen, konfigurálnia kell a hitelesítési beállításokat a Azure Functionsban.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

Példa *function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

Példa *function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

Példa *function. JSON*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

Példa *function. JSON*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

#### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

## <a name="configuration"></a>Konfiguráció

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Az alábbi táblázat a *function. JSON* fájlban és a `SignalRConnectionInfo` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**| – | `signalRConnectionInfo`értékre kell állítani.|
|**direction**| – | `in`értékre kell állítani.|
|**név**| – | A kapcsolatkérelem-objektumhoz tartozó függvény kódjában használt változó neve. |
|**hubName**|**HubName**| Ezt az értéket annak a jelző-hubhoz a nevére kell beállítani, amelyhez a kapcsolódási adatok létrejöttek.|
|**userId**|**UserId**| Nem kötelező: a hozzáférési kulcs jogkivonatában beállítani kívánt felhasználói azonosító jogcím értéke. |
|**connectionStringSetting**|**ConnectionStringSetting**| A jelző szolgáltatás kapcsolódási sztringjét tartalmazó Alkalmazásbeállítás neve (alapértelmezett érték: "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

Az alábbi táblázat a *function. JSON* fájlban és a `SignalR` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**| – | `signalR`értékre kell állítani.|
|**direction**| – | `out`értékre kell állítani.|
|**név**| – | A kapcsolatkérelem-objektumhoz tartozó függvény kódjában használt változó neve. |
|**hubName**|**HubName**| Ezt az értéket annak a jelző-hubhoz a nevére kell beállítani, amelyhez a kapcsolódási adatok létrejöttek.|
|**connectionStringSetting**|**ConnectionStringSetting**| A jelző szolgáltatás kapcsolódási sztringjét tartalmazó Alkalmazásbeállítás neve (alapértelmezett érték: "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](../azure-signalr/signalr-concept-serverless-development-config.md)
