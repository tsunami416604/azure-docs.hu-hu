---
title: Az Azure Functions SendGrid-kötések
description: Az Azure Functions SendGrid-kötések hivatkozása.
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 9ed2b81c12c698822b9542bb6903189c865b572b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277465"
---
# <a name="azure-functions-sendgrid-bindings"></a>Az Azure Functions SendGrid-kötések

Ez a cikk bemutatja, hogyan küldhet e-mailt az Azure Functions [SendGrid-kötések](https://sendgrid.com/docs/User_Guide/index.html) használatával. Az Azure Functions támogatja a SendGrid kimeneti kötését.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x függvények

A SendGrid-kötések a [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet csomag 2.x-es verziójában találhatók. A csomag forráskódja az [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok - 2.x vagy újabb funkciók

A SendGrid-kötések a [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet csomag 3.x-es verziójában találhatók. A csomag forráskódja az [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy Service Bus várólista-eseményindítót és egy SendGrid kimeneti kötést használ.

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

Kihagyhatja az attribútum tulajdonságának `ApiKey` beállítását, ha az API-kulcs az "AzureWebJobsSendGridApiKey" nevű alkalmazásbeállításban van.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy SendGrid kimeneti kötést mutat be egy *function.json* fájlban, és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja.

A *function.json* fájlban a kötési adatok:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy SendGrid kimeneti kötést mutat be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja.

A *function.json* fájlban a kötési adatok:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

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

A következő példa egy HTTP-aktivált függvényt mutat be, amely e-mailt küld a SendGrid kötés használatával. A kötési konfigurációban alapértelmezett értékeket adhat meg. Például a *from* e-mail cím a *function.json*nyelven van konfigurálva. 

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

A következő függvény bemutatja, hogyan adhat egyéni értékeket a választható tulajdonságokhoz.

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

A következő példa `@SendGridOutput` a Java [függvények futásidejű függvénytárából](/java/api/overview/azure/functions/runtime) származó jegyzetet használja egy e-mail küldésére a SendGrid kimeneti kötés használatával.

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

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) attribútumot.

A konfigurálható attribútumtulajdonságokról a [Konfiguráció című témakörben](#configuration)talál további információt. Íme egy `SendGrid` attribútum példa egy metódus aláírás:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
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

A [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) jegyzet lehetővé teszi a SendGrid-kötés deklaratív konfigurálását konfigurációs értékek megadásával. További részletekért tekintse meg a [példát](#example) és a [konfigurációs](#configuration) szakaszokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és `SendGrid` az attribútumban/jegyzetben elérhető kötési konfigurációs tulajdonságokat sorolja fel.

| *function.json* tulajdonság | Attribútum/jegyzet tulajdonság | Leírás | Optional |
|--------------------------|-------------------------------|-------------|----------|
| type |n/a| A beállításnak `sendGrid`a beállítására kell beállítható.| Nem |
| irány |n/a| A beállításnak `out`a beállítására kell beállítható.| Nem |
| név |n/a| A kérelem vagy kérelem törzsének függvénykódjában használt változónév. Ez az `$return` érték akkor van, ha csak egy visszatérési érték van. | Nem |
| apiKey (apiKey) | ApiKey (ApiKey) | Az API-kulcsot tartalmazó alkalmazásbeállítás neve. Ha nincs beállítva, az alapértelmezett alkalmazásbeállítás neve *Az AzureWebJobsSendGridApiKey*lesz.| Nem |
| erre:| Művelet | A címzett e-mail címe. | Igen |
| honnan| Feladó | A feladó e-mail címe. |  Igen |
| Tárgy| Tárgy | Az e-mail tárgya. | Igen |
| szöveg| Szöveg | Az e-mail tartalma. | Igen |

A választható tulajdonságok alapértelmezett értékeit a kötésben definiálják, és programozott módon hozzáadják vagy felülbírálják.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json beállítások

Ez a szakasz a kötéshez a 2.x-es vagy újabb verziókban elérhető globális konfigurációs beállításokat ismerteti. Az alábbi host.json példafájl csak a kötés 2.x+ verzióját tartalmazza. A 2.x-es és az azt meghaladó verziók globális konfigurációs beállításairól az [Azure Functions host.json referencia című témakörében](functions-host-json.md)olvashat bővebben.

> [!NOTE]
> A host.json 1.x függvényében a [host.json témakörben az Azure Functions 1.x host.json hivatkozása](functions-host-json-v1.md)látható.

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
|honnan|n/a|A feladó e-mail címe az összes funkcióban.| 


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)
