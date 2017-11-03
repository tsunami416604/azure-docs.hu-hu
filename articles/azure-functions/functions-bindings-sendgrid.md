---
title: "Az Azure Functions SendGrid kötések |} Microsoft Docs"
description: "Az Azure Functions SendGrid kötések referencia"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: rachelap
ms.openlocfilehash: 4cdafbe05e29d8b483c6b0e1daf41a36583d7b5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-sendgrid-bindings"></a>Az Azure Functions SendGrid kötések

Ez a cikk azt ismerteti, hogyan konfigurálását és az Azure Functions SendGrid kötések használatát. Sendgrid használhatja az Azure Functions programozott módon testreszabott e-mail üzenetek küldéséhez.

Ez a cikk az Azure Functions fejlesztői útmutatót. Ha most ismerkedik az Azure Functions, kezdje a a következőket:

[Az első Azure-függvény létrehozása](functions-create-first-azure-function.md). 
[C#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md), vagy [csomópont](functions-reference-node.md) fejlesztői hivatkozik.

## <a name="functionjson-for-sendgrid-bindings"></a>a SendGrid kötések Function.JSON

Az Azure Functions biztosít SendGrid egy kimeneti kötése. A SendGrid kimeneti kötés lehetővé teszi, hogy a létrehozása és elküldése e-mailben elküldi programozott módon. 

A SendGrid kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság  |Leírás  |
|---------|---------|
|**név**| Kötelező – a kérelem vagy kérelemtörzset függvény a kódban használt változó neve. Ez az érték ```$return``` Ha csak egy visszatérési értéket. |
|**típusa**| Szükséges – kell állítani `sendGrid`.|
|**iránya**| Szükséges – kell állítani `out`.|
|**apiKey**| Szükséges – a függvény App Alkalmazásbeállítások tárolja az API-kulcs nevét kell lennie. |
|**a**| a címzett e-mail címét. |
|**a**| a feladó e-mail címe. |
|**tulajdonos**| az e-mail tárgyát. |
|**szöveg**| az e-mailek tartalmának. |

Példa **function.json**:

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

> [!NOTE]
> Az Azure Functions tárolja a kapcsolati adatokat és API-kulcsokat beállításait, hogy nem ellenőrzi azokat a verziókövetési tárházzal. Ez a művelet megvédi a bizalmas adatokat.
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>A SendGrid például C# kimeneti kötése

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
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

## <a name="node-example-of-the-sendgrid-output-binding"></a>A SendGrid csomópont példa kimeneti kötése

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

## <a name="next-steps"></a>Következő lépések
Az Azure Functions más kötései és eseményindítók kapcsolatos információkért lásd: 
- [Az Azure Functions eseményindítók és kötések fejlesztői segédanyagai](functions-triggers-bindings.md)

- [Gyakorlati tanácsok az Azure Functions](functions-best-practices.md) néhány ajánlott eljárás az Azure Functions létrehozásakor használandó sorolja fel.

- [Az Azure Functions fejlesztői segédanyagai](functions-reference.md) programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
