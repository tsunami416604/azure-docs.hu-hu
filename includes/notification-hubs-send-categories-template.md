---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ce8496596f25b85719b8a6dff849ebf0fc3e5dc3
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405601"
---
Ebben a szakaszban a legfrissebb híreket címkézett sablonértesítésekként fogja elküldeni egy .NET-konzolalkalmazásból. 

1. A Visual Studióban hozzon létre egy új Vizualizációt C# Konzolalkalmazás: egy. Válassza a menü **fájl** > **új** > **projekt**.
    b. Bontsa ki a **Visual C#** , és válassza ki **Windows asztali**. 
    c. Válassza ki **Console App (.NET Framework)** sablonok listáján. 
    d. Adjon meg egy **neve** az alkalmazáshoz. 
    e. Válassza ki a **mappa** az alkalmazáshoz.
    f. A projekt létrehozásához válassza az **OK** lehetőséget. 
2. A Visual Studio főmenüjében válassza **eszközök** > **NuGet-Csomagkezelő** > **Package Manager Console** , majd a konzolablakban Adja meg a következő karakterláncot:
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. Nyomja le az **Enter** billentyűt.  
    Ez a művelet hozzáad egy, az Azure Notification Hubs SDK-ra mutató hivatkozást a [Microsoft.Azure.Notification Hubs NuGet-csomag] használatával.

4. Nyissa meg a Program.cs fájlt, majd adja hozzá a következő `using` utasítást:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. A `Program` osztályban adja hozzá a következő metódust, vagy ha már létezik, cserélje le azt a következőre:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Ez a kód sablonértesítéseket küld a sztringtömb mind a hat címkéjének. A címkék használatával biztosítható, hogy az eszközök csak a regisztrált kategóriákhoz tartozó értesítéseket fogadják.

5. A `<hub name>` és a `<connection string with full access>` helyőrzőket cserélje le a fenti kódban az értesítési központ nevére és a *DefaultFullSharedAccessSignature* az értesítési központ irányítópultjáról származó kapcsolati sztringjére.

6. A **Main** metódusban adja hozzá a következő sorokat:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Hozza létre a konzolalkalmazást.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet-csomag]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
