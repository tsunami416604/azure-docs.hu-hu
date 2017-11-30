---
title: "Az Azure Functions SendGrid kötések"
description: "Az Azure Functions SendGrid kötések hivatkozás."
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
ms.openlocfilehash: f24c2aecf44dd44fec05dc9a4d156ff408b0c953
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-sendgrid-bindings"></a>Az Azure Functions SendGrid kötések

Ez a cikk ismerteti, hogyan küldjön e-mailek [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) Azure Functions kötések. Az Azure Functions SendGrid egy kimeneti kötése támogatja.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Példa

Tekintse meg a nyelvspecifikus példát:

* [Lefordított C#](#c-example)
* [C# parancsfájl](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# – példa

Az alábbi példa mutatja egy [előre le a C# függvény fordítva](functions-dotnet-class-library.md) , hogy indítás, használja a Service Bus-üzenetsorba, és a SendGrid kimeneti kötése.

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
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

A [előre le fordítva C#](functions-dotnet-class-library.md) funkciók használata a [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) attribútumot, amelyet a NuGet-csomag [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid).

Konfigurálható attribútumtulajdonságok kapcsolatos információkért lásd: [konfigurációs](#configuration). Íme egy `SendGrid` metódus-aláírás attribútum példát:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Tekintse meg a teljes például [előre le fordítva C# példa](#c-example).

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `SendGrid` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa**|| Szükséges – kell állítani `sendGrid`.|
|**iránya**|| Szükséges – kell állítani `out`.|
|**név**|| Kötelező – a kérelem vagy kérelemtörzset függvény a kódban használt változó neve. Ez az érték ```$return``` Ha csak egy visszatérési értéket. |
|**apiKey**|**ApiKey**| Az API-kulcsot tartalmazó alkalmazásbeállítás neve. Ha nincs beállítva, a beállítás alapértelmezett alkalmazás nevének megadása "AzureWebJobsSendGridApiKey".|
|**a**|**Címzett**| a címzett e-mail címét. |
|**a**|**A**| a feladó e-mail címe. |
|**tulajdonos**|**Tárgy**| az e-mail tárgyát. |
|**szöveg**|**Szöveg**| az e-mailek tartalmának. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)