---
title: Azure Functions-kötések Notification Hubs
description: Ismerje meg, hogyan használhatja az Azure Notification hub-kötést Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 8bc7f879a2c2e8b1e0e2d82216241704a466ad60
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231136"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Azure Functions Notification Hubs kimeneti kötése

Ez a cikk azt ismerteti, hogyan küldhet leküldéses értesítéseket az [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) -kötések használatával Azure Functionsban. Azure Functions támogatja a Notification Hubs kimeneti kötéseit.

Az Azure Notification Hubs-t konfigurálni kell a használni kívánt platform Notifications szolgáltatáshoz (PNS). Ha meg szeretné tudni, hogyan kérhet le leküldéses értesítéseket az ügyfélalkalmazás Notification Hubsről, tekintse meg a [Notification Hubs első lépéseivel foglalkozó](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) témakört, és válassza ki a cél ügyféloldali platformot a lap tetején található legördülő listából.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> A Google [elavult Google Cloud Messaging (GCM) a Firebase Cloud Messaging (FCM) javára](https://developers.google.com/cloud-messaging/faq). Ez a kimeneti kötés nem támogatja az FCM-et. Ha az FCM használatával szeretne értesítéseket küldeni, használja a [Firebase API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) -t közvetlenül a függvényében, vagy használja a [sablonra vonatkozó értesítéseket](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Notification Hubs kötések a [Microsoft. Azure. webjobs. Extensions. NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet csomagban találhatók, 1. x verzióban. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

Ez a kötés nem érhető el a 2. x függvényben.

## <a name="example---template"></a>Példa – sablon

Az Ön által küldött értesítések lehetnek natív értesítések vagy [sablon-értesítések](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). A natív értesítések a kimeneti kötés `platform` tulajdonságában megadott ügyféloldali platformot céloznak meg. A sablonokkal kapcsolatos értesítések több platform megcélzására is használhatók.   

Tekintse meg az adott nyelvű példa:

* [C#parancsfájl-kilépési paraméter](#c-script-template-example---out-parameter)
* [C#parancsfájl – aszinkron](#c-script-template-example---asynchronous)
* [C#parancsfájl – JSON](#c-script-template-example---json)
* [C#parancsfájl-függvénytárak típusai](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C#parancsfájl-sablon – példa – kimenő paraméter

Ez a példa egy olyan sablon- [regisztrációra](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) vonatkozó értesítést küld, amely `message` helyőrzőt tartalmaz a sablonban.

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

### <a name="c-script-template-example---asynchronous"></a>C#Példa parancsfájl-sablonra – aszinkron

Aszinkron kód használata esetén a kimenő paraméterek nem engedélyezettek. Ebben az esetben a `IAsyncCollector` használatával adja vissza a sablon értesítését. A következő kód egy aszinkron példa a fenti kódra. 

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

### <a name="c-script-template-example---json"></a>C#Példa a parancsfájl sablonra – JSON

Ez a példa egy olyan sablon- [regisztrációra](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) vonatkozó értesítést küld, amely egy érvényes JSON-karakterlánc használatával `message` helyőrzőt tartalmaz a sablonban.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#parancsfájl-sablon – példa – típustár-típusok

Ez a példa bemutatja, hogyan használhatók a [Microsoft Azure Notification Hubs könyvtárban](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)definiált típusok. 

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

### <a name="f-template-example"></a>F#Példa sablonra

Ez a példa egy `location` és `message`tartalmazó [sablon-regisztrációra](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) vonatkozó értesítést küld.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Példa JavaScript-sablonra

Ez a példa egy `location` és `message`tartalmazó [sablon-regisztrációra](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) vonatkozó értesítést küld.

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

Ez C# a szkript bemutatja, hogyan küldhet egy natív APNS-értesítést. 

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

Ez C# a szkript bemutatja, hogyan használhatók a [Microsoft Azure Notification Hubs könyvtárban](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) definiált típusok egy natív WNS-bejelentési értesítés küldéséhez. 

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

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) attribútumot.

Az attribútum konstruktorának paraméterei és tulajdonságai a [konfiguráció](#configuration) szakaszban olvashatók.

## <a name="configuration"></a>Konfiguráció

A következő táblázat ismerteti a *function. JSON* fájlban és a `NotificationHub` attribútumban beállított kötési konfigurációs tulajdonságokat:

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** |n/a| `notificationHub`értékre kell állítani. |
|**direction** |n/a| `out`értékre kell állítani. | 
|**név** |n/a| Az értesítési központ üzenetének függvény kódjában használt változó neve. |
|**tagExpression** |**TagExpression** | A címkézési kifejezések lehetővé teszik annak megadását, hogy a rendszer az értesítéseket a címkével megegyező értesítések fogadására regisztrált eszközök egy csoportjának adja meg.  További információ: [útválasztási és címkézési kifejezések](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Az értesítési központ erőforrásának neve a Azure Portalban. |
|**kapcsolat** | **ConnectionStringSetting** | Egy Notification Hubs-kapcsolatok sztringjét tartalmazó Alkalmazásbeállítás neve.  A kapcsolódási karakterláncot az értesítési központ *DefaultFullSharedAccessSignature* értékére kell beállítani. A jelen cikk későbbi, a [kapcsolatok karakterláncának beállítása](#connection-string-setup) című szakaszban talál.|
|**platform** | **Platform** | A platform tulajdonság az értesítési célokhoz tartozó ügyféloldali platformot jelzi. Alapértelmezés szerint, ha a platform tulajdonságot kihagyja a kimeneti kötésből, a sablonok értesítései az Azure Notification hub-ban konfigurált platformok célzására használhatók. Ha többet szeretne megtudni arról, hogyan használhatók a sablonok a platformfüggetlen értesítések Azure Notification hub használatával történő küldéséhez, tekintse meg a [sablonok](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)című témakört. Ha be van állítva, a **platformnak** a következő értékek egyikének kell lennie: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. A APNS értesítési központjának konfigurálásával és az értesítés egy ügyfélalkalmazásba való fogadásával kapcsolatos további információkért lásd: [leküldéses értesítések küldése iOS](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)-re az Azure Notification Hubs.</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Az ADM-hez készült értesítési központ konfigurálásával és az értesítés egy Kindle-alkalmazásban való fogadásával kapcsolatos további információkért lásd: [Első lépések Notification Hubs for Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)-alkalmazásokhoz.</li><li><code>wns</code>&mdash;[Windows-Leküldéses Notification Services](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) a Windows platformokat célozza meg. A WNS a Windows Phone-telefon 8,1-es és újabb verzióját is támogatja. További információ: [Bevezetés a Notification Hubs használatába a Windows Universal platform alkalmazásaihoz](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;a [Microsoft leküldéses értesítési szolgáltatását](/previous-versions/windows/apps/ff402558(v=vs.105)). Ez a platform a Windows Phone-telefon 8 és korábbi Windows Phone-telefon platformokat támogatja. További információ: [leküldéses értesítések küldése az Azure Notification Hubs on Windows Phone-telefon](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Példa a function. JSON fájlra

Íme egy példa egy Notification Hubs kötésre egy *function. JSON* fájlban.

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

### <a name="connection-string-setup"></a>A kapcsolatok karakterláncának beállítása

Az értesítési központ kimeneti kötésének használatához konfigurálnia kell a hub kapcsolati karakterláncát. Kiválaszthat egy meglévő értesítési központot, vagy létrehozhat egy újat közvetlenül a Azure Portal *integrálás* lapján. A kapcsolatok karakterláncát manuálisan is konfigurálhatja. 

A kapcsolódási karakterlánc konfigurálása meglévő értesítési központhoz:

1. Nyissa meg az értesítési központot a [Azure Portalban](https://portal.azure.com), válassza a **hozzáférési szabályzatok**lehetőséget, és kattintson a **DefaultFullSharedAccessSignature** szabályzat melletti Másolás gombra. Ezzel átmásolja a *DefaultFullSharedAccessSignature* szabályzathoz tartozó kapcsolódási karakterláncot az értesítési központba. Ez a kapcsolódási karakterlánc lehetővé teszi, hogy a függvény értesítési üzeneteket küldjön a hubhoz.
    ![az értesítési központ kapcsolatok karakterláncának másolása](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Keresse meg a Function alkalmazást a Azure Portalban, válassza az **Alkalmazásbeállítások**lehetőséget, adjon hozzá egy kulcsot, például a **MyHubConnectionString**, illessze be az értesítési központ átmásolt *DefaultFullSharedAccessSignature* az értékként, majd kattintson a **Mentés**gombra.

Ennek az alkalmazásnak a neve a *function. JSON* vagy a .NET attribútum kimeneti kötési kapcsolatának beállítása. Tekintse meg a jelen cikk korábbi, [konfigurációs szakaszát](#configuration) .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Referencia |
|---|---|
| Notification Hub (Értesítési központ) | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

