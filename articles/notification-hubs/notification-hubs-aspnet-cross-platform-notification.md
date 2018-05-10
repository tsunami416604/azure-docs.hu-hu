---
title: Platformok közötti értesítések küldése a felhasználóknak az Azure Notification Hubs (ASP.NET)
description: Megtudhatja, hogyan egyetlen kérelem, platformfüggetlen megcélzó platform-független értesítést küldeni a Notification Hubs-sablonok használatával.
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
ms.openlocfilehash: 95793aac3c25563e3af39f3c47cebdd06e25e35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Platformok közötti értesítések küldése a felhasználóknak a Notification hubs használatával
Egy korábbi oktatóanyagban [értesítse a felhasználókat a Notification hubs használatával], megtudta, hogyan leküldéses értesítések az összes, egy adott hitelesített felhasználó számára regisztrált eszközre. Hogy az oktatóanyag több kérés kellett minden támogatott ügyfélplatform értesítést küldeni. Az Azure Notification Hubs támogatja a sablonok, amellyel megadhatja, hogyan egy adott eszköz azt szeretné, ha értesítést szeretne kapni. Ez a módszer egyszerűbbé teszi a platformok közötti értesítések küldése. 

Ez a cikk bemutatja, hogyan egyetlen kérelem, platformfüggetlen megcélzó platform-független értesítést küldhet sablonok előnyeit. Részletesebb információ a sablonok, lásd: [Azure Notification Hubs – áttekintés][Templates].

> [!IMPORTANT]
> Windows Phone-projektek 8.1 és korábbi verziók nem támogatottak a Visual Studio 2017. További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> A Notification hubs használatával eszköz regisztrálhatja a azonos címkével több sablont. Ebben az esetben, amelynek célpontja a címke eredmények több értesítés is érkezett a bejövő üzenet juttatni az eszközre, minden sablon egy. Ez a folyamat lehetővé teszi, hogy több vizuális értesítések, például egy jelvény és egy Windows Áruházbeli alkalmazásban egy bejelentési értesítést is ugyanaz az üzenet megjelenítését.
> 
> 

Platformok közötti értesítések küldéséhez sablonokkal, tegye a következőket:

1. A Visual Studio Solution Explorerben bontsa ki a **tartományvezérlők** mappa, és nyissa meg a RegisterController.cs fájlt.

2. Keresse meg a kódblokkot az a **Put** metódus, amely létrehoz egy új regisztrációs, akkor cserélje le a `switch` tartalom a következő kóddal:
   
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
   
    Ez a kód meghívja a platform-specifikus metódust egy sablon regisztrációs helyett egy natív regisztrációjának létrehozásához. Sablon regisztrációk natív regisztrációk származik, mert nem kell módosítani a meglévő regisztrációk.

3. Az a **értesítések** hozzáadására, és cserélje le a **sendNotification** metódus a következő kóddal:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Ezt a kódot értesítést küld az összes platformra egyszerre, nélkül lépett fel a natív hasznos adatok között adja meg. Notification Hubs alapszik, és továbbítja a megfelelő tartalom minden eszköz a megadott *címke* érték, a regisztrált sablonok megadott.

4. A WebApi háttérrendszerből projekt közzé.

5. Futtassa újra az ügyfél alkalmazást, és ellenőrizze, hogy sikerült-e a regisztrációt.

6. (Választható) Az ügyfélalkalmazás telepítése egy másik eszközön, és futtassa az alkalmazást.
    Minden egyes eszközön megjelenik egy értesítés.

## <a name="next-steps"></a>További lépések
Most, hogy ez az oktatóanyag befejezése többet szeretne tudni a Notification Hubs és a sablonok a következő témakörökben talál:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Azure Notification Hubs – áttekintés][Templates]: sablonok részletes információkat tartalmaz.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[értesítse a felhasználókat a Notification hubs használatával]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
