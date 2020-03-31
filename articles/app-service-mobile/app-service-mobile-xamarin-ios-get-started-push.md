---
title: Leküldéses értesítések hozzáadása a Xamarin.iOS alkalmazáshoz
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az Azure App Service használatával a Xamarin.iOS alkalmazásnak.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249281"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Push-értesítések hozzáadása a Xamarin.iOS alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá a [Xamarin.iOS gyorsindítási](app-service-mobile-xamarin-ios-get-started.md) projekthez, hogy a rendszer leküldéses értesítést küldjön az eszköznek minden alkalommal, amikor egy rekordot beszúr.

Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, szüksége lesz a leküldéses értesítésbővítmény csomagra. További [információ: A .NET háttérkiszolgáló SDK-ja az Azure Mobile Apps alkalmazáshoz](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) című témakörben található.

## <a name="prerequisites"></a>Előfeltételek

* Töltse ki a [Xamarin.iOS rövid útmutatót.](app-service-mobile-xamarin-ios-get-started.md)
* Fizikai iOS-eszköz. A leküldéses értesítéseket az iOS-szimulátor nem támogatja.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Regisztrálja az alkalmazást leküldéses értesítésekhez az Apple fejlesztői portálján

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>A mobilalkalmazás konfigurálása leküldéses értesítések küldésére

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>A kiszolgálóprojekt frissítése leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>A Xamarin.iOS projekt konfigurálása

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz

1. A **QSTodoService**alkalmazásban adja hozzá a következő tulajdonságot, hogy az **AppDelegate** megszerezhesse a mobil ügyfelet:

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

2. Adja hozzá `using` a következő utasítást a **AppDelegate.cs** fájl tetejéhez.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. Az **AppDelegate**alkalmazásban felülbírálja a **FinishedLaunch** eseményt:

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

4. Ugyanabban a fájlban `RegisteredForRemoteNotifications` felülbírálja az eseményt. Ebben a kódban egy egyszerű sablonértesítésre regisztrál, amelyet a kiszolgáló az összes támogatott platformon elküld.

    Az Értesítési központtal rendelkező sablonokról a [Sablonok című témakörben olvashat bővebben.](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)

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

5. Ezután felülbírálja a **DidReceivedRemoteNotification** eseményt:

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

Az alkalmazás most frissül, hogy támogassa a leküldéses értesítéseket.

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Leküldéses értesítések tesztelése az alkalmazásban

1. A **Futtatás** gombra kattintva megépíti a projektet, és iOS-kompatibilis eszközön indítja el az alkalmazást, majd kattintson az **OK** gombra a leküldéses értesítések elfogadásához.

   > [!NOTE]
   > Explicit módon el kell fogadnia az alkalmazásleküldéses értesítéseket. Ez a kérés csak akkor fordul elő, amikor az alkalmazás első alkalommal fut.

2. Az alkalmazásban írjon be egy feladatot,**+** majd kattintson a plusz ( ) ikonra.
3. Ellenőrizze, hogy érkezett-e értesítés, majd kattintson az **OK** gombra az értesítés elvetéséhez.
4. Ismételje meg a 2.

Sikeresen befejezte ezt az oktatóanyagot.
