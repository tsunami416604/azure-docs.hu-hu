---
title: Értesítési központok kötései az Azure Functionshez
description: Ismerje meg, hogyan használhatja az Azure Notification Hub-kötést az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 211f8c8a203b81a4df6a8e9515b403f99cec572a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277283"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Értesítési központok kimeneti kötése az Azure Functions-hez

Ez a cikk bemutatja, hogyan küldhet leküldéses értesítéseket az [Azure Notification Hubs-kötések](../notification-hubs/notification-hubs-push-notification-overview.md) használatával az Azure Functionsben. Az Azure Functions támogatja az értesítési központok kimeneti kötéseit.

Az Azure Értesítési központokat konfigurálni kell a használni kívánt Platform értesítési szolgáltatáshoz (PNS). Ha meg szeretné tudni, hogyan kaphat leküldéses értesítéseket az ügyfélalkalmazásban az Értesítési központokból, olvassa el az [Értesítési központok első lépései című témakört,](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) és válassza ki a célügyfél platformot a lap tetején található legördülő listából.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> A Google [elavulta a Google Cloud Messaging (GCM) javára Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). Ez a kimeneti kötés nem támogatja az FCM-et. Ha az FCM használatával szeretne értesítéseket küldeni, használja a [Firebase API-t](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) közvetlenül a függvényben, vagy használja a [sablonértesítéseket.](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)

## <a name="packages---functions-1x"></a>Csomagok - 1.x függvények

Az értesítési központok kötései a [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet csomag 1.x-es verziójában találhatók. A csomag forráskódja az [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok - 2.x vagy újabb funkciók

Ez a kötés nem érhető el a 2.x- es vagy újabb függvényekben.

## <a name="example---template"></a>Példa - sablon

Az elküldött értesítések lehetnek natív vagy [sablonértesítések.](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) A natív értesítések egy adott `platform` ügyfélplatformot céloznak meg a kimeneti kötés tulajdonságában konfigurálva. A sablonértesítés több platform megcélzására is használható.   

Lásd a nyelvspecifikus példát:

* [C# script - out paraméter](#c-script-template-example---out-parameter)
* [C# parancsfájl - aszinkron](#c-script-template-example---asynchronous)
* [C# parancsfájl - JSON](#c-script-template-example---json)
* [C# parancsfájl - könyvtártípusok](#c-script-template-example---library-types)
* [F #](#f-template-example)
* [Javascript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# script sablon példa - ki paraméter

Ez a példa értesítést küld egy `message` [sablonregisztrációról,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) amely helyőrzőt tartalmaz a sablonban.

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

### <a name="c-script-template-example---asynchronous"></a>C# script sablon példa - aszinkron

Ha aszinkron kódot használ, a kimenő paraméterek nem engedélyezettek. Ebben az `IAsyncCollector` esetben használja a sablon értesítését. A következő kód a fenti kód aszinkron példája. 

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

### <a name="c-script-template-example---json"></a>C# script sablon példa - JSON

Ez a példa értesítést küld egy `message` [sablonregisztrációról,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) amely egy helyőrzőt tartalmaz a sablonban egy érvényes JSON-karakterlánc használatával.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# parancsfájlsablon példa – könyvtártípusok

Ez a példa a [Microsoft Azure értesítési központok könyvtárában](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)definiált típusok használatát mutatja be. 

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

### <a name="f-template-example"></a>Példa F# sablonra

Ez a példa értesítést küld egy `location` `message` [sablon regisztrációjáról,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) amely tartalmazza a és a.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Példa JavaScript-sablonra

Ez a példa értesítést küld egy `location` `message` [sablon regisztrációjáról,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) amely tartalmazza a és a.

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

## <a name="example---apns-native"></a>Példa - APNS natív

Ez a C# parancsfájl példa bemutatja, hogyan kell küldeni egy natív APNS értesítést. 

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

## <a name="example---wns-native"></a>Példa - WNS natív

Ez a C# parancsfájl példa bemutatja, hogyan használhatja a [Microsoft Azure Értesítési központok könyvtárában](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) definiált típusoknatív WNS bejelentési értesítés küldéséhez. 

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

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) attribútumot.

Az attribútum konstruktorparamétereit és tulajdonságait a [konfigurációs](#configuration) szakasz ismerteti.

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `NotificationHub` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti:

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** |n/a| A beállításnak `notificationHub`a beállítására kell beállítható. |
|**direction** |n/a| A beállításnak `out`a beállítására kell beállítható. | 
|**név** |n/a| Az értesítési központ üzenetének függvénykódjában használt változónév. |
|**tagExpression kifejezés** |**TagExpression kifejezés** | A címkekifejezések lehetővé teszik annak megadását, hogy az értesítések olyan eszközökre érkezhessenek, amelyek regisztráltak, hogy a címkekifejezésnek megfelelő értesítéseket kapjanak.  További információt az [Útválasztás és címkekifejezések című témakörben talál.](../notification-hubs/notification-hubs-tags-segment-push-message.md) |
|**hubName** | **HubName (Központneve)** | Az értesítési központ erőforrás neve az Azure Portalon. |
|**Kapcsolat** | **ConnectionStringSetting (Kapcsolatkarakterlánc-beállítás)** | Az értesítési központok kapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve.  A kapcsolati karakterláncot az értesítési központ *DefaultFullSharedAccessSignature* értékére kell állítani. Lásd a [kapcsolati karakterlánc beállítását](#connection-string-setup) a cikk későbbi részében.|
|**Platform** | **Platform** | A platform tulajdonsága azt jelzi, hogy az ügyfélplatform az értesítési célokat. Alapértelmezés szerint, ha a platform tulajdonság kimaradt a kimeneti kötés, sablonértesítések segítségével az Azure Értesítési Központ konfigurált bármely platformra. A sablonok használatával kapcsolatban általában az Azure Értesítési központtal való platformközi értesítések küldéséről a Sablonok című témakörben [olvashat bővebben.](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) Ha be van állítva, a **platformnak** a következő értékek egyikének kell lennie: <ul><li><code>apns</code>&mdash;Apple leküldéses értesítési szolgáltatás. Az értesítési központ APNS-hez való konfigurálásáról és az értesítés ügyfélalkalmazásban való fogadásáról további információt a [Leküldéses értesítések küldése az iOS-nek az Azure Notification Hubs használatával](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)című témakörben talál.</li><li><code>adm</code>&mdash;[Amazon eszköz üzenetek](https://developer.amazon.com/device-messaging). Az Értesítési központ ADM-hez való konfigurálásáról és az értesítés Kindle-alkalmazásban való fogadásáról az [Első lépések a Kindle-alkalmazások értesítési központjaival](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)című témakörben talál további információt.</li><li><code>wns</code>&mdash;[Windows leküldéses értesítési szolgáltatások,](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) amelyek a Windows-platformokat célozzák meg. A WNS támogatja a Windows Phone 8.1-es és újabb verzióit is. További információt a [Windows Universal Platform Apps értesítési központok – első lépések című témakörben talál.](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)</li><li><code>mpns</code>&mdash;[Microsoft leküldéses értesítési szolgáltatás](/previous-versions/windows/apps/ff402558(v=vs.105)). Ez a platform támogatja a Windows Phone 8 és a korábbi Windows Phone platformokat. További információt a [Leküldéses értesítések küldése az Azure Notification Hubs használatával Windows Phone-telefonon című témakörben talál.](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>function.json fájl példa

Íme egy példa egy *function.json* fájlban lévő értesítési központok kötésére.

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

Az értesítési központ kimeneti kötésének használatához konfigurálnia kell a hub kapcsolati karakterláncát. Kiválaszthat egy meglévő értesítési központot, vagy létrehozhat egy újat az Azure Portal *Integráció lapjáról.* A kapcsolati karakterláncot manuálisan is beállíthatja. 

A kapcsolati karakterlánc konfigurálása egy meglévő értesítési központtal:

1. Keresse meg az értesítési központot az [Azure Portalon,](https://portal.azure.com)válassza az **Access-szabályzatok**lehetőséget, és válassza a Copy gombot a **DefaultFullSharedAccessSignature** házirend mellett. Ezzel átmásolja a *DefaultFullSharedAccessSignature* házirend kapcsolati karakterláncát az értesítési központba. Ez a kapcsolati karakterlánc lehetővé teszi, hogy a függvény értesítési üzeneteket küldjön a központnak.
    ![Az értesítési központ kapcsolati karakterláncának másolása](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Keresse meg a függvényalkalmazást az Azure Portalon, válassza az **Alkalmazásbeállítások**lehetőséget, adjon hozzá egy kulcsot, például **a MyHubConnectionString karakterláncot,** illessze be a másolt *DefaultFullSharedAccessSignature-et* az értesítési központhoz értékként, majd kattintson a **Mentés gombra.**

Ennek az alkalmazásbeállításnak a neve az, ami a *function.json* vagy a .NET attribútum kimeneti kötési kapcsolati beállításában szerepel. Lásd a [konfiguráció szakasz](#configuration) korábbi ebben a cikkben.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Kivételek és visszaküldési kódok

| Kötés | Referencia |
|---|---|
| Értesítési központ | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)

