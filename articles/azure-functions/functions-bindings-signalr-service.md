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
ms.date: 09/23/2018
ms.author: cshoe
ms.openlocfilehash: 7a7063b9177774c5207746283dc7cd25e3dd5793
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721886"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-szolgáltatáskötések az Azure Functionshöz

Ez a cikk azt ismerteti, hogyan és valós idejű üzeneteket küldeni a kapcsolódó ügyfelek hitelesítéséhez [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) SignalR Service kötések az Azure Functions használatával. Az Azure Functions támogatja a bemeneti és kimeneti kötései SignalR Service.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A SignalR Service kötéseket szerepelnek a [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet-csomag verziója 1.0.0-preview1-*. A csomag forráskódja a [azure-functions-signalrservice-bővítmény](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub-adattárban.

> [!NOTE]
> Az Azure SignalR Service szolgáltatás általánosan elérhető. Azonban SignalR Service – Azure Functions kötések jelenleg előzetes verzióban érhető el.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>A SignalR kapcsolati adatok bemeneti kötéssel

Egy ügyfél csak akkor csatlakozhatnak az Azure SignalR Service, a végpont URL-címe és a egy érvényes hozzáférési jogkivonatot kell lekérnie. A *SignalRConnectionInfo* bemeneti kötésnek a SignalR Service-végpont URL-címe és a egy érvényes tokent a szolgáltatáshoz való csatlakozáshoz használt hoz létre. A jogkivonat időben korlátozott, és a egy kapcsolat egy adott felhasználót hitelesítésre használható, mert ne gyorsítótárazza a jogkivonat és ossza meg az ügyfelek között. Ez a kötés használatával a HTTP-trigger segítségével az ügyfelek által a kapcsolati adatok lekéréséhez.

Tekintse meg az adott nyelvű példa:

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>a bemeneti 2.x C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely beszerzi a SignalR kapcsolati adatokat, használja a bemeneti kötést, és adja vissza, HTTP protokollon keresztül.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Hitelesített jogkivonatok

A függvény egy hitelesített ügyfél akkor aktiválódik, ha egy felhasználói azonosító jogcím a generált token is hozzáadhat. Egyszerűen hozzáadhatja az hitelesítési egy függvényalkalmazáshoz használatával [App Service-hitelesítés] (.. /App-Service/Overview-Authentication-Authorization.MD).

App Service-hitelesítés beállítása nevű HTTP-fejlécek `x-ms-client-principal-id` és `x-ms-client-principal-name` tartalmaznak, a hitelesített felhasználó egyszerű ügyfél-Azonosítóját és nevét, illetve. Beállíthatja a `UserId` tulajdonság értéke kötésének vagy fejléc használatával egy [kifejezés kötés](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>2.x JavaScript bemeneti példa

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
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>Hitelesített jogkivonatok

A függvény egy hitelesített ügyfél akkor aktiválódik, ha egy felhasználói azonosító jogcím a generált token is hozzáadhat. Egyszerűen hozzáadhatja az hitelesítési egy függvényalkalmazáshoz használatával [App Service-hitelesítés] (.. /App-Service/Overview-Authentication-Authorization.MD).

App Service-hitelesítés beállítása nevű HTTP-fejlécek `x-ms-client-principal-id` és `x-ms-client-principal-name` tartalmaznak, a hitelesített felhasználó egyszerű ügyfél-Azonosítóját és nevét, illetve. Beállíthatja a `userId` tulajdonság értéke kötésének vagy fejléc használatával egy [kifejezés kötés](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}`. 

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
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>A SignalR kimeneti kötése

Használja a *SignalR* kimeneti kötése az Azure SignalR Service segítségével egy vagy több üzenet küldése. Üzenetet elküldheti az összes csatlakoztatott ügyfelek, vagy csak a csatlakoztatott ügyfelek, amelyek egy adott felhasználó hitelesített szórási.

Tekintse meg az adott nyelvű példa:

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>Példa a kimenetre 2.x C#

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
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>JavaScript-kimeneti példában 2.x

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
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Küldhet a felhasználónak

Küldhet üzenetet csak egy felhasználónak beállításával hitelesített kapcsolatokhoz a `userId` tulajdonság a SignalR-üzenet.

*Function.JSON* változatlan marad. A következő JavaScript-kódot:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>Konfiguráció

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `SignalRConnectionInfo` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**|| Meg kell `signalRConnectionInfo`.|
|**direction**|| Meg kell `in`.|
|**name**|| Kapcsolatobjektum-adatait a függvény kódját a használt változó neve. |
|**HubName**|**HubName**| Ez az érték, amelynek jön létre a kapcsolati adatok SignalR-központon nevére kell állítani.|
|**userId**|**Felhasználói azonosító**| Nem kötelező: A felhasználói azonosító értékét kell beállítani a kulcs hozzáférési jogkivonatot az igényt. |
|**connectionStringSetting**|**ConnectionStringSetting**| A SignalR Service kapcsolati karakterlánc (az alapértelmezett "AzureSignalRConnectionString") tartalmazó alkalmazásbeállítás neve |

### <a name="signalr"></a>SignalR

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `SignalR` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**|| Meg kell `signalR`.|
|**direction**|| Meg kell `out`.|
|**name**|| Kapcsolatobjektum-adatait a függvény kódját a használt változó neve. |
|**HubName**|**HubName**| Ez az érték, amelynek jön létre a kapcsolati adatok SignalR-központon nevére kell állítani.|
|**connectionStringSetting**|**ConnectionStringSetting**| A SignalR Service kapcsolati karakterlánc (az alapértelmezett "AzureSignalRConnectionString") tartalmazó alkalmazásbeállítás neve |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

