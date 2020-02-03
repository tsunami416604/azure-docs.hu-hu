---
title: Twilio-kötés Azure Functions
description: Ismerje meg, hogyan használhatók a Twilio-kötések Azure Functions használatával.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1426d6e770cca566c4b77ca4742e2f8a0fbb5465
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715058"
---
# <a name="twilio-binding-for-azure-functions"></a>Azure Functions Twilio kötése

Ez a cikk azt ismerteti, hogyan küldhet szöveges üzeneteket a Azure Functions [Twilio](https://www.twilio.com/) kötéseinek használatával. Azure Functions támogatja a Twilio kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Twilio-kötések a [Microsoft. Azure. webjobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet-csomagban, az 1. x verzióban érhetők el. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A Twilio-kötések a [Microsoft. Azure. webjobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet csomagban, 3. x verzióban találhatók. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Példa – 2. x és újabb függvények

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

A következő JavaScript-kódot:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan küldhet SMS-üzenetet a kimeneti kötés használatával a következő *function. js*fájlban megadott módon.

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

Az SMS-üzenet elküldéséhez átadhat egy szerializált JSON-objektumot a `func.Out` paraméternek.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="javatabjava"></a>[Java](#tab/java)

Az alábbi példa bemutatja, hogyan lehet SMS-üzenetet küldeni a [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) -jegyzet használatával. A `to`, `from`és `body` értékeit akkor is meg kell adni az attribútum definíciójában, ha programozott módon felülbírálja őket.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

Teljes példa: [ C# példa](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

Helyezzen [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) jegyzetet egy [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.outputbinding) paraméterre, ahol `T` lehet bármilyen natív Java-típus, például `int`, `String`, `byte[]`vagy POJO.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `TwilioSms` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

| v1 function. JSON tulajdonság | v2 function. JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|---------|----------------------|
|**type**|**type**| `twilioSms`értékre kell állítani.|
|**direction**|**direction**| `out`értékre kell állítani.|
|**név**|**név**| A Twilio SMS szöveges üzenethez tartozó Function Code-ban használt változó neve. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Ezt az értéket egy olyan Alkalmazásbeállítások nevére kell beállítani, amely a Twilio-fiók biztonsági azonosítóját (`TwilioAccountSid`) tárolja. Ha nincs beállítva, az alapértelmezett alkalmazás-beállítás neve "AzureWebJobsTwilioAccountSid". |
|**Aad**|**authTokenSetting**|**AuthTokenSetting**| Ezt az értéket egy olyan Alkalmazásbeállítások nevére kell beállítani, amely a Twilio hitelesítési tokent (`TwilioAccountAuthToken`) tárolja. Ha nincs beállítva, az alapértelmezett alkalmazás-beállítás neve "AzureWebJobsTwilioAuthToken". |
|**hogy**| N/A – megadás a kódban | **Címzett**| Ez az érték arra a telefonszámra van beállítva, amelyre az SMS-szöveg érkezik.|
|**a**|**a** | **A**| Ez az érték arra a telefonszámra van beállítva, amelyet az SMS-szöveg elküldésére használt.|
|**törzse**|**törzse** | **Törzs**| Ez az érték használható az SMS szöveges üzenet kódolására, ha nem kell dinamikusan beállítania a függvény kódjában. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
