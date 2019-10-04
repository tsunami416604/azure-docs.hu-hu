---
title: SendGrid-kötések Azure Functions
description: Azure Functions SendGrid kötések hivatkozása.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: a0d12639ce074c3ed105513a3d90e323e30d1087
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155072"
---
# <a name="azure-functions-sendgrid-bindings"></a>SendGrid-kötések Azure Functions

Ez a cikk azt ismerteti, hogyan küldhet e-mailt [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) -kötések használatával Azure Functionsban. Azure Functions támogatja a SendGrid kimeneti kötését.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A SendGrid-kötések a [Microsoft. Azure. webjobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet csomagban, 2. x verzióban találhatók. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A SendGrid-kötések a [Microsoft. Azure. webjobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet csomagban, 3. x verzióban találhatók. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) tárházban található.

> [!NOTE]
> A 2. x verzió nem hozza létre a `ServiceBusTrigger` példányban konfigurált témakört vagy előfizetést. A 2. x verzió a [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) alapul, és nem kezeli a várólista-kezelést.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Példa

Tekintse meg az adott nyelvű példa:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>C#például

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy Service Bus üzenetsor-triggert és egy SendGrid kimeneti kötést használ.

#### <a name="synchronous-c-example"></a>Szinkron C# példa:

```cs
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
#### <a name="asynchronous-c-example"></a>Aszinkron C# példa:

```cs
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

### <a name="c-script-example"></a>C#parancsfájl – példa

Az alábbi példa egy SendGrid kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja.

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

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

### <a name="java-example"></a>Java-példa

Az alábbi példa a `@SendGridOutput` [Java functions runtime library](/java/api/overview/azure/functions/runtime) jegyzetét használja egy e-mailt küldeni a SendGrid kimeneti kötés használatával.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>JavaScript-példa

Az alábbi példa egy SendGrid kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja.

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

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

## <a name="attributes"></a>Attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) attribútumot.

További információ a konfigurálható attribútumok tulajdonságairól: [konfiguráció](#configuration). Íme egy `SendGrid` attribútum példa egy podpis metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Teljes példa: [ C# példa](#c-example).

## <a name="configuration"></a>Konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `SendGrid` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**|| Kötelező – a `sendGrid`következőre kell beállítani:.|
|**direction**|| Kötelező – a `out`következőre kell beállítani:.|
|**name**|| Kötelező – a kérelem vagy a kérelem törzse függvény kódjában használt változó neve. Ez az érték ```$return``` akkor van, ha csak egy visszatérési érték van. |
|**apiKey**|**ApiKey**| Az API-kulcsot tartalmazó Alkalmazásbeállítás neve. Ha nincs beállítva, az alapértelmezett alkalmazás-beállítás neve "AzureWebJobsSendGridApiKey".|
|**to**|**To**| a címzett e-mail-címe. |
|**a**|**From**| a feladó e-mail-címe. |
|**subject**|**Subject**| az e-mail tárgya. |
|**szöveg**|**Text**| az e-mail tartalma. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz ismerteti a globális konfigurációs beállításoknak a kötéshez verziójában elérhető 2.x. Az alábbi példa host.json-fájl csak a verzió 2.x beállításait tartalmazza ezt a kötést. További információ a globális konfigurációs beállításoknak verzióban 2.x verzióját, lásd: [verzióját az Azure Functions – host.json referencia 2.x](functions-host-json.md).

> [!NOTE]
> Az a funkciók host.json odkaz 1.x, lásd: [Azure Functions – host.json referencia 1.x](functions-host-json-v1.md).

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
|from|n/a|A küldő e-mail-címe az összes függvényen belül.| 


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
