---
title: "Leküldéses értesítések hozzáadása a Xamarin.iOS-alkalmazás az Azure App Service"
description: "Azure App Service használata leküldéses értesítések küldésére a Xamarin.iOS-alkalmazás"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: glenga
ms.openlocfilehash: bf922e49c4c92d0065817a5dd6c7d10a04737304
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Leküldéses értesítések Xamarin.iOS-alkalmazás hozzáadása
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban leküldéses értesítések hozzáadása a [Xamarin.iOS gyors üzembe helyezési](app-service-mobile-xamarin-ios-get-started.md) projektre, hogy egy leküldéses értesítést küld az eszköz minden alkalommal, amikor egy olyan rekordot csatlakoztatva van.

Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, szüksége lesz a leküldéses értesítési kiterjesztési csomagot. Lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) további információt.

## <a name="prerequisites"></a>Előfeltételek
* Fejezze be a [Xamarin.iOS gyors üzembe helyezés](app-service-mobile-xamarin-ios-get-started.md) oktatóanyag.
* Egy fizikai iOS-eszközön. Leküldéses értesítések nem támogatottak az iOS-szimulátorban.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Az alkalmazás az Apple fejlesztői portálján a leküldéses értesítések regisztrálása
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Leküldéses értesítések küldéséhez a mobilalkalmazás konfigurálása
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Frissítés a kiszolgáló projekt leküldéses értesítések küldéséhez
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>A Xamarin.iOS-projekt konfigurálása
[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz
1. A **QSTodoService**, adja hozzá a következő tulajdonságot, hogy **AppDelegate** lekérheti a mobil ügyfél:
   
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
2. Adja hozzá a következő `using` nyilatkozat tetején a **AppDelegate.cs** fájlt.
   
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
3. A **AppDelegate**, bírálja felül a **FinishedLaunching** esemény:
   
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
4. Ugyanebben a fájlban, bírálja felül a **RegisteredForRemoteNotifications** esemény. Ebben a kódban regisztrál egy egyszerű sablon értesítést küldjön el az összes támogatott platformon, a kiszolgáló.
   
    Sablonok a Notification hubs használatával kapcsolatban lásd: [sablonok](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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


1. Ezt követően bírálja felül a **DidReceivedRemoteNotification** esemény:
   
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

Az alkalmazás most frissíteni leküldéses értesítések támogatásához használható.

## <a name="test"></a>Teszt leküldéses értesítések az alkalmazásban
1. Nyomja meg a **futtatása** gombra kattint, hogy a projekt felépítéséhez és a kompatibilis iOS-eszközön indítsa el az alkalmazást, majd kattintson az **OK** leküldéses értesítések fogadásához.
   
   > [!NOTE]
   > Az alkalmazásból explicit módon el kell fogadnia a leküldéses értesítések. A kérelem csak akkor történik meg az első alkalommal futtatja az alkalmazást.
   > 
   > 
2. Az alkalmazásban írjon be egy feladatot, és kattintson a plusz (**+**) ikonra.
3. Győződjön meg arról, hogy értesítést kap, majd kattintson az **OK** az értesítés bezárásának engedélyezése.
4. Ismételje meg a 2. lépés azonnal zárja be az alkalmazást, majd ellenőrizze, hogy megjelenik-e értesítést.

Ez az oktatóanyag sikeresen befejeződött.

<!-- Images. -->

<!-- URLs. -->



