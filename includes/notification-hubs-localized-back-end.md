---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/02/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 527567ee3f3a939c7358fb6a62271cbe38e16974
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42915448"
---
A sablon értesítést küld, amikor, csak meg kell adnia a tulajdonságai készletét. Ebben a forgatókönyvben a tulajdonságai készletét tartalmazza az aktuális hírek honosított változatát.

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

### <a name="send-notifications-using-a-c-console-app"></a>Egy C# Konzolalkalmazás használatával értesítések küldése

Ez a szakasz bemutatja, hogyan küldhetők értesítések egy konzolalkalmazás használatával. A kódot közzéteszi az értesítések Windows Store és az IOS-es eszközökre történő küldéséhez. Módosítsa a `SendTemplateNotificationAsync` metódust a korábban létrehozott konzolalkalmazásban az alábbi kóddal:

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

A SendTemplateNotificationAsync módszert biztosít a hírek honosított részét **összes** az eszközök, függetlenül a platform. Az értesítési központban épít, és a megfelelő natív adattartalom biztosít az adott címkére feliratkozott összes eszközre.

### <a name="sending-notification-with-mobile-services"></a>A Mobile Services értesítést küld

A mobilszolgáltatások scheduler használja az alábbi parancsfájlt:

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```
