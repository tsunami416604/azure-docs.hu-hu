---
title: Az Azure Functions SendGrid kötések
description: Az Azure Functions SendGrid kötések hivatkozás.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/29/2017
ms.author: tdykstra
ms.openlocfilehash: 0cd5730d049749949db13f29499e268a1ebccc18
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830612"
---
# <a name="azure-functions-sendgrid-bindings"></a>Az Azure Functions SendGrid kötések

Ez a cikk ismerteti, hogyan küldjön e-mailek [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) Azure Functions kötések. Az Azure Functions SendGrid egy kimeneti kötése támogatja.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x működik

A SendGrid kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-csomag verziója 2.x. A csomag forráskódja van a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub-tárházban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok - 2.x működik

A SendGrid kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-csomag verziója 3.x. A csomag forráskódja van a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub-tárházban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , hogy indítás, használja a Service Bus-üzenetsorba, és a SendGrid kimeneti kötése.

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

Akkor kihagyhatja a attribútum `ApiKey` tulajdonság, ha az API-kulcs "AzureWebJobsSendGridApiKey" nevű Alkalmazásbeállítás szerepel.

### <a name="c-script-example"></a>C# parancsfájl – példa

A következő példa bemutatja a SendGrid kimeneti kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja.

Itt az kötés adatai a *function.json* fájlt:

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

A [konfigurációs](#configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
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

### <a name="javascript-example"></a>JavaScript – példa

A következő példa bemutatja a SendGrid kimeneti kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja.

Itt az kötés adatai a *function.json* fájlt:

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

A [konfigurációs](#configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

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

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) attribútum.

Konfigurálható attribútumtulajdonságok kapcsolatos információkért lásd: [konfigurációs](#configuration). Íme egy `SendGrid` metódus-aláírás attribútum példát:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Tekintse meg a teljes például [C# példa](#c-example).

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `SendGrid` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**|| Szükséges – kell állítani `sendGrid`.|
|**direction**|| Szükséges – kell állítani `out`.|
|**name**|| Kötelező – a kérelem vagy kérelemtörzset függvény a kódban használt változó neve. Ez az érték ```$return``` Ha csak egy visszatérési értéket. |
|**apiKey**|**ApiKey**| Az API-kulcsot tartalmazó alkalmazásbeállítás neve. Ha nincs beállítva, a beállítás alapértelmezett alkalmazás nevének megadása "AzureWebJobsSendGridApiKey".|
|**A**|**Címzett**| a címzett e-mail címét. |
|**A**|**A**| a feladó e-mail címe. |
|**subject**|**Tárgy**| az e-mail tárgyát. |
|**Szöveg**|**Szöveg**| az e-mailek tartalmának. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
