---
title: Azure Functions Twilio kötés
description: Ismerje meg, hogyan használhatja a Twilio-kötéseket az Azure Functions szolgáltatással.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1426d6e770cca566c4b77ca4742e2f8a0fbb5465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715058"
---
# <a name="twilio-binding-for-azure-functions"></a>Twilio-kötés az Azure Functionshez

Ez a cikk bemutatja, hogyan küldhet szöveges üzeneteket [twilio-kötések](https://www.twilio.com/) használatával az Azure Functionsben. Az Azure Functions támogatja a Twilio kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x függvények

A Twilio-kötések a [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet csomag 1.x-es verziójában találhatók. A csomag forráskódja az [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok - 2.x vagy újabb funkciók

A Twilio-kötések a [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet csomag 3.x-es verziójában találhatók. A csomag forráskódja az [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Példa - 2.x és újabb függvények

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely szöveges üzenetet küld, ha egy üzenetsor-üzenet váltja ki.

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

Ez a `TwilioSms` példa az attribútumot használja a metódus visszatérési értékével. Egy másik lehetőség, hogy az `out CreateMessageOptions` attribútumot `ICollector<CreateMessageOptions>` `IAsyncCollector<CreateMessageOptions>` egy paraméterrel, vagy egy vagy paraméterrel használjuk.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy Twilio kimeneti kötést mutat be egy *function.json* fájlban, és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény `out` egy paramétert használ szöveges üzenet küldéséhez.

A *function.json* fájlban az adatok kötése:

Példa function.json:

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

Itt a C# parancsfájlkód:

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

Az aszinkron kódban nem használhatók paraméterek. Íme egy aszinkron C# parancsfájlkód példa:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy Twilio kimeneti kötést mutat be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja.

A *function.json* fájlban az adatok kötése:

Példa function.json:

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

Itt a JavaScript-kód:

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

# <a name="python"></a>[Python](#tab/python)

A következő példa bemutatja, hogyan küldhet SMS-üzenetet a következő *function.js*fájlban meghatározott kimeneti kötéssel.

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

Az SMS-üzenet elküldéséhez átadhat `func.Out` egy szerializált JSON-objektumot a paraméternek.

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

# <a name="java"></a>[Java](#tab/java)

A következő példa bemutatja, hogyan használhatja a [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) jegyzet egy SMS-üzenet küldéséhez. A `to`, `from`és `body` akkor is kötelező az attribútumdefinícióban, ha programozott módon felülbírálja őket.

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

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) attribútumot.

A konfigurálható attribútumtulajdonságokról a [Konfiguráció című témakörben](#configuration)talál további információt. Íme egy `TwilioSms` attribútum példa egy metódus aláírás:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Egy teljes példa, lásd: [C# példa](#example).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

TwilioSmsOutput jegyzet elhelyezése `T` egy [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.outputbinding) olyan paraméteren, `int`amely `String` `byte[]`bármely natív Java típus lehet, például , , vagy POJO típus. [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput)

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `TwilioSms` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

| v1 function.json tulajdonság | v2 function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|---------|----------------------|
|**Típus**|**Típus**| a beállítását `twilioSms`a beállításra kell állítani.|
|**direction**|**direction**| a beállítását `out`a beállításra kell állítani.|
|**név**|**név**| A Twilio SMS szöveges üzenet függvénykódjában használt változónév. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Ezt az értéket a Twilio-fiók sid (`TwilioAccountSid`) tartalmazó alkalmazásbeállítás nevére kell beállítani. Ha nincs beállítva, az alapértelmezett alkalmazásbeállítás neve "AzureWebJobsTwilioAccountSid". |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Ezt az értéket a Twilio hitelesítési jogkivonatot (`TwilioAccountAuthToken`tartalmazó alkalmazásbeállítás nevére) kell beállítani. Ha nincs beállítva, az alapértelmezett alkalmazásbeállítás neve "AzureWebJobsTwilioAuthToken". |
|**a**| N/A - adja meg a kódot | **Címzett**| Ez az érték arra a telefonszámra van beállítva, amelynek az SMS-szöveg et küldi.|
|**honnan**|**honnan** | **Honnan**| Ez az érték arra a telefonszámra van beállítva, amelyről az SMS-szöveg et küldi.|
|**Szervezet**|**Szervezet** | **Törzs**| Ez az érték használható az SMS szöveges üzenet kemény kódolására, ha nem kell dinamikusan beállítania a funkció kódjában. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)
