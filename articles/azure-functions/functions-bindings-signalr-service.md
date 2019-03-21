---
title: Az Azure Functions SignalR Service kötések
description: Megtudhatja, hogyan használhatja a SignalR Service kötések az Azure Functions használatával.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: ab050a683913e62c6671bf01397e76311a08952b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006789"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-szolgáltatáskötések az Azure Functionshöz

Ez a cikk azt ismerteti, hogyan és valós idejű üzeneteket küldeni a kapcsolódó ügyfelek hitelesítéséhez [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) SignalR Service kötések az Azure Functions használatával. Az Azure Functions támogatja a bemeneti és kimeneti kötései SignalR Service.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A SignalR Service kötéseket szerepelnek a [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet-csomag verziója 1.*. A csomag forráskódja a [azure-functions-signalrservice-bővítmény](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java-jegyzetek

Java-függvények a SignalR Service jegyzetek használ, adja hozzá egy függőséget kell a *azure-functions-java-kódtár – signalr* a pom.xml lehívandó összetevő (1.0-s vagy újabb verzió).

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> A SignalR Service kötéseket Java-környezetben, győződjön meg arról, hogy 2.4.419 verzióját használja, vagy újabb verzióját az Azure Functions Core Tools (gazdagép verziója 2.0.12332) használatára.

## <a name="using-signalr-service-with-azure-functions"></a>SignalR Service használata az Azure Functions használatával

Megtudhatja, hogyan konfigurálhatja és használhatja a SignalR Service és az Azure Functions együtt, tekintse meg a [Azure Functions fejlesztői és az Azure SignalR Service configuration](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>A SignalR kapcsolati adatok bemeneti kötéssel

Egy ügyfél csak akkor csatlakozhatnak az Azure SignalR Service, a végpont URL-címe és a egy érvényes hozzáférési jogkivonatot kell lekérnie. A *SignalRConnectionInfo* bemeneti kötésnek a SignalR Service-végpont URL-címe és a egy érvényes tokent a szolgáltatáshoz való csatlakozáshoz használt hoz létre. A jogkivonat időben korlátozott, és a egy kapcsolat egy adott felhasználót hitelesítésre használható, mert ne gyorsítótárazza a jogkivonat és ossza meg az ügyfelek között. Ez a kötés használatával a HTTP-trigger segítségével az ügyfelek által a kapcsolati adatok lekéréséhez.

Tekintse meg az adott nyelvű példa:

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

Hogyan ezt a kötést "egyeztetni" függvény, amely egy SignalR ügyfél-SDK által felhasználható létrehozásához használt további információkért lásd: a [Azure Functions fejlesztői és konfigurációs cikk](../azure-signalr/signalr-concept-serverless-development-config.md) a a SignalR-szolgáltatással kapcsolatos fogalmak dokumentáció.

### <a name="2x-c-input-examples"></a>2.x C# bemeneti példák

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely beszerzi a SignalR kapcsolati adatokat, használja a bemeneti kötést, és adja vissza, HTTP protokollon keresztül.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Hitelesített jogkivonatok

A függvény egy hitelesített ügyfél akkor aktiválódik, ha egy felhasználói azonosító jogcím a generált token is hozzáadhat. Egyszerűen hozzáadhat hitelesítési egy függvény használatával [App Service-hitelesítés](../app-service/overview-authentication-authorization.md).

App Service-hitelesítés beállítása nevű HTTP-fejlécek `x-ms-client-principal-id` és `x-ms-client-principal-name` tartalmaznak, a hitelesített felhasználó egyszerű ügyfél-Azonosítóját és nevét, illetve. Beállíthatja a `UserId` tulajdonság értéke kötésének vagy fejléc használatával egy [kifejezés kötés](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-javascript-input-examples"></a>2.x JavaScript bemeneti példák

Az alábbi példa bemutatja a SignalR kapcsolati adatok bemeneti kötést egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja, a kapcsolati információkat ad vissza.

Íme kötési adatait a *function.json* fájlt:

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

A következő JavaScript-kódot:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Hitelesített jogkivonatok

A függvény egy hitelesített ügyfél akkor aktiválódik, ha egy felhasználói azonosító jogcím a generált token is hozzáadhat. Egyszerűen hozzáadhat hitelesítési egy függvény használatával [App Service-hitelesítés](../app-service/overview-authentication-authorization.md).

App Service-hitelesítés beállítása nevű HTTP-fejlécek `x-ms-client-principal-id` és `x-ms-client-principal-name` tartalmaznak, a hitelesített felhasználó egyszerű ügyfél-Azonosítóját és nevét, illetve. Beállíthatja a `userId` tulajdonság értéke kötésének vagy fejléc használatával egy [kifejezés kötés](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`. 

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

A következő JavaScript-kódot:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2.x Java-alapú bemeneti példa

A következő példa bemutatja egy [Java függvény](functions-reference-java.md) , amely beszerzi a SignalR kapcsolati adatokat, használja a bemeneti kötést, és adja vissza, HTTP protokollon keresztül.

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

#### <a name="authenticated-tokens"></a>Hitelesített jogkivonatok

A függvény egy hitelesített ügyfél akkor aktiválódik, ha egy felhasználói azonosító jogcím a generált token is hozzáadhat. Egyszerűen hozzáadhat hitelesítési egy függvény használatával [App Service-hitelesítés](../app-service/overview-authentication-authorization.md).

App Service-hitelesítés beállítása nevű HTTP-fejlécek `x-ms-client-principal-id` és `x-ms-client-principal-name` tartalmaznak, a hitelesített felhasználó egyszerű ügyfél-Azonosítóját és nevét, illetve. Beállíthatja a `UserId` tulajdonság értéke kötésének vagy fejléc használatával egy [kifejezés kötés](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`.

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

## <a name="signalr-output-binding"></a>A SignalR kimeneti kötése

Használja a *SignalR* kimeneti kötése az Azure SignalR Service segítségével egy vagy több üzenet küldése. Üzenetet elküldheti az összes csatlakoztatott ügyfelek, vagy csak a csatlakoztatott ügyfelek, amelyek egy adott felhasználó hitelesített szórási.

Is használhatja azt, hogy egy felhasználó tartozik a csoportok kezelése.

Tekintse meg az adott nyelvű példa:

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.x C# kimeneti példa üzenet küldése

#### <a name="broadcast-to-all-clients"></a>Összes ügyfél a közvetítést

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely egy csatlakoztatott ügyfelek összes kimeneti kötés használatával üzeneteket küld. A `Target` minden egyes ügyfélnek meg kell hívni a metódus neve. A `Arguments` tulajdonság értéke nulla vagy több, az ügyfél módszer átadandó objektumok egy tömbjét.

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

#### <a name="send-to-a-user"></a>Küldhet a felhasználónak

Küldhet üzenetet csak egy felhasználónak beállításával hitelesített kapcsolatokhoz a `UserId` tulajdonság a SignalR-üzenet.

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

#### <a name="send-to-a-group"></a>Küldése egy csoportot

Csak a kapcsolatok beállításával csoporthoz hozzáadott küldhet üzenetet a `GroupName` tulajdonság a SignalR-üzenet.

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

### <a name="2x-c-group-management-output-examples"></a>2.x C# eszközcsoport-kezelés kimeneti példa

SignalR Service lehetővé teszi a felhasználóknak adni csoportokhoz. Egy csoport ezután képes üzeneteket küldeni. Használhatja a `SignalRGroupAction` osztály használatával a `SignalR` kimeneti kötést egy felhasználói csoport tagságának kezeléséhez.

#### <a name="add-user-to-a-group"></a>Felhasználó hozzáadása csoporthoz

Az alábbi példa hozzáad egy felhasználót egy csoporthoz.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa eltávolítja a felhasználó a csoportból.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>JavaScript-üzenetküldés 2.x-es kimeneti példák

#### <a name="broadcast-to-all-clients"></a>Összes ügyfél a közvetítést

Az alábbi példa bemutatja egy kötelező a SignalR-kimenet egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja az Azure SignalR Service egy üzenetet kíván küldeni. Állítsa a kimeneti kötést egy vagy több SignalR üzenetet tömbjét. A SignalR üzenet áll egy `target` tulajdonságot, amely a meghívni kívánt metódus nevét adja meg az egyes ügyfelekre és a egy `arguments` tulajdonságot, amely az ügyfél módszer átadása argumentumként objektumok tömbje.

Íme kötési adatait a *function.json* fájlt:

Példa function.json:

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

#### <a name="send-to-a-user"></a>Küldhet a felhasználónak

Küldhet üzenetet csak egy felhasználónak beállításával hitelesített kapcsolatokhoz a `userId` tulajdonság a SignalR-üzenet.

*Function.JSON* változatlan marad. A következő JavaScript-kódot:

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

#### <a name="send-to-a-group"></a>Küldése egy csoportot

Csak a kapcsolatok beállításával csoporthoz hozzáadott küldhet üzenetet a `groupName` tulajdonság a SignalR-üzenet.

*Function.JSON* változatlan marad. A következő JavaScript-kódot:

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

### <a name="2x-javascript-group-management-output-examples"></a>JavaScript eszközcsoport-kezelés 2.x kimeneti példa

SignalR Service lehetővé teszi a felhasználóknak adni csoportokhoz. Egy csoport ezután képes üzeneteket küldeni. Használhatja a `SignalR` kimeneti kötést egy felhasználói csoport tagságának kezeléséhez.

#### <a name="add-user-to-a-group"></a>Felhasználó hozzáadása csoporthoz

Az alábbi példa hozzáad egy felhasználót egy csoporthoz.

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

A következő példa eltávolítja a felhasználó a csoportból.

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

### <a name="2x-java-send-message-output-examples"></a>Java-üzenetküldés 2.x-es kimeneti példák

#### <a name="broadcast-to-all-clients"></a>Összes ügyfél a közvetítést

A következő példa bemutatja egy [Java függvény](functions-reference-java.md) , amely egy csatlakoztatott ügyfelek összes kimeneti kötés használatával üzeneteket küld. A `target` minden egyes ügyfélnek meg kell hívni a metódus neve. A `arguments` tulajdonság értéke nulla vagy több, az ügyfél módszer átadandó objektumok egy tömbjét.

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

#### <a name="send-to-a-user"></a>Küldhet a felhasználónak

Küldhet üzenetet csak egy felhasználónak beállításával hitelesített kapcsolatokhoz a `userId` tulajdonság a SignalR-üzenet.

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

#### <a name="send-to-a-group"></a>Küldése egy csoportot

Csak a kapcsolatok beállításával csoporthoz hozzáadott küldhet üzenetet a `groupName` tulajdonság a SignalR-üzenet.

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

### <a name="2x-java-group-management-output-examples"></a>Java-csoportkezelés 2.x kimeneti példák

SignalR Service lehetővé teszi a felhasználóknak adni csoportokhoz. Egy csoport ezután képes üzeneteket küldeni. Használhatja a `SignalRGroupAction` osztály használatával a `SignalROutput` kimeneti kötést egy felhasználói csoport tagságának kezeléséhez.

#### <a name="add-user-to-a-group"></a>Felhasználó hozzáadása csoporthoz

Az alábbi példa hozzáad egy felhasználót egy csoporthoz.

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

A következő példa eltávolítja a felhasználó a csoportból.

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
|**name**|| Kapcsolatobjektum-adatait a függvény kódját a használt változó neve. |
|**hubName**|**HubName**| Ez az érték, amelynek jön létre a kapcsolati adatok SignalR-központon nevére kell állítani.|
|**userId**|**UserId**| Nem kötelező: A felhasználói azonosító értékét kell beállítani a kulcs hozzáférési jogkivonatot az igényt. |
|**connectionStringSetting**|**ConnectionStringSetting**| A SignalR Service kapcsolati karakterlánc (az alapértelmezett "AzureSignalRConnectionString") tartalmazó alkalmazásbeállítás neve |

### <a name="signalr"></a>SignalR

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `SignalR` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**|| Meg kell `signalR`.|
|**direction**|| Meg kell `out`.|
|**name**|| Kapcsolatobjektum-adatait a függvény kódját a használt változó neve. |
|**hubName**|**HubName**| Ez az érték, amelynek jön létre a kapcsolati adatok SignalR-központon nevére kell állítani.|
|**connectionStringSetting**|**ConnectionStringSetting**| A SignalR Service kapcsolati karakterlánc (az alapértelmezett "AzureSignalRConnectionString") tartalmazó alkalmazásbeállítás neve |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Az Azure Functions fejlesztői és az Azure SignalR Service configuration](../azure-signalr/signalr-concept-serverless-development-config.md)
