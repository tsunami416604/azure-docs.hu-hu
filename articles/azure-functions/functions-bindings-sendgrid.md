---
title: SendGrid-kötések Azure Functions
description: Azure Functions SendGrid kötések hivatkozása.
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 9ed2b81c12c698822b9542bb6903189c865b572b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277465"
---
# <a name="azure-functions-sendgrid-bindings"></a>SendGrid-kötések Azure Functions

Ez a cikk azt ismerteti, hogyan küldhet e-mailt [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) -kötések használatával Azure Functionsban. Azure Functions támogatja a SendGrid kimeneti kötését.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

A SendGrid-kötések a [Microsoft. Azure. webjobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet csomagban, 2. x verzióban találhatók. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A SendGrid-kötések a [Microsoft. Azure. webjobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet csomagban, 3. x verzióban találhatók. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy Service Bus üzenetsor-triggert és egy SendGrid kimeneti kötést használ.

### <a name="synchronous"></a>Szinkron

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>Aszinkron

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Ha az API-kulcs egy " `ApiKey` AzureWebJobsSendGridApiKey" nevű alkalmazás-beállításban van, akkor kihagyhatja az attribútum tulajdonságának beállítását.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A következő példa egy SendGrid kimeneti kötést mutat be egy *function. JSON* fájlban és egy [C# parancsfájl-függvényben](functions-reference-csharp.md) , amely a kötést használja.

Itt található a *function. JSON* fájlban található kötési adat:

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy SendGrid kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja.

Itt található a *function. JSON* fájlban található kötési adat:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, input) {
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy HTTP által aktivált függvényt mutat be, amely a SendGrid kötés használatával küld e-mailt. A kötési konfigurációban alapértelmezett értékeket adhat meg. A *from* e-mail cím például a *function. JSON*fájlban van konfigurálva. 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

A következő függvény bemutatja, hogyan adhat meg egyéni értékeket a választható tulajdonságokhoz.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa a `@SendGridOutput` [Java functions runtime library](/java/api/overview/azure/functions/runtime) jegyzetét használja egy e-mailt küldeni a SendGrid kimeneti kötés használatával.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) attribútumot.

További információ a konfigurálható attribútumok tulajdonságairól: [konfiguráció](#configuration). `SendGrid` Példa a metódus aláírására:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

A teljes példa: [C# példa](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) jegyzet lehetővé teszi a SendGrid-kötések deklaratív konfigurálását a konfigurációs értékek megadásával. További részletekért tekintse meg a [példa](#example) és a [konfigurációs](#configuration) szakaszt.

---

## <a name="configuration"></a>Configuration

A következő táblázat a *function. JSON* fájlban és az `SendGrid` attribútumban/megjegyzésben elérhető kötési konfigurációs tulajdonságokat sorolja fel.

| *function. JSON* -tulajdonság | Attribútum/Megjegyzés tulajdonság | Leírás | Optional |
|--------------------------|-------------------------------|-------------|----------|
| type |n/a| Értékre kell állítani `sendGrid`.| Nem |
| irány |n/a| Értékre kell állítani `out`.| Nem |
| név |n/a| A kérelem vagy a kérelem törzse függvény kódjában használt változó neve. Ez az érték `$return` akkor van, ha csak egy visszatérési érték van. | Nem |
| apiKey | ApiKey | Az API-kulcsot tartalmazó Alkalmazásbeállítás neve. Ha nincs beállítva, az alapértelmezett Alkalmazásbeállítás neve *AzureWebJobsSendGridApiKey*.| Nem |
| erre:| Művelet | A címzett e-mail-címe. | Igen |
| a| Feladó | A feladó e-mail-címe. |  Igen |
| tulajdonos| Tárgy | Az e-mail tárgya. | Igen |
| szöveg| Szöveg | Az e-mail tartalma. | Igen |

A választható tulajdonságok rendelkezhetnek a kötésben definiált alapértelmezett értékekkel, és akár programozott módon is hozzáadhatók vagy felülbíráltak.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>gazdagép. JSON-beállítások

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. A következő példa a Host. JSON fájlt tartalmazza, csak a 2. x + beállításokat a kötéshez. További információ a 2. x verzióban található globális konfigurációs beállításokról: a [Host. JSON dokumentációja Azure functions](functions-host-json.md).

> [!NOTE]
> Az 1. x függvények Host. JSON fájljának hivatkozását lásd: [Host. JSON-dokumentáció Azure functions 1. x-hez](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|a|n/a|A küldő e-mail-címe az összes függvényen belül.| 


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
