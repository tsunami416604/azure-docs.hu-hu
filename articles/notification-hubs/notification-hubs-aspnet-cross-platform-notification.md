---
title: Platformfüggetlen értesítések küldése a felhasználóknak az Azure Értesítési központok segítségével (ASP.NET)
description: Ismerje meg, hogyan használhatja az Értesítési központok sablonjait egyetlen kérelemben egy platformfüggetlen értesítés küldésére, amely az összes platformot célozza meg.
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
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127052"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Platformfüggetlen értesítések küldése az Azure Értesítési központokkal

Ez az oktatóanyag az előző oktatóanyagra, az [Értesítések küldése adott felhasználóknak az Azure Értesítési központok használatával]épül. Ez az oktatóanyag leírja, hogyan lehet értesítéseket leadni egy adott hitelesített felhasználóhoz regisztrált összes eszközre. Ez a megközelítés több kérelmet igényelt, hogy értesítést küldjön az egyes támogatott ügyfélplatformoknak. Az Azure Notification Hubs támogatja a sablonokat, amelyekkel megadhatja, hogy egy adott eszköz hogyan szeretne értesítéseket kapni. Ez a módszer leegyszerűsíti a platformok közötti értesítések küldését.

Ez a cikk bemutatja, hogyan használhatja ki a sablonok at, hogy küldjön egy értesítést, amely az összes platformot célozza. Ez a cikk egyetlen kérelmet használ egy platformsemleges értesítés küldésére. A sablonokról az [Értesítési központok – áttekintés című témakörben olvashat bővebben.][Templates]

> [!IMPORTANT]
> A Visual Studio 2019 nem támogatja a 8.1-es és korábbi Windows Phone-projekteket. További információ: [Visual Studio 2019 Platform Célzás and Compatibility](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Az értesítési központok segítségével az eszközök több sablont is regisztrálhatnak ugyanazzal a címkével. Ebben az esetben egy bejövő üzenet, amely a címkét célozza, azt eredményezi, hogy több értesítés érkezik az eszközre, mindegyik sablonhoz. Ez a folyamat lehetővé teszi, hogy ugyanazt az üzenetet több vizuális értesítésben is megjelenítse, például jelvényként és bejelentési értesítésként egy Windows Áruházbeli alkalmazásban.

## <a name="send-cross-platform-notifications-using-templates"></a>Platformfüggetlen értesítések küldése sablonok használatával

Ez a szakasz az Értesítések [küldése adott felhasználóknak] az Azure Notification Hubs oktatóanyag használatával beépített mintakódot használja. A mintaalkalmazást a [GitHubról](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers) töltheti le.

Ha sablonok használatával szeretne platformfüggetlen értesítéseket küldeni, tegye a következőket:

1. A Visual Studio in **Solution Explorer**alkalmazásban bontsa ki a **Vezérlők mappát,** majd nyissa meg a *RegisterController.cs* fájlt.

1. Keresse meg a kódblokkot az `Put` új regisztrációt létrehozó `switch` metódusban, majd cserélje le a tartalmat a következő kódra:

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

    Ez a kód meghívja a platformspecifikus metódust, hogy hozzon létre egy sablon regisztráció helyett egy natív regisztráció. Mivel a sablonregisztrációk natív regisztrációkból származnak, nem kell módosítania a meglévő regisztrációkat.

1. A **Megoldáskezelőben**a **Vezérlők** mappában nyissa meg a *NotificationsController.cs* fájlt. Cserélje le az `Post` metódust az alábbi kódra:

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

    Ez a kód értesítést küld az összes platformnak egyszerre. Nem adhat meg natív hasznos terhet. Az értesítési központok a regisztrált sablonokban megadott címkével rendelkező minden eszközre létrehozza és biztosítja a megfelelő hasznos adatot.

1. Tegye közzé újra a webes API-projektet.

1. Futtassa újra az ügyfélalkalmazást, és ellenőrizze, hogy a regisztráció sikeres volt-e.

1. Szükség esetén telepítse az ügyfélalkalmazást egy második eszközre, majd futtassa az alkalmazást. Minden eszközön megjelenik egy értesítés.

## <a name="next-steps"></a>További lépések

Most, hogy befejezte ezt az oktatóanyagot, az alábbi cikkekben többet is megtudhatsz az értesítési központokról és sablonokról:

* A sablonok használatának eltérő forgatókönyvét az [Univerzális Windows Platform-alkalmazásokat futtató adott Windows-eszközökre vonatkozó leküldéses értesítések][Use Notification Hubs to send breaking news] oktatóanyaga ismerteti.
* A sablonokról az [Értesítési központok – áttekintés című témakörben olvashat bővebben.][Templates]

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Értesítések küldése adott felhasználóknak az Azure Értesítési központok használatával]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
