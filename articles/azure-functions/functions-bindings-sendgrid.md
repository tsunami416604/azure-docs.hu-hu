---
title: Az Azure Functions SendGrid-kötések
description: Az Azure Functions SendGrid-kötések hivatkozhat.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: glenga
ms.openlocfilehash: 79fb24e85dea5a8d8d9ca637612ea4a65339a4e3
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087412"
---
# <a name="azure-functions-sendgrid-bindings"></a>Az Azure Functions SendGrid-kötések

Ez a cikk azt ismerteti, hogyan e-mailek küldése használatával [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) kötések az Azure Functions szolgáltatásban. Az Azure Functions kimeneti kötés SendGrid támogatja.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A SendGrid-kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-csomag verziója 2.x. A csomag forráskódja a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub-adattárban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A SendGrid-kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-csomag verziója 3.x. A csomag forráskódja a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Példa

Tekintse meg az adott nyelvű példa:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , hogy használja a Service Bus-üzenetsorba aktiválása és a SendGrid kimeneti kötést.

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

Kihagyhatja az attribútum beállítása `ApiKey` Ha API-kulcsát egy alkalmazásbeállításhoz "AzureWebJobsSendGridApiKey" nevű tulajdonság.

### <a name="c-script-example"></a>C#-szkript példa

Az alábbi példa bemutatja egy kötelező a SendGrid-kimenet egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja.

Itt van a kötési adatait a *function.json* fájlt:

```json 
{
    "bindings": [
        {
            "name": "message",
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

Íme a C#-szkriptkódot:

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;
using Microsoft.Extensions.Logging;

public static void Run(ILogger log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

### <a name="javascript-example"></a>JavaScript-példa

Az alábbi példa bemutatja egy kötelező a SendGrid-kimenet egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja.

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

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) attribútum.

Konfigurálható attribútum-tulajdonságokkal kapcsolatos információkért lásd: [konfigurációs](#configuration). Íme egy `SendGrid` attribútum példa egy podpis metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Egy teljes példa: [C#-példa](#c-example).

## <a name="configuration"></a>Konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `SendGrid` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**|| Kötelező – kell állítani `sendGrid`.|
|**direction**|| Kötelező – kell állítani `out`.|
|**name**|| Kötelező – a a függvény kódját a kérelem vagy a kérelem törzsében használt változó neve. Ez az érték ```$return``` Ha csak egy visszatérési értéket tartalmaz. |
|**apiKey**|**ApiKey**| Az API-kulcsot tartalmazó alkalmazásbeállítás neve. Ha nincs beállítva, az alapértelmezett alkalmazás beállítása a név "AzureWebJobsSendGridApiKey".|
|**A**|**Címzett**| a címzett e-mail címét. |
|**A**|**A**| a feladó e-mail címe. |
|**subject**|**Tárgy**| az e-mail tárgya. |
|**Szöveg**|**Szöveg**| az e-mail tartalma. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
