---
title: A Xamarin.iOS-alkalmazást az Azure App Service leküldéses értesítések hozzáadása
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket a Xamarin.iOS-alkalmazást az Azure App Service használatával
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 32a8c36d223e2b0c12f5d82ec748af66ae841b01
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819022"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Leküldéses értesítések Xamarin.iOS-alkalmazás hozzáadása

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a leküldéses értesítések hozzáadása az [Xamarin.iOS gyors üzembe helyezési](app-service-mobile-xamarin-ios-get-started.md) projekthez, hogy a leküldéses értesítést küld az eszköz minden alkalommal, amikor a rendszer beszúr egy rekordot.

Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, szüksége lesz a leküldéses értesítési kiterjesztési csomag. Lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) további információt.

## <a name="prerequisites"></a>Előfeltételek

* Végezze el a [Xamarin.iOS rövid](app-service-mobile-xamarin-ios-get-started.md) oktatóanyag.
* Egy fizikai iOS-eszközön. Leküldéses értesítések iOS-szimulátorban történő nem támogatottak.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Regisztrálja az alkalmazást leküldéses értesítésekhez az Apple fejlesztői portálján

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Leküldéses értesítések küldéséhez a mobilalkalmazás konfigurálása

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Leküldéses értesítések küldéséhez a kiszolgálói projekt frissítése

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Xamarin.iOS-projekt konfigurálása

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz

1. A **QSTodoService**, adja hozzá a következő tulajdonságot úgy, hogy **AppDelegate** vásárolhatja meg a mobil ügyfelekből:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Adja hozzá a következő `using` utasítással felső részén a **AppDelegate.cs** fájlt.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. A **AppDelegate**, bírálja felül a **FinishedLaunching** esemény:

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. Ugyanebben a fájlban, bírálja felül a `RegisteredForRemoteNotifications` esemény. Ez a kód egy egyszerű sablon értesítést küld el minden támogatott platformon a kiszolgáló által regisztrálni.

    A Notification hubs használatával sablonokat a további információkért lásd: [sablonok](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Ezután felülbírálják a **DidReceivedRemoteNotification** esemény:

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Az alkalmazás most már frissül, és támogatja a leküldéses értesítéseket.

## <a name="test"></a>Teszt leküldéses értesítések az alkalmazásban

1. Nyomja le az **futtatása** gombot a projekt buildjének elkészítéséhez, és a kompatibilis iOS-eszközön indítsa el az alkalmazást, majd kattintson a **OK** leküldéses értesítések fogadásához.

   > [!NOTE]
   > Az alkalmazásból explicit módon el kell fogadnia a leküldéses értesítések. Ezt a kérelmet csak akkor történik meg, amely az alkalmazás első alkalommal.

2. Az alkalmazásban írjon be egy feladatot, és kattintson a plusz (**+**) ikonra.
3. Győződjön meg arról, hogy értesítés érkezik, majd kattintson a **OK** az értesítés elvetéséhez.
4. Ismételje meg a 2. lépés azonnal zárja be az alkalmazást, majd győződjön meg arról, hogy egy értesítés jelenik meg.

Ebben az oktatóanyagban sikeresen befejeződött.
