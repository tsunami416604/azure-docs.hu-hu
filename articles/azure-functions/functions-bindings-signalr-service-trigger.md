---
title: A Azure Functions jelző szolgáltatás triggerének kötése
description: Útmutató a szignáló szolgáltatás üzeneteinek küldéséhez Azure Functionsról.
author: chenyl
ms.topic: reference
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: c2ad9b6c4410a62d5652050406e05be4cde5fab0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830706"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>A signaler szolgáltatás triggerének kötése Azure Functions

A *signaler* trigger-kötés használatával válaszolhat az Azure Signaler szolgáltatásból küldött üzenetekre. Ha a függvény aktiválódik, a függvénynek átadott üzenetek JSON-objektumként vannak értelmezve.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-signalr-service.md).

## <a name="example"></a>Példa

Az alábbi példa egy olyan függvényt mutat be, amely az trigger kötés használatával üzenetet fogad, és naplózza az üzenetet.

# <a name="c"></a>[C#](#tab/csharp)

A signaler szolgáltatás triggerének kötése C#-ban két programozási modell van. Osztály alapú modell és hagyományos modell. Az osztályon alapuló modell egységes Signaler kiszolgálóoldali programozási élményt biztosíthat. A és a hagyományos modell nagyobb rugalmasságot és hasonló funkciókat biztosít más függvények kötésekhez.

### <a name="with-class-based-model"></a>Osztály alapú modellel

Részletekért lásd a [Class-alapú modellt](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) .

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Hagyományos modellel

A hagyományos modell a C# által fejlesztett Azure Function egyezményt engedelmeskedik. Ha még nem ismeri, megismerheti a [dokumentumokat](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-class-library).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Az attribútum használatának `[SignalRParameter]` egyszerűsítése`ParameterNames`

Mivel a használata nehézkes a használathoz `ParameterNames` , `SignalRParameter` ugyanezt a célt szolgálja.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Itt látható a Python-kód:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Konfiguráció

### <a name="signalrtrigger"></a>SignalRTrigger

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `SignalRTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**típusa**| n.a. | Értékre kell állítani `SignalRTrigger` .|
|**direction**| n.a. | Értékre kell állítani `in` .|
|**név**| n.a. | A függvény kódjában használt változó neve a trigger meghívása környezeti objektumhoz. |
|**hubName**|**HubName**| Ezt az értéket az aktiválni kívánt függvény jelző központjajának nevére kell beállítani.|
|**Kategória**|**Kategória**| Ezt az értéket úgy kell beállítani, hogy az üzenetek kategóriája legyen az elindítható függvény számára. A kategória a következő értékek egyike lehet: <ul><li>**kapcsolatok**: a *csatlakoztatott* és a *leválasztott* eseményeket is beleértve</li><li>**üzenetek**: beleértve az összes többi eseményt, kivéve a *kapcsolatok* kategóriájában lévőket</li></ul> |
|**esemény**|**Esemény**| Ezt az értéket úgy kell beállítani, hogy a függvény elindítsa az üzenetek eseményét. Az *üzenetek* kategória esetében az esemény az a *cél* , amely az ügyfelek által küldött [Meghívási üzenetben](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) szerepel. A *kapcsolatok* kategória esetében csak a *csatlakoztatott* és a *leválasztott* kapcsolat van használatban. |
|**parameterNames**|**ParameterNames**| Választható A paraméterekhez kötődő nevek listája. |
|**connectionStringSetting**|**ConnectionStringSetting**| A jelző szolgáltatás kapcsolódási sztringjét tartalmazó Alkalmazásbeállítás neve (alapértelmezett érték: "AzureSignalRConnectionString") |

## <a name="payload"></a>Adattartalom

Az trigger típusú bemeneti típust `InvocationContext` vagy egyéni típusként deklaráljuk. Ha úgy dönt `InvocationContext` , hogy teljes hozzáférést kap a kérelem tartalmához. Egyéni típus esetén a futásidejű megpróbálja elemezni a JSON-kérés törzsét az objektum tulajdonságainak beállításához.

### <a name="invocationcontext"></a>InvocationContext

A InvocationContext tartalmazza az üzenetben a Signaler szolgáltatásból küldött összes tartalmat.

|Tulajdonság a InvocationContext | Description|
|------------------------------|------------|
|Argumentumok| Az *üzenetek* kategóriához érhető el. *Argumentumokat* tartalmaz a [Meghívási üzenetben](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Hiba| *Leválasztott* eseményhez elérhető. Ez akkor lehet üres, ha a kapcsolatok hiba nélkül lezárult, vagy a hibaüzeneteket tartalmazza.|
|Hub| A hub neve, amelyhez az üzenet tartozik.|
|Kategória| Az üzenet kategóriája.|
|Esemény| Az üzenet eseménye.|
|ConnectionId| Az üzenetet küldő ügyfél kapcsolatazonosító.|
|UserId (Felhasználóazonosító)| Az üzenetet küldő ügyfél felhasználói identitása.|
|Fejlécek| A kérelem fejlécei.|
|Lekérdezés| A kérés lekérdezése, amikor az ügyfelek csatlakoznak a szolgáltatáshoz.|
|Jogcímek| Az ügyfél jogcímei.|

## <a name="using-parameternames"></a>Az `ParameterNames` használata

A tulajdonsága `ParameterNames` `SignalRTrigger` lehetővé teszi, hogy a Meghívási üzenetek argumentumait a függvények paramétereinek megfelelően kösse. Ez kényelmes módot biztosít az argumentumai eléréséhez `InvocationContext` .

Tegyük fel, hogy rendelkezik egy JavaScript-jelzővel, amely `broadcast` két argumentummal próbálkozik az Azure-függvény metódusának meghívásával.

```javascript
await connection.invoke("broadcast", message1, message2);
```

Ezt a két argumentumot elérheti a paraméterből, valamint hozzárendelheti a paraméter típusát is a használatával `ParameterNames` .

### <a name="remarks"></a>Megjegyzések

A paraméter kötése esetén a sorrend számít. A használata esetén `ParameterNames` a sorrend `ParameterNames` megegyezik az ügyfélben meghívott argumentumok sorrendjével. Ha a C#-ban használja az attribútumot `[SignalRParameter]` , akkor az Azure Function metódusokban az argumentumok sorrendje megegyezik az ügyfelek argumentumai sorrendjével.

`ParameterNames`és az attribútum `[SignalRParameter]` **nem** használható egyszerre, vagy kivételt fog kapni.

## <a name="send-messages-to-signalr-service-trigger-binding"></a>Üzenetek küldése a jelző szolgáltatás triggerének kötéséhez

Az Azure Function létrehoz egy URL-címet a jelző szolgáltatás triggerének kötéséhez, és a következőképpen van formázva:

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

Az az `API_KEY` Azure Function által generált. A `API_KEY` from Azure Portal a signaler szolgáltatás-trigger kötésének használatakor kérheti le.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API-kulcs":::

Ezt az URL-címet a `UrlTemplate` signaler szolgáltatás felsőbb rétegbeli beállításainál kell beállítania.

## <a name="next-steps"></a>További lépések

* [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](../azure-signalr/signalr-concept-serverless-development-config.md)
* [A signaler szolgáltatás Triggerének kötési mintája](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)