---
title: Twilio-kötés Azure Functions
description: Ismerje meg, hogyan használhatók a Twilio-kötések Azure Functions használatával.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c24e8b6098ba33a2e738a7f5f310ae7e65ee516
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925278"
---
# <a name="twilio-binding-for-azure-functions"></a>Azure Functions Twilio kötése

Ez a cikk azt ismerteti, hogyan küldhet szöveges üzeneteket a Azure Functions [Twilio](https://www.twilio.com/) kötéseinek használatával. Azure Functions támogatja a Twilio kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

A Twilio-kötések a [Microsoft. Azure. webjobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet-csomagban, az 1. x verzióban érhetők el. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A Twilio-kötések a [Microsoft. Azure. webjobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet csomagban, 3. x verzióban találhatók. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>Példa – 1. x függvények

Tekintse meg a nyelvspecifikus példát:

* [C#](#c-example)
* [C#parancsfájl (. CSX)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C#például

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely szöveges üzenetet küld, amikor egy üzenetsor-üzenet aktiválja.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

Ez a példa a `TwilioSms` attribútumot használja a metódus visszatérési értékével. Egy másik lehetőség, hogy az attribútumot egy `out SMSMessage` paraméterrel vagy egy `ICollector<SMSMessage>` vagy `IAsyncCollector<SMSMessage>` paraméterrel használja.

### <a name="c-script-example"></a>C#parancsfájl – példa

Az alábbi példa egy Twilio kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy `out` paramétert használ szöveges üzenet küldéséhez.

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Itt a C# szkript kódja:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Aszinkron kódban nem használhatók paraméterek. Példa egy aszinkron C# parancsfájl-kódra:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>JavaScript-példa

Az alábbi példa egy Twilio kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja.

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x-and-higher"></a>Példa – 2. x és újabb függvények

Tekintse meg a nyelvspecifikus példát:

* [2. x +C#](#2x-c-example)
* [2. x + C# parancsfájl (. CSX)](#2x-c-script-example)
* [2. x + JavaScript](#2x-javascript-example)

### <a name="2x-c-example"></a>2. x + C# példa

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely szöveges üzenetet küld, amikor egy üzenetsor-üzenet aktiválja.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

Ez a példa a `TwilioSms` attribútumot használja a metódus visszatérési értékével. Egy másik lehetőség, hogy az attribútumot egy `out CreateMessageOptions` paraméterrel vagy egy `ICollector<CreateMessageOptions>` vagy `IAsyncCollector<CreateMessageOptions>` paraméterrel használja.

### <a name="2x-c-script-example"></a>2. x + C# parancsfájl – példa

Az alábbi példa egy Twilio kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy `out` paramétert használ szöveges üzenet küldéséhez.

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Itt a C# szkript kódja:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Aszinkron kódban nem használhatók paraméterek. Példa egy aszinkron C# parancsfájl-kódra:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>2. x + JavaScript-példa

Az alábbi példa egy Twilio kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja.

Itt a *function. JSON* fájlban található kötési adat:

Példa a function. JSON fájlra:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) attribútumot.

További információ a konfigurálható attribútumok tulajdonságairól: [konfiguráció](#configuration). Példa `TwilioSms` attribútumra a metódus-aláírásban:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Teljes példa: [ C# példa](#c-example).

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `TwilioSms` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

| v1 function. JSON tulajdonság | v2 function. JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|---------|----------------------|
|**type**|**type**| `twilioSms`értékre kell állítani.|
|**direction**|**direction**| `out`értékre kell állítani.|
|**név**|**név**| A Twilio SMS szöveges üzenethez tartozó Function Code-ban használt változó neve. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Ezt az értéket egy olyan Alkalmazásbeállítás nevére kell beállítani, amely a Twilio-fiók biztonsági azonosítóját (például TwilioAccountSid) tárolja. Ha nincs beállítva, az alapértelmezett alkalmazás-beállítás neve "AzureWebJobsTwilioAccountSid". |
|**Aad**|**authTokenSetting**|**AuthTokenSetting**| Ezt az értéket egy olyan Alkalmazásbeállítás nevére kell beállítani, amely a Twilio hitelesítési tokent (például TwilioAccountAuthToken) tárolja. Ha nincs beállítva, az alapértelmezett alkalmazás-beállítás neve "AzureWebJobsTwilioAuthToken". |
|**to**| N/A – megadás a kódban | **Címzett**| Ez az érték arra a telefonszámra van beállítva, amelyre az SMS-szöveg érkezik.|
|**from**|**from** | **From**| Ez az érték arra a telefonszámra van beállítva, amelyet az SMS-szöveg elküldésére használt.|
|**törzse**|**törzse** | **Törzs**| Ez az érték használható az SMS szöveges üzenet kódolására, ha nem kell dinamikusan beállítania a függvény kódjában. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
