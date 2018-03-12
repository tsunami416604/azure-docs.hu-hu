---
title: "Ismerkedés az Azure Notification Hubs Xamarin.iOS-alkalmazásokkal való használatával | Microsoft Docs"
description: "Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin iOS-alkalmazásokba."
services: notification-hubs
keywords: "ios leküldéses értesítések,leküldéses üzenetek,leküldéses értesítések,leküldéses üzenet"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 38ad8a15fcc4077926e735e01f877a4ee66718ef
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Ismerkedés az Azure Notification Hubs Xamarin.iOS-alkalmazásokkal való használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére iOS-alkalmazásokba. Létre fog hozni egy üres Xamarin.iOS-alkalmazást, amely leküldéses értesítéseket fogad az [Apple Push Notification szolgáltatás (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) használatával. 

Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre. A befejezett kód a minta [NotificationHubs alkalmazásban][GitHub] érhető el.

Ez az oktatóanyag az egyszerű leküldéses üzenetküldési forgatókönyvet mutatja be a Notification Hubs használatával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyaghoz az alábbiakra lesz szükség:

* Az [Xcode][Install Xcode] legújabb verziója
* Az iOS 10-es (vagy újabb) verziójával kompatibilis eszköz
* Tagság az [Apple fejlesztői programjában](https://developer.apple.com/programs/).
* [Visual Studio for Mac]
  
  > [!NOTE]
  > Az iOS leküldéses értesítések konfigurációs követelményei miatt a mintaalkalmazást a szimulátor helyett egy fizikai iOS-eszközön (iPhone vagy iPad) kell üzembe helyeznie és tesztelnie.
  > 
  > 

Ennek az oktatóanyagnak az elvégzése a Xamarin.iOS-alkalmazásokkal kapcsolatos összes további Notification Hubs-oktatóanyag elvégzésének előfeltétele.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Az értesítési központ konfigurálása iOS leküldéses értesítésekhez
Ez a szakasz végigvezeti egy új értesítési központ létrehozásának, valamint az APNS-hitelesítés korábban létrehozott **.p12** leküldéses tanúsítvánnyal történő konfigurálásának lépésein. Ha egy már korábban létrehozott értesítési központot kíván használni, egyből az 5. lépésre ugorhat.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Kattintson az <b>Értesítési szolgáltatások</b> gombra, majd válassza az <b>Apple (APNS)</b> lehetőséget. Válassza a <b>Tanúsítvány</b> lehetőséget, kattintson a fájl ikonra, és válassza ki a korábban exportált <b>.p12</b> fájlt. Ügyeljen rá, hogy a helyes jelszót adja meg.</p>

<p>Válassza a <b>Védőfal</b> módot, mivel ez használható fejlesztéshez. Az <b>Éles</b> beállítást kizárólag akkor használja, ha olyan felhasználóknak szeretne leküldéses értesítéseket küldeni, akik megvásárolták az alkalmazást az áruházból.</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Az APNS konfigurálása az Azure Portalon][6]

&emsp;&emsp;&emsp;&emsp;![APNS-tanúsítvány konfigurálása az Azure Portalon][7]

Az értesítési központ konfigurálva lett az APNS-sel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálásához és leküldéses értesítések küldéséhez.

## <a name="connect-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz
#### <a name="create-a-new-project"></a>Új projekt létrehozása
1. A Visual Studióban hozzon létre egy új iOS-projektet, és válassza az **Egynézetes alkalmazás** sablont, majd kattintson a **Tovább** elemre.
   
     ![Visual Studio – Alkalmazástípus kiválasztása][31]

2. Adja meg az alkalmazás nevét és a céges azonosítót, és kattintson a **Tovább**, majd a **Létrehozás** elemre.

3. A Megoldás nézetben kattintson duplán az *Info.plist* elemre, és az **Identitás** területen győződjön meg arról, hogy a csomagazonosító megegyezik a kiépítési profil létrehozásakor használt értékkel. Az **Aláírás** alatt győződjön meg arról, hogy a Developer-fiók ki van választva a **Csoport** területen, hogy az „Aláírás automatikus kezelése” be van jelölve, és hogy az Aláíró tanúsítvány és létesítési profil automatikusan ki van választva.

    ![Visual Studio – iOS-alkalmazás konfigurációja][32]

4. Adja hozzá az Azure-üzenetkezelési csomagot. A Megoldásnézetben kattintson a jobb gombbal a projektre, és válassza a **Hozzáadás** > **NuGet-csomagok hozzáadása** lehetőséget. Keresse meg a **Xamarin.Azure.NotificationHubs.iOS** elemet, és adja hozzá a csomagot a projektjéhez.

5. Adjon hozzá egy új fájlt az osztályhoz, adja neki a **Constants.cs** nevet, adja hozzá az alábbi változókat, és cserélje le a szövegkonstans helyőrzőit a *központ nevére* és a korábban feljegyzett *DefaultListenSharedAccessSignature* változóra.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. Az **AppDelegate.cs** osztályban adja hozzá a következő using utasítást:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. Deklarálja az **SBNotificationHub** egy példányát:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. Az **AppDelegate.cs** fájlban szerkessze a **FinishedLaunching()** metódust úgy, hogy az egyezzen az alábbiakkal:
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
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

9. Bírálja felül a **RegisteredForRemoteNotifications()** metódust az **AppDelegate.cs** osztályban:
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
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

10. Bírálja felül a **ReceivedRemoteNotification()** metódust az **AppDelegate.cs** osztályban:
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. Hozza létre a következő **ProcessNotification()** metódust az **AppDelegate.cs** osztályban:
   
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
  

12. Futtassa az alkalmazást az eszközön.

## <a name="sending-test-push-notifications"></a>Leküldéses tesztértesítések küldése
Az [Azure Portal] *Tesztküldés* lehetőségével tesztelheti az alkalmazásban az értesítések fogadását. Ez egy leküldéses tesztértesítést küld az eszközre.

![Azure Portal – Küldés tesztelése][30]

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis könyvtár használatával, mint a Mobile Apps vagy az ASP.NET. Ha a háttérszolgáltatáshoz nem érhető el kódtár, az értesítések küldéséhez használhatja közvetlenül a REST API-t is.

Az értesítéseknek ASP.NET-háttérrendszerből történő küldéséhez következő lépésként [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagot ajánljuk. Értesítések küldéséhez azonban az alábbi megközelítések is alkalmazhatók:

Az alábbi listában egyéb oktatóprogramok találhatók, amelyek az értesítések küldésével kapcsolatosak:
* REST-felület: A [REST-felület](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) használatával bármilyen háttérplatformon támogathatja a leküldéses értesítéseket.
* **Microsoft Azure Notification Hubs .NET SDK**: A Visual Studio NuGet-csomagkezelőjében futtassa a következő parancsot: [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* Node.js: [A Notification Hubs használata a Node.js-ből](notification-hubs-nodejs-push-notification-tutorial.md).
* Java/PHP**: A Notification Hubs Javából/PHP-ből való használatát ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)) ismertető témakörben találhat példát arra, hogyan küldhetők leküldéses értesítések a REST API-k használatával.

## <a name="next-steps"></a>További lépések
Ebben az egyszerű példában leküldéses értesítéseket küldött az összes iOS-eszközre. Ha adott felhasználóknak szeretne értesítést küldeni, tekintse meg [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyagot. Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakört. A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával] és [Notification Hubs iOS rendszeren való használatával] foglalkozó témakörben tekinthet meg további információt.

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Notification Hubs használatával]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs iOS rendszeren való használatával]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
