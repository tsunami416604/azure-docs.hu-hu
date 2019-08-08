---
title: Azure Functions szignáló szolgáltatás kötései
description: Megtudhatja, hogyan használhatja a Signaler szolgáltatás kötéseit Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: b4622321dc25025eb2f7752755490eb5bc105069
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741785"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-szolgáltatáskötések az Azure Functionshöz

Ez a cikk bemutatja, hogyan hitelesítheti és küldheti el a valós idejű üzeneteket az [Azure signaler szolgáltatáshoz](https://azure.microsoft.com/services/signalr-service/) csatlakozó ügyfeleknek a Azure functions-ben a Signal Service-kötések használatával. Azure Functions támogatja a jelző szolgáltatás bemeneti és kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Signaler szolgáltatás kötései a [Microsoft. Azure. webjobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet csomagban találhatók, 1. verzió. *. A csomag forráskódja az [Azure-functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java-jegyzetek

Ahhoz, hogy használni lehessen a Signaler szolgáltatás megjegyzéseit a Java-függvényekben, hozzá kell adnia egy függőséget az *Azure-functions-Java-Library-signaler* összetevőhöz (1,0-es vagy újabb verzió) a Pom. xml fájlhoz.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Ha a Signaler szolgáltatáshoz tartozó kötéseket a javában szeretné használni, győződjön meg róla, hogy a Azure Functions Core Tools (2.4.419) vagy újabb verzióját használja (a gazdagép verziója 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>A Signaler szolgáltatás használata Azure Functions

A Signaler Azure Functions szolgáltatás konfigurálásával és használatával kapcsolatos további részletekért tekintse meg a [Azure functions fejlesztés és konfigurálás az Azure signaler szolgáltatással](../azure-signalr/signalr-concept-serverless-development-config.md)című témakört.

## <a name="signalr-connection-info-input-binding"></a>A jelző kapcsolódási adatainak bemeneti kötése

Ahhoz, hogy az ügyfél csatlakozni tudjanak az Azure Signaler szolgáltatáshoz, le kell kérnie a szolgáltatási végpont URL-címét és egy érvényes hozzáférési tokent. A *SignalRConnectionInfo* bemeneti kötése létrehozza a signaler szolgáltatás végpontjának URL-címét és a szolgáltatáshoz való kapcsolódáshoz használt érvényes jogkivonatot. Mivel a jogkivonat időkorlátja korlátozott, és egy adott felhasználó egy kapcsolathoz való hitelesítésére használható, ne gyorsítótárazza a tokent, vagy ossza meg az ügyfelek között. A kötést használó HTTP-triggereket az ügyfelek használhatják a kapcsolati adatok lekérésére.

Tekintse meg az adott nyelvű példa:

* [2. xC#](#2x-c-input-examples)
* [2. x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

Ha további információra van szükség arról, hogy ez a kötés hogyan használható a Signaler ügyféloldali SDK által felhasználható "egyeztetés" függvény létrehozásához, tekintse meg a következő témakört: [Azure functions fejlesztés és konfiguráció cikk](../azure-signalr/signalr-concept-serverless-development-config.md) a Signaler szolgáltatással kapcsolatos fogalmak dokumentációjában.

### <a name="2x-c-input-examples"></a>2. x C# bemeneti példák

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

#### <a name="authenticated-tokens"></a>Hitelesített tokenek

Ha a függvényt egy hitelesített ügyfél indítja el, felhasználói azonosító jogcímet adhat hozzá a generált jogkivonathoz. [App Service hitelesítés](../app-service/overview-authentication-authorization.md)használatával egyszerűen adhat hozzá hitelesítést egy Function alkalmazáshoz.

App Service hitelesítés beállítja a nevű `x-ms-client-principal-id` http- `x-ms-client-principal-name` fejléceket, amelyek tartalmazzák a hitelesített felhasználó ügyfél-azonosítóját és nevét. Megadhatja a kötés `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}` [](./functions-bindings-expressions-patterns.md) tulajdonságátbármelyikfejlécértékéreegy`UserId` kötési kifejezés használatával: vagy. 

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

### <a name="2x-javascript-input-examples"></a>2. x JavaScript bemeneti példák

Az alábbi példa egy Signal-kapcsolati adatokat tartalmazó bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript](functions-reference-node.md) -függvényt, amely a kötést használja a kapcsolati adatok visszaadásához.

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

#### <a name="authenticated-tokens"></a>Hitelesített tokenek

Ha a függvényt egy hitelesített ügyfél indítja el, felhasználói azonosító jogcímet adhat hozzá a generált jogkivonathoz. [App Service hitelesítés](../app-service/overview-authentication-authorization.md)használatával egyszerűen adhat hozzá hitelesítést egy Function alkalmazáshoz.

App Service hitelesítés beállítja a nevű `x-ms-client-principal-id` http- `x-ms-client-principal-name` fejléceket, amelyek tartalmazzák a hitelesített felhasználó ügyfél-azonosítóját és nevét. Megadhatja a kötés `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}` [](./functions-bindings-expressions-patterns.md) tulajdonságátbármelyikfejlécértékéreegy`userId` kötési kifejezés használatával: vagy. 

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

### <a name="2x-java-input-examples"></a>2. x Java-bemeneti példák

Az alábbi példa egy [Java](functions-reference-java.md) -függvényt mutat be, amely a bemeneti kötés használatával szerzi be a jelző kapcsolati adatait, és visszaadja a HTTP protokollon keresztül.

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

#### <a name="authenticated-tokens"></a>Hitelesített tokenek

Ha a függvényt egy hitelesített ügyfél indítja el, felhasználói azonosító jogcímet adhat hozzá a generált jogkivonathoz. [App Service hitelesítés](../app-service/overview-authentication-authorization.md)használatával egyszerűen adhat hozzá hitelesítést egy Function alkalmazáshoz.

App Service hitelesítés beállítja a nevű `x-ms-client-principal-id` http- `x-ms-client-principal-name` fejléceket, amelyek tartalmazzák a hitelesített felhasználó ügyfél-azonosítóját és nevét. Megadhatja a kötés `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}` [](./functions-bindings-expressions-patterns.md) tulajdonságátbármelyikfejlécértékéreegy`UserId` kötési kifejezés használatával: vagy.

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

## <a name="signalr-output-binding"></a>Jelző kimeneti kötése

A *signaler* kimeneti kötés használatával küldjön egy vagy több üzenetet az Azure Signaler szolgáltatással. Küldhet üzenetet az összes csatlakoztatott ügyfélnek, vagy csak az adott felhasználó számára hitelesített csatlakoztatott ügyfelek számára is közvetítheti.

Azt is megteheti, hogy felügyeli azokat a csoportokat, amelyekhez a felhasználó tartozik.

Tekintse meg az adott nyelvű példa:

* [2. xC#](#2x-c-send-message-output-examples)
* [2. x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2. x C# üzenet küldése kimeneti példák

#### <a name="broadcast-to-all-clients"></a>Közvetítés az összes ügyfél számára

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely az összes csatlakoztatott ügyfél kimeneti kötését használva üzenetet küld. A `Target` az egyes ügyfeleken meghívott metódus neve. A `Arguments` tulajdonság az ügyfél metódusának átadandó nulla vagy több objektum tömbje.

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

#### <a name="send-to-a-user"></a>Küldés felhasználó számára

A jelző üzenet `UserId` tulajdonságának beállításával csak a felhasználó számára hitelesített kapcsolatokra küldhet üzenetet.

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

#### <a name="send-to-a-group"></a>Küldés egy csoportba

A jelző üzenet `GroupName` tulajdonságának beállításával csak a csoportba felvett kapcsolatokhoz küldhet üzenetet.

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

### <a name="2x-c-group-management-output-examples"></a>2. x C# csoport-felügyeleti kimeneti példák

A signaler szolgáltatás lehetővé teszi a felhasználóknak a csoportokba való felvételét. Ezután üzeneteket küldhetnek egy csoportnak. A `SignalRGroupAction` osztály és a `SignalR` kimeneti kötés használatával kezelheti a felhasználók csoportjának tagságát.

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
> `ClaimsPrincipal` A megfelelő kötés eléréséhez konfigurálnia kell a hitelesítési beállításokat a Azure Functionsban.

### <a name="2x-javascript-send-message-output-examples"></a>2. x JavaScript-üzenetek küldésére vonatkozó példák

#### <a name="broadcast-to-all-clients"></a>Közvetítés az összes ügyfél számára

Az alábbi példa egy Signaler kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript](functions-reference-node.md) -függvényt, amely a kötés használatával küld egy üzenetet az Azure signaler szolgáltatással. Adja meg a kimeneti kötést egy vagy több jelző üzenet tömbje számára. A jelző üzenet egy olyan `target` tulajdonságból áll, amely megadja az egyes ügyfeleken meghívott metódus nevét, valamint egy olyan `arguments` tulajdonságot, amely az ügyfél metódusának argumentumként való továbbítására szolgáló objektumok tömbje.

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

A következő JavaScript-kódot:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Küldés felhasználó számára

A jelző üzenet `userId` tulajdonságának beállításával csak a felhasználó számára hitelesített kapcsolatokra küldhet üzenetet.

a *function. JSON* ugyanaz marad. A következő JavaScript-kódot:

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

#### <a name="send-to-a-group"></a>Küldés egy csoportba

A jelző üzenet `groupName` tulajdonságának beállításával csak a csoportba felvett kapcsolatokhoz küldhet üzenetet.

a *function. JSON* ugyanaz marad. A következő JavaScript-kódot:

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

### <a name="2x-javascript-group-management-output-examples"></a>2. x JavaScript csoport-felügyeleti kimeneti példák

A signaler szolgáltatás lehetővé teszi a felhasználóknak a csoportokba való felvételét. Ezután üzeneteket küldhetnek egy csoportnak. A `SignalR` kimeneti kötés használatával kezelheti a felhasználók csoportjának tagságát.

#### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

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

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2. x Java-üzenet küldése kimeneti példák

#### <a name="broadcast-to-all-clients"></a>Közvetítés az összes ügyfél számára

Az alábbi példa egy [Java](functions-reference-java.md) -függvényt mutat be, amely az összes csatlakoztatott ügyfél kimeneti kötését használva üzenetet küld. A `target` az egyes ügyfeleken meghívott metódus neve. A `arguments` tulajdonság az ügyfél metódusának átadandó nulla vagy több objektum tömbje.

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

#### <a name="send-to-a-user"></a>Küldés felhasználó számára

A jelző üzenet `userId` tulajdonságának beállításával csak a felhasználó számára hitelesített kapcsolatokra küldhet üzenetet.

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

#### <a name="send-to-a-group"></a>Küldés egy csoportba

A jelző üzenet `groupName` tulajdonságának beállításával csak a csoportba felvett kapcsolatokhoz küldhet üzenetet.

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

### <a name="2x-java-group-management-output-examples"></a>2. x Java csoport-felügyeleti kimeneti példák

A signaler szolgáltatás lehetővé teszi a felhasználóknak a csoportokba való felvételét. Ezután üzeneteket küldhetnek egy csoportnak. A `SignalRGroupAction` osztály és a `SignalROutput` kimeneti kötés használatával kezelheti a felhasználók csoportjának tagságát.

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

## <a name="configuration"></a>Konfiguráció

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `SignalRConnectionInfo` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**|| Meg kell `signalRConnectionInfo`.|
|**direction**|| Meg kell `in`.|
|**name**|| A kapcsolatkérelem-objektumhoz tartozó függvény kódjában használt változó neve. |
|**hubName**|**HubName**| Ezt az értéket annak a jelző-hubhoz a nevére kell beállítani, amelyhez a kapcsolódási adatok létrejöttek.|
|**userId**|**UserId**| Nem kötelező: A hozzáférési kulcs jogkivonatában beállítani kívánt felhasználói azonosító jogcím értéke. |
|**connectionStringSetting**|**ConnectionStringSetting**| A jelző szolgáltatás kapcsolódási sztringjét tartalmazó Alkalmazásbeállítás neve (alapértelmezett érték: "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `SignalR` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**|| Meg kell `signalR`.|
|**direction**|| Meg kell `out`.|
|**name**|| A kapcsolatkérelem-objektumhoz tartozó függvény kódjában használt változó neve. |
|**hubName**|**HubName**| Ezt az értéket annak a jelző-hubhoz a nevére kell beállítani, amelyhez a kapcsolódási adatok létrejöttek.|
|**connectionStringSetting**|**ConnectionStringSetting**| A jelző szolgáltatás kapcsolódási sztringjét tartalmazó Alkalmazásbeállítás neve (alapértelmezett érték: "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](../azure-signalr/signalr-concept-serverless-development-config.md)
