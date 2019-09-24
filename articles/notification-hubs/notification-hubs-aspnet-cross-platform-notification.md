---
title: Platformfüggetlen értesítések küldése a felhasználóknak az Azure Notification Hubs (ASP.NET) használatával
description: Ismerje meg, hogyan küldhet Notification Hubs sablonokat egyetlen kérelemben, amely az összes platformra kiterjedő, a platformtól független értesítést küld.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: cea0d63c20af781fcfc6ba5d7c06061b12992702
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212025"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Platformfüggetlen értesítések küldése a felhasználóknak a Notification Hubs

Egy korábbi oktatóanyagban [Felhasználók értesítése Notification Hubs], megtudta, hogyan küldhet le értesítéseket minden olyan eszközre, amely regisztrálva van egy adott hitelesített felhasználó számára. Ebben az oktatóanyagban több kérelemre volt szükség ahhoz, hogy értesítést küldjön az egyes támogatott ügyféloldali platformokhoz. Az Azure Notification Hubs támogatja a sablonokat, amellyel meghatározhatja, hogy egy adott eszköz hogyan kapjon értesítéseket. Ez a módszer leegyszerűsíti a platformok közötti értesítések küldését.

Ez a cikk bemutatja, hogyan veheti igénybe a sablonok előnyeit egyetlen kérelemben, amely az összes platformra kiterjedő, a platformtól független értesítés. A sablonokkal kapcsolatos részletesebb információkért lásd: az [Azure Notification Hubs áttekintése][Templates].

> [!IMPORTANT]
> Windows Phone-telefon a 8,1-es és korábbi projektek nem támogatottak a Visual Studio 2017-ben. További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> A Notification Hubs segítségével egy eszköz több sablont is regisztrálhat ugyanazzal a címkével. Ebben az esetben egy olyan bejövő üzenet, amely a címkét célozza, több értesítés érkezik az eszközre, egyet pedig az egyes sablonokhoz. Ez a folyamat lehetővé teszi, hogy ugyanazt az üzenetet több vizualizációs értesítésben jelenítse meg, például jelvényként, valamint egy Windows áruházbeli alkalmazásban bejelentési értesítésként.

## <a name="send-cross-platform-notifications-using-templates"></a>Platformfüggetlen értesítések küldése sablonok használatával

Ha platformfüggetlen értesítéseket szeretne küldeni a sablonok használatával, tegye a következőket:

1. A Visual Studióban Megoldáskezelő bontsa ki a **vezérlők** mappát, majd nyissa meg a RegisterController.cs fájlt.

2. Keresse meg a kód blokkját a `Put` metódusban, amely új regisztrációt hoz létre, majd `switch` cserélje le a tartalmat a következő kódra:

    ```csharp
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
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Ez a kód meghívja a platform-specifikus metódust, hogy natív regisztráció helyett sablon-regisztrációt hozzon létre. Mivel a sablon regisztrációja natív regisztrációból származik, nem kell módosítania a meglévő regisztrációkat.

3. A vezérlőben cserélje le a `sendNotification` metódust a következő kódra: `Notifications`

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Ez a kód egyszerre küld értesítést az összes platformra, anélkül, hogy natív adattartalmat kellene megadnia. A Notification Hubs a megadott *címke* értékkel rendelkező összes eszközre kiépíti és biztosítja a megfelelő adattartalmat a regisztrált sablonokban meghatározottak szerint.

4. Tegye közzé újra a WebApi projektjét.

5. Futtassa ismét az ügyfélalkalmazás alkalmazást, és ellenőrizze, hogy a regisztráció sikeres volt-e.

6. Választható Telepítse az ügyfélalkalmazás egy második eszközre, majd futtassa az alkalmazást.
    Egy értesítés jelenik meg minden eszközön.

## <a name="next-steps"></a>További lépések

Most, hogy elvégezte az oktatóanyagot, a következő témakörökben talál további információt a Notification Hubs és a sablonokról:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* Az [Azure Notification Hubs áttekintése][Templates]: Részletesebb információkat tartalmaz a sablonokról.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Felhasználók értesítése Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
