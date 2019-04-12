---
title: Notification Hubs kötéseit az Azure Functions szolgáltatáshoz
description: Megismerheti az Azure Notification Hub-kötés az Azure Functions használatával.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 79ea9455fec7d31f800b2b5d36df6a2a53f502c3
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490962"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>A Notification Hubs kimeneti kötése az Azure Functions szolgáltatáshoz

Ez a cikk azt ismerteti, hogyan küldhetők leküldéses értesítések használatával [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) kötések az Azure Functions szolgáltatásban. Az Azure Functions támogatja a kimeneti kötések Notification hubs szolgáltatásban.

Az Azure Notification Hubs esetében a Platform értesítések szolgáltatástól (PNS) használni kívánt kell konfigurálni. Az ügyfélalkalmazás a leküldéses értesítések küldése a Notification hubs használatával kapcsolatban lásd: [Ismerkedés a Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) és a cél ügyfélplatform kiválasztása az oldal tetején a legördülő listából.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google rendelkezik [Google Cloud-Messaging (GCM) értéke Firebase Cloud Messaging (FCM) elavult](https://developers.google.com/cloud-messaging/faq). A kimeneti kötés nem támogatja az FCM. FCM használatával értesítéseket küldeni, használja a [Firebase API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) közvetlenül a a függvény vagy [Sablonértesítők](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Notification Hubs kötéseit szerepelnek a [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet-csomag verziója 1.x. A csomag forráskódja a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub-adattárban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

Ez a kötés nem érhető el a függvények 2.x.

## <a name="example---template"></a>Példa – sablon

Az értesítéseket küld lehet natív értesítések vagy [Sablonértesítők](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Natív értesítések célplatform egy adott ügyfél konfigurált a `platform` a kimeneti kötés tulajdonságát. A sablon értesítést segítségével célozhat meg több platformot.   

Tekintse meg az adott nyelvű példa:

* [C#-szkript – ki paraméter](#c-script-template-example---out-parameter)
* [C#-szkript – aszinkron](#c-script-template-example---asynchronous)
* [C#-szkript - JSON](#c-script-template-example---json)
* [C#-szkript – könyvtár-típusok](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# sablon-példaszkript – ki paraméter

Ebben a példában értesítést küld egy [sablon regisztrációs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , amely tartalmazza a `message` helyőrző a sablonban.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>C# sablon-példaszkript – aszinkron

Ha aszinkron kódot használ, ki paraméterek nem engedélyezettek. Ebben az esetben `IAsyncCollector` való visszatéréshez a sablon értesítést. A következő kódot a fenti kód aszinkron példája. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C# sablon-példaszkript – JSON

Ebben a példában egy értesítést küld egy [sablon regisztrációs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , amely tartalmaz egy `message` helyőrző a sablonban érvényes JSON-karakterlánc használatával.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# sablon-példaszkript – könyvtár-típusok

Ez a példa bemutatja, hogyan használható a definiált típusok a [a Microsoft Azure Notification Hubs kódtárat](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>F#sablon példa

Ebben a példában értesítést küld egy [sablon regisztrációs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) tartalmazó `location` és `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>JavaScript a példasablonban

Ebben a példában értesítést küld egy [sablon regisztrációs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) tartalmazó `location` és `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Példa – natív APNS

Ez a C#-példaszkript bemutatja, hogyan natív APNS értesítést küldeni. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Példa – natív WNS

Ez a C#-példaszkript bemutatja, hogyan használja a-típusokkal a [a Microsoft Azure Notification Hubs kódtárat](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) natív WNS bejelentési értesítést küldeni. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [Értesítésiközpont](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) attribútum.

Az attribútum a konstruktor paramétereket és a tulajdonságok leírását a [konfigurációs](#configuration) szakaszban.

## <a name="configuration"></a>Konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `NotificationHub` attribútum:

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** |n/a| Állítsa "Értesítésiközpont". |
|**irány** |n/a| Állítsa "out". | 
|**név** |n/a| Az értesítési központ üzenet függvénykódot a használt változó neve. |
|**tagExpression** |**TagExpression** | Címke kifejezések lehetővé teszik, hogy az eszközök, amelyek megfelelnek a címkét alkotó kifejezés értesítések fogadására regisztrált értesítések kézbesítendő megadását.  További információkért lásd: [az Útválasztás és címke kifejezések](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**HubName** | **HubName** | Az Azure Portalon az értesítésiközpont-erőforrás neve. |
|**kapcsolat** | **ConnectionStringSetting** | A Notification Hubs kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve.  A kapcsolati karakterlánc értékre kell állítani a *DefaultFullSharedAccessSignature* értékét az értesítési központban. Lásd: [kapcsolati karakterlánc beállítása](#connection-string-setup) a cikk későbbi részében.|
|**Platform** | **Platform** | A platform tulajdonság azt jelzi, hogy az ügyfél platformja az értesítési célokat. Alapértelmezés szerint ha a platform tulajdonság nem szerepel a kimeneti kötés Sablonértesítők használható bármilyen platformon, az Azure Notification Hub konfigurált lehetőséget. A sablonok általában használata a platform értesítések az Azure Notification Hub közötti küldése további információkért lásd: [sablonok](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Ha a beállítás, **platform** musí mít jednu z následujících hodnot: <ul><li><code>apns</code>&mdash;Apple Push Notification szolgáltatás. Az értesítési központ konfigurálása az APNS és a egy ügyfélalkalmazás az értesítés fogadása a további információkért lásd: [Sending leküldéses értesítések iOS-re az Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Az értesítési központ konfigurálása az ADM, és az értesítés fogadása a Kindle-alkalmazást a további információkért lásd: [Ismerkedés a Notification Hubs használata Kindle-alkalmazásokhoz](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;[Windows leküldéses értesítési szolgáltatások](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) Windows platformot célozza. Wns – Windows Phone 8.1 és újabb verziók is támogatja. További információkért lásd: [Ismerkedés a Notification Hubs a Windows univerzális platformon futó](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[A Microsoft leküldéses értesítéseket kezelő szolgáltatása](/previous-versions/windows/apps/ff402558(v=vs.105)). A platform támogatja a Windows Phone 8 és a korábbi Windows Phone-platformokat. További információkért lásd: [Sending leküldéses értesítéseket az Azure Notification Hubs Windows Phone a](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>function.json file example

Íme egy példa a Notification Hubs kötés egy *function.json* fájlt.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Kapcsolati karakterlánc beállítása

Egy értesítési központ kimeneti kötése használatához konfigurálnia kell a kapcsolati karakterláncra. Válassza ki egy meglévő értesítési központot, vagy hozzon létre egy új egy jobb a a *integráció* lap az Azure Portalon. A kapcsolati karakterlánc manuálisan is konfigurálhatja. 

A kapcsolati karakterláncot egy meglévő értesítési központ konfigurálása:

1. Keresse meg az az értesítési központ a [az Azure portal](https://portal.azure.com), válassza ki **hozzáférési házirendek**, és válassza ki a másolási gomb melletti a **DefaultFullSharedAccessSignature** házirend. Ezzel kimásolja a kapcsolati karakterláncát a *DefaultFullSharedAccessSignature* az értesítési központ szabályzatot. Ez a kapcsolati karakterlánc lehetővé teszi, hogy az értesítési üzeneteket küldjön a hubnak függvény.
    ![Az értesítési központ kapcsolati karakterlánc másolása](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Keresse meg a függvényalkalmazáshoz az Azure Portalon, válassza a **Alkalmazásbeállítások**, például adjon hozzá egy kulcsot **MyHubConnectionString**, illessze be a másolt *DefaultFullSharedAccessSignature* , az értéket, majd kattintson az értesítési központ **mentése**.

Az Alkalmazásbeállítás neve nem mi hová kerül a kimeneti kötés kapcsolat beállításban *function.json* vagy a .NET-attribútum. Tekintse meg a [konfigurációs szakasz](#configuration) a cikk elején.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Leírások |
|---|---|
| Notification Hub (Értesítési központ) | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

