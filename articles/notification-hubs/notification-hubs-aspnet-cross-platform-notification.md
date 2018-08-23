---
title: Platformfüggetlen értesítések küldése a felhasználóknak az Azure Notification Hubs (ASP.NET)
description: Ismerje meg, hogyan egy egyetlen kérés esetén, amely minden platformon célozza platform-agnosztikus értesítést küldeni, a Notification Hubs-sablonok használatával.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c9d1874fb611b349403736593fdc9eccc45d2d4d
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42056666"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Platformfüggetlen értesítések küldése a felhasználóknak a Notification hubs használatával
Egy korábbi oktatóanyagban [értesítse a felhasználókat a Notification hubs használatával], útmutatóból megtudhatta, hogyan küldhet leküldéses értesítéseket, amelyek egy adott hitelesített felhasználó összes eszközre. Az oktatóanyag több kérés kellett egy értesítést küld minden támogatott ügyfélplatform. Az Azure Notification Hubs támogatja a sablonok, amellyel megadhatja, milyen egy adott eszközhöz szeretne értesítéseket kapni. Ez a módszer egyszerűbbé teszi a platformfüggetlen értesítések küldése. 

Ez a cikk bemutatja, hogyan szeretne küldeni, egy egyetlen kérés esetén a platform-agnosztikus értesítést, amely minden platformon célozza sablonok előnyeit. További részletes információ a sablonok,: [Azure Notification Hubs – áttekintés][Templates].

> [!IMPORTANT]
> Windows Phone-projektek, 8.1 és korábbi verziók nem támogatottak a Visual Studio 2017-ben. További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> A Notification Hubs egy eszköz több sablon az azonos címkéjű lehet regisztrálni. Ebben az esetben egy bejövő üzenet, amely több értesítés is érkezett a címke eredményeket célozza kell juttatni az eszközre, az egyes sablonok egyikét. Ez a folyamat lehetővé teszi, hogy ugyanazon üzenet megjelenítése több visual értesítéseket, például egy jelvény, és a egy bejelentési értesítés, a Windows Store App is.
> 
> 

Platformfüggetlen értesítések küldése a sablonok használatával, tegye a következőket:

1. A Visual Studióban a Solution Explorerben bontsa ki a **tartományvezérlők** mappát, és nyissa meg a registercontroller.cs fájlhoz fájlt.

2. Keresse meg a kódblokkot az a **Put** metódushoz, amely létrehoz egy új regisztrációt, és cserélje a `switch` tartalom a következő kóddal:
   
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    Ez a kód meghívja az egyes platformokra vonatkozó metódussal hoz létre egy sablon regisztrációt egy natív regisztrációjának helyett. Sablonregisztrációk natív regisztrációinak származhat, mert nem kell módosítani a meglévő regisztrációk.

3. Az a **értesítések** vezérlő, cserélje le a **sendNotification** módszer a következő kóddal:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Ez a kód egy értesítést küld minden platform egyszerre, merülnek fel a natív hasznos adat megadása nélkül. A Notification Hubs hozza létre, és a megfelelő tartalom biztosít minden, a megadott eszközre *címke* érték, a regisztrált sablonok megadott módon.

4. Tegye közzé újra a WebApi háttérrendszer projekt.

5. Futtassa ismét az ügyfél-alkalmazást, és ellenőrizze, hogy a regisztráció sikeres volt-e.

6. (Nem kötelező) Az ügyfél-alkalmazás üzembe helyezése egy másik eszközön, és futtassa az alkalmazást.
    Minden eszközön megjelenik egy értesítés.

## <a name="next-steps"></a>További lépések
Most, hogy ebben az oktatóanyagban elvégezte, tudjon meg többet a Notification Hubs és a sablonokat az alábbi témakörök:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Az Azure Notification Hubs – áttekintés][Templates]: a sablonok részletesebb információkat tartalmaz.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Értesítse a felhasználókat a Notification hubs használatával]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
