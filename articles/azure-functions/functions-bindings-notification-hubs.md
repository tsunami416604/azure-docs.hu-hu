---
title: "Az Azure Functions kötései értesítési központok"
description: "Azure Notification Hub-kötés az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 6be75035247f05995949734cd4f4f0d934e30685
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>A Notification Hubs kimeneti kötése az Azure Functions

Ez a cikk azt ismerteti, hogyan küldhetők leküldéses értesítések segítségével [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) Azure Functions kötések. Az Azure funkciók által támogatott kimeneti kötései Notification hubs használatával.

Az Azure Notification Hubs az a Platform értesítések szolgáltatás (PNS) használni kívánt be kell állítani. Az ügyfél alkalmazásban leküldéses értesítések a Notification Hubs beszerezni, lásd: [Ismerkedés a Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) és a lap tetején a legördülő listából a célként megadott ügyfélplatformot válasszon ki.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example---template"></a>Példa - sablon

Az értesítéseket küld lehet natív értesítések vagy [sablon értesítések](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Natív értesítések célként egy meghatározott ügyfélplatformot be a `platform` tulajdonság a kimeneti kötése. Sablon értesítést, amelyekre több platformon is használható.   

Tekintse meg a nyelvspecifikus példát:

* [C# parancsfájl - ki paraméter](#c-script-template-example---out-parameter)
* [C# parancsfájl - aszinkron](#c-script-template-example---asynchronous)
* [C# parancsfájl - JSON](#c-script-template-example---json)
* [C# parancsfájl - dokumentumtár-típus](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# sablon mintaparancsfájl - ki paraméter

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

### <a name="c-script-template-example---asynchronous"></a>C# sablon mintaparancsfájl - aszinkron

Aszinkron kódot használja, ha kimenő paraméterek nem engedélyezettek. Ebben az esetben használjon `IAsyncCollector` a sablon értesítési vissza. A következő kódot a fenti kódot aszinkron példája. 

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

### <a name="c-script-template-example---json"></a>C# sablon mintaparancsfájl - JSON

Ebben a példában értesítést küld egy [sablon regisztrációs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , amely tartalmazza a `message` helyőrző a sablonban érvényes JSON karakterláncnak használatával.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# sablon mintaparancsfájl - dokumentumtár-típus

A példa bemutatja, hogyan használható a megadott típus beolvasása a [Microsoft Azure Notification Hubs könyvtár](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

### <a name="f-template-example"></a>F # sablon – példa

Ebben a példában értesítést küld egy [sablon regisztrációs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) tartalmazó `location` és `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>JavaScript sablon – példa

Ebben a példában értesítést küld egy [sablon regisztrációs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) tartalmazó `location` és `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
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

A C# parancsfájl példa bemutatja, hogyan natív APNS értesítés küldése. 

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

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---gcm-native"></a>Natív GCM - – példa

A C# parancsfájl példa bemutatja, hogyan natív GCM értesítés küldése. 

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
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Példa – natív WNS

A C# parancsfájl példa bemutatja, hogyan használható a megadott típus beolvasása a [Microsoft Azure Notification Hubs Library](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) natív WNS bejelentési értesítés küldése. 

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

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) attribútumot, amelyet a NuGet-csomag [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs).

Az attribútum konstruktorparaméterek és a tulajdonságok a [konfigurációs](#configuration) szakasz.

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `NotificationHub` attribútum:

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** |n/a| "NotificationHub" értékre kell állítani. |
|**direction** |n/a| "Ki" értékre kell állítani. | 
|**name** |n/a| Az értesítési központ üzenet függvény kódban használt változó neve. |
|**tagExpression** |**TagExpression** | Címke kifejezések adja meg, hogy az eszközök, amelyek megfelelnek a címke kifejezésnek értesítések fogadására regisztrált értesítések kézbesítendő teszik lehetővé.  További információkért lásd: [Útválasztás és címke kifejezések](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Az Azure-portálon az értesítési központ erőforrás neve. |
|**connection** | **ConnectionStringSetting** | A Notification Hubs kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve.  A kapcsolati karakterláncot kell beállítani a *DefaultFullSharedAccessSignature* értéke az értesítési központban. Lásd: [kapcsolati karakterlánc beállítása](#connection-string-setup) című cikkben.|
|**platform** | **Platform** | A platform tulajdonság jelöli a ügyfélplatform az értesítési célokat. Alapértelmezés szerint a platform tulajdonság nem szerepel a kimeneti kötés, ha sablon értesítések segítségével bármely célplatform az Azure értesítési központ konfigurálva. A közötti az Azure Notification Hub platform értesítések küldése általában sablonokkal további információkért lásd: [sablonok](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Ha a beállítás, **platform** a következő értékek egyike lehet: <ul><li><code>apns</code>&mdash;Apple Push Notification szolgáltatás. Az értesítési központ konfigurálása az APN szolgáltatás és az értesítés fogadásának egy ügyfél alkalmazásban további információkért lásd: [küldő leküldéses értesítések küldéséhez iOS az Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Az értesítési központ konfigurálása az ADM és az értesítés fogadásának Kindle-alkalmazást a további információkért lásd: [Ismerkedés a Notification Hubs szolgáltatással Kindle-alkalmazásokhoz](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[A Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, amely GCM új verziója, is támogatott. További információkért lásd: [küldő leküldéses értesítések androidra az Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[A Windows leküldéses értesítéseket kezelő szolgáltatása](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) célzó Windows platformra. Windows Phone 8.1 és újabb verziók WNS is támogatja. További információkért lásd: [Ismerkedés a Notification Hubs Windows Universal Platform alkalmazásokkal való](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[A Microsoft leküldéses értesítéseket kezelő szolgáltatása](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Ez a platform támogatja a Windows Phone 8 és a korábbi Windows Phone-platformokat. További információkért lásd: [küldő leküldéses értesítések az Azure Notification Hubs – Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Function.JSON fájl például

A Notification Hubs kötés a példa egy *function.json* fájlt.

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
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Kapcsolati karakterlánc beállítása

A notification hub kimeneti kötés használatához konfigurálnia kell a kapcsolati karakterláncot a központ. Válasszon egy meglévő értesítési központot, vagy hozzon létre egy új egy közvetlenül a a *integráció* fülre az Azure portálon. A kapcsolati karakterlánc manuálisan is konfigurálhatja. 

A kapcsolati karakterláncot egy meglévő értesítési központ konfigurálása:

1. Keresse meg az értesítési központ a a [Azure-portálon](https://portal.azure.com), válassza a **hozzáférési házirendek**, és jelölje be a Másolás gombra a **DefaultFullSharedAccessSignature** házirend. Ez a kapcsolati karakterláncot másolja át a *DefaultFullSharedAccessSignature* házirend számára, az értesítési központban. Ez a kapcsolati karakterlánc lehetővé teszi, hogy a függvény szeretne az elosztóhoz az értesítések küldéséhez.
    ![Másolja az értesítési központ kapcsolati karakterláncot](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Keresse meg a függvény alkalmazást az Azure portálon, válassza a **Alkalmazásbeállítások**, például a kulcs hozzáadása **MyHubConnectionString**, illessze be a másolt *DefaultFullSharedAccessSignature* lehetőséget a értéket, majd kattintson az értesítési központ **mentése**.

Az Alkalmazásbeállítás neve nem a kimeneti kötése kapcsolat beállítására tartalmának *function.json* vagy a .NET-attribútum. Tekintse meg a [konfigurációs szakasz](#configuration) korábbi ebben a cikkben.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Kivételeket és a visszatérési kódok

| Kötelező | Leírások |
|---|---|
| Notification Hub (Értesítési központ) | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

