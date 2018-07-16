---
title: Leküldéses értesítések küldése Xamarin.iOS-alkalmazásokba az Azure Notification Hubs használatával | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin iOS-alkalmazásokba.
services: notification-hubs
keywords: ios leküldéses értesítések,leküldéses üzenetek,leküldéses értesítések,leküldéses üzenet
documentationcenter: xamarin
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: cebb73fedffe3b5f0a11c919ff39d1d2acd462d3
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969527"
---
# <a name="tutorial-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése Xamarin.iOS-alkalmazásokba az Azure Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére iOS-alkalmazásokba. Létre fog hozni egy üres Xamarin.iOS-alkalmazást, amely leküldéses értesítéseket fogad az [Apple Push Notification szolgáltatás (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) használatával. 

Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre. A befejezett kód a minta [NotificationHubs alkalmazásban][GitHub] érhető el.

Ebben az oktatóanyagban kódot fog létrehozni/frissíteni az alábbi feladatok végrehajtásához: 

> [!div class="checklist"]
> * A tanúsítvány-aláírási kérelem fájljának létrehozása
> * Alkalmazás regisztrálása leküldéses értesítésekhez
> * Üzembe helyezési profil létrehozása az alkalmazáshoz
> * Az értesítési központ konfigurálása iOS leküldéses értesítésekhez
> * Teszt leküldéses értesítések küldése

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Az [Xcode][Install Xcode] legújabb verziója
- Az iOS 10-es (vagy újabb) verziójával kompatibilis eszköz
- Tagság az [Apple fejlesztői programjában](https://developer.apple.com/programs/).
- [Visual Studio for Mac]
  
  > [!NOTE]
  > Az iOS leküldéses értesítések konfigurációs követelményei miatt a mintaalkalmazást a szimulátor helyett egy fizikai iOS-eszközön (iPhone vagy iPad) kell üzembe helyeznie és tesztelnie.

Ennek az oktatóanyagnak az elvégzése a Xamarin.iOS-alkalmazásokkal kapcsolatos összes további Notification Hubs-oktatóanyag elvégzésének előfeltétele.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Az értesítési központ konfigurálása iOS leküldéses értesítésekhez
Ez a szakasz végigvezeti egy új értesítési központ létrehozásának, valamint az APNS-hitelesítés korábban létrehozott **.p12** leküldéses tanúsítvánnyal történő konfigurálásának lépésein. Ha egy már korábban létrehozott értesítési központot kíván használni, egyből az 5. lépésre ugorhat.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-ios-settings-for-the-notification-hub"></a>Az értesítési központ iOS-beállításainak konfigurálása
1. Válassza az **Apple (APNs)** lehetőséget a **NOTIFICATION SETTINGS** (Értesítési beállítások) csoportban. 
2. Válassza a **Certificate** (Tanúsítvány) lehetőséget, kattintson a **fájl** ikonra, és válassza ki a korábban exportált **.p12** fájlt. 
3. Adja meg a tanúsítványhoz tartozó **jelszót**. 
4. Válassza a **Védőfal** módot. Az **Éles** beállítást kizárólag akkor használja, ha olyan felhasználóknak szeretne leküldéses értesítéseket küldeni, akik megvásárolták az alkalmazást az áruházból.

    ![Az APNS konfigurálása az Azure Portalon][6]

    ![APNS-tanúsítvány konfigurálása az Azure Portalon][7]

Az értesítési központ konfigurálva lett az APNS-sel való együttműködésre, és rendelkezik a kapcsolati sztringekkel az alkalmazás regisztrálásához és leküldéses értesítések küldéséhez.

## <a name="connect-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz
#### <a name="create-a-new-project"></a>Új projekt létrehozása
1. A Visual Studióban hozzon létre egy új iOS-projektet, és válassza az **Egynézetes alkalmazás** sablont, majd kattintson a **Tovább** elemre.
   
     ![Visual Studio – Alkalmazástípus kiválasztása][31]

2. Adja meg az alkalmazás nevét és a céges azonosítót, és kattintson a **Tovább**, majd a **Létrehozás** elemre.

3. A Megoldás nézetben kattintson duplán az *Info.plist* elemre, és az **Identitás** területen győződjön meg arról, hogy a csomagazonosító megegyezik a kiépítési profil létrehozásakor használt értékkel. Az **Aláírás** alatt győződjön meg arról, hogy a Developer-fiók ki van választva a **Csoport** területen, hogy az „Aláírás automatikus kezelése” be van jelölve, és hogy az Aláíró tanúsítvány és létesítési profil automatikusan ki van választva.

    ![Visual Studio – iOS-alkalmazás konfigurációja][32]

4. A Megoldás nézetben kattintson duplán az *Entitlements.plist* elemre, és ügyeljen arra, hogy a „Leküldéses értesítések engedélyezése” beállítás be legyen jelölve.

    ![Visual Studio – iOS-jogosultságok konfigurációja][33]

5. Adja hozzá az Azure-üzenetkezelési csomagot. A Megoldásnézetben kattintson a jobb gombbal a projektre, és válassza a **Hozzáadás** > **NuGet-csomagok hozzáadása** lehetőséget. Keresse meg a **Xamarin.Azure.NotificationHubs.iOS** elemet, és adja hozzá a csomagot a projektjéhez.

6. Adjon hozzá egy új fájlt az osztályhoz, adja neki a **Constants.cs** nevet, adja hozzá az alábbi változókat, és cserélje le a szövegkonstans helyőrzőit a *központ nevére* és a korábban feljegyzett *DefaultListenSharedAccessSignature* változóra.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ListenConnectionString = "<Azure connection string>";
        public const string NotificationHubName = "<Azure hub path>";
    ```

7. Az **AppDelegate.cs** osztályban adja hozzá a következő using utasítást:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

8. Deklarálja az **SBNotificationHub** egy példányát:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

9.  Az **AppDelegate.cs** fájlban frissítse a **FinishedLaunching()** metódust úgy, hogy az egyezzen az alábbi kóddal:
  
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
            {
                UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Sound,
                                                                      (granted, error) =>
                {
                    if (granted)
                        InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
                });
            } else if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

10. Bírálja felül a **RegisteredForRemoteNotifications()** metódust az **AppDelegate.cs** osztályban:
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

11. Bírálja felül a **ReceivedRemoteNotification()** metódust az **AppDelegate.cs** osztályban:
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

12. Hozza létre a következő **ProcessNotification()** metódust az **AppDelegate.cs** osztályban:
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > Dönthet úgy, hogy felülbírálja a **FailedToRegisterForRemoteNotifications()** metódust az olyan helyzetek kezelésére, amikor például nem áll rendelkezésre hálózati kapcsolat. Ez különösen fontos, ha a felhasználók offline módban is elindíthatják az alkalmazást (például repülőgép üzemmódban), és kezelni szeretné az alkalmazással kapcsolatos egyedi leküldéses üzenetküldési forgatókönyveket.
  

13. Futtassa az alkalmazást az eszközön.

## <a name="send-test-push-notifications"></a>Teszt leküldéses értesítések küldése
Az [Azure Portal] *Tesztküldés* lehetőségével tesztelheti az alkalmazásban az értesítések fogadását. A parancs egy leküldéses tesztértesítést küld az eszközre.

![Azure Portal – Küldés tesztelése][30]

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis könyvtár használatával, mint a Mobile Apps vagy az ASP.NET. Ha a háttérszolgáltatáshoz nem érhető el kódtár, az értesítések küldéséhez használhatja közvetlenül a REST API-t is.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban szórásos értesítéseket küldött a háttérrendszerben regisztrált iOS-eszközök mindegyikének. Ha szeretné megtudni, hogy hogyan küldhet értesítéseket adott iOS-eszközökre, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
