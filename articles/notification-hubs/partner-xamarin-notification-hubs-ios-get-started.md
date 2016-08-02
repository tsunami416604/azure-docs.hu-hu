<properties
    pageTitle="iOS leküldéses értesítések küldése a Notification Hubs használatával Xamarin-alkalmazásokba | Microsoft Azure"
    description="Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin iOS-alkalmazásokba."
    services="notification-hubs"
    keywords="ios push notifications,push messages,push notifications,push message"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="02/15/2016"
    ms.author="yuaxu"/>

# iOS leküldéses értesítések küldése a Notification Hubs használatával Xamarin-alkalmazásokba

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Áttekintés
> [AZURE.IMPORTANT] Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére iOS-alkalmazásokba.
Létre fog hozni egy üres Xamarin.iOS-alkalmazást, amely leküldéses értesítéseket fogad az [Apple Push Notification szolgáltatás (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) használatával. Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre. A befejezett kód a minta [NotificationHubs-alkalmazásban][GitHub] érhető el.

Ez az oktatóanyag az egyszerű leküldéses üzenetküldési forgatókönyvet mutatja be a Notification Hubs használatával.

##Előfeltételek

Az oktatóanyaghoz az alábbiakra lesz szükség:

+ [Xcode 6.0][Az Xcode telepítése]
+ Az iOS 7.0-tel (vagy újabb verzióval) kompatibilis eszköz
+ iOS fejlesztőprogrambeli tagság
+ [Xamarin Studio]

   > [AZURE.NOTE] Az iOS leküldéses értesítések konfigurációs követelményei miatt a mintaalkalmazást a szimulátor helyett egy fizikai iOS-eszközön (iPhone vagy iPad) kell üzembe helyeznie és tesztelnie.

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Xamarin iOS-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##Az értesítési központ konfigurálása

Ez a szakasz végigvezeti egy új értesítési központ létrehozásának és az APNS-hitelesítés konfigurálásának folyamatán a létrehozott **.p12** leküldéses tanúsítvány használatával. Ha egy már korábban létrehozott értesítési központot kíván használni, egyből az 5. lépésre ugorhat.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Mivel az APNS-kapcsolatot szeretnénk konfigurálni, nyissa meg az értesítési központ beállításait az Azure portálon, kattintson a <b>Notification Services</b> (Értesítési szolgáltatások) lehetőségre, majd az <b>Apple (APNS)</b> elemre a listában. Amint elkészült, kattintson az <b>Upload Certificate</b> (Tanúsítvány feltöltése) parancsra, és jelölje ki a korábban exportált <b>.p12</b> tanúsítványt, valamint adja meg annak jelszavát.</p>
<p>Válassza a <b>Sandbox</b> (Védőfal) módot, mivel fejlesztői környezetben fog leküldéses üzeneteket küldeni. A <b>Production</b> (Éles) beállítást kizárólag akkor használja, ha olyan felhasználóknak szeretne leküldéses értesítéseket küldeni, akik már megvásárolták az alkalmazást az áruházból.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


Az értesítési központ konfigurálva lett az APNS-sel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálásához és leküldéses értesítések küldéséhez.


##Az alkalmazás csatlakoztatása az értesítési központhoz

#### Új projekt létrehozása

1. A Xamarin Studióban hozzon létre egy új iOS-projektet, és válassza a **Unified API** > **Single View Application** (Egyesített API > Egynézetes alkalmazás) sablont.

    ![Xamarin Studio – Alkalmazástípus kiválasztása][31]

2. Adja hozzá az Azure Messaging összetevőre mutató hivatkozást. A Solution (Megoldás) nézetben kattintson a jobb gombbal a projekt **Components** (Összetevők) mappájára, és válassza a **Get More Components** (További összetevők beszerzése) lehetőséget. Keresse meg az **Azure Messaging** összetevőt, és adja hozzá a projekthez.

3. Az **AppDelegate.cs** osztályban adja hozzá a következő using utasítást:

        using WindowsAzure.Messaging;

4. Deklarálja az **SBNotificationHub** egy példányát:

        private SBNotificationHub Hub { get; set; }

5. Hozza létre a **Constants.cs** osztályt a következő változókkal:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. Az **AppDelegate.cs** fájlban szerkessze a **FinishedLaunching()** metódust úgy, hogy az egyezzen az alábbiakkal:

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

7. Bírálja felül a **RegisteredForRemoteNotifications()** metódust az **AppDelegate.cs** osztályban:

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

8. Bírálja felül a **ReceivedRemoteNotification()** metódust az **AppDelegate.cs** osztályban:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Hozza létre a következő **ProcessNotification()** metódust az **AppDelegate.cs** osztályban:

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

    > [AZURE.NOTE] Dönthet úgy, hogy felülbírálja a **FailedToRegisterForRemoteNotifications()** metódust az olyan helyzetek kezelésére, amikor például nem áll rendelkezésre hálózati kapcsolat. Ez különösen fontos, ha a felhasználók offline módban is elindíthatják az alkalmazást (például repülőgép üzemmódban), és kezelni szeretné az alkalmazással kapcsolatos egyedi leküldéses üzenetküldési forgatókönyveket.


10. Futtassa az alkalmazást az eszközön.


## Leküldéses értesítések küldése


A leküldéses értesítések fogadásának az alkalmazásban való teszteléséhez értesítéseket küldhet az [Azure portálról] közvetlenül az értesítési központ lapján található **Hibaelhárítás** eszközkészlet **Küldés tesztelése** funkciója használatával, az alábbi képernyőn látható módon.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. A leküldéses üzenetek küldéséhez használhatja közvetlenül a REST API-t is, ha a forgatókönyvben nem érhető el kódtár. 

Ebben az oktatóanyagban az egyszerűbb megoldást választjuk, és az ügyfélalkalmazás tesztelését egy konzolalkalmazás értesítési központjának .NET SDK-ja használatával küldött értesítésekkel mutatjuk be háttérszolgáltatás használata helyett. Az értesítéseknek ASP.NET-háttérrendszerből történő küldéséhez következő lépésként [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára](notification-hubs-aspnet-backend-ios-notify-users.md) oktatóanyagot ajánljuk. Értesítések küldéséhez azonban az alábbi megközelítések is alkalmazhatók:

* **REST-felület**: A [REST-felület](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) használatával bármilyen háttérplatformon támogathatja a leküldéses értesítéseket.

* **Microsoft Azure Notification Hubs .NET SDK**: A Visual Studio NuGet-csomagkezelőjében futtassa a következő parancsot: [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: [A Notification Hub használata a Node.js-ből](notification-hubs-nodejs-how-to-use-notification-hubs.md).

* **Azure Mobile Services**: A „Leküldéses értesítések a Mobile Services szolgáltatásban – első lépések” ([.NET-háttérrendszer](../mobile-services/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md) | [JavaScript-háttérrendszer](../mobile-services/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md)) témakörben találhat példát arra, hogy hogyan küldhetők leküldéses értesítések a Notification Hubs szolgáltatással integrált Azure Mobile Services Backend háttérrendszerből.

* **Java/PHP**: „A Notification Hubs használata Javából/PHP-ből” ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)) témakörben találhat példát arra, hogyan küldhetők leküldéses értesítések a REST API-k használatával.


####(Választható) Leküldéses értesítések küldése .NET-konzolalkalmazásból

Ebben a szakaszban egy egyszerű .NET konzolalkalmazás használatával küldünk leküldéses értesítéseket. A jelen példában átváltunk Windows fejlesztői környezetre, amelyben a Visual Studio már telepítve lett.

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást:

    ![Visual Studio – Új konzolalkalmazás létrehozása][213]

2. A Visual Studióban kattintson az **Eszközök**, a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Package Manager Console** (Csomagkezelő konzol) elemre.

    A csomagkezelő konzolnak a Visual Studio munkaterületének aljához rögzítve kell megjelennie.

3. A Package Manager Console (Csomagkezelő konzol) ablakban az **Alapértelmezett projekt** értékeként adja meg az új konzolalkalmazás-projektet, majd a konzolablakban hajtsa végre az alábbi parancsot:

        Install-Package Microsoft.Azure.NotificationHubs

    Ezzel hozzáad a <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-csomagot</a> használó Azure Notification Hubs SDK-ra mutató hivatkozást.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Nyissa meg a `Program.cs` fájlt, majd adja hozzá a következő `using` utasítást, amely biztosítja, hogy használhatunk Azure-osztályokat és -függvényeket a fő osztályban:

        using Microsoft.Azure.NotificationHubs;

3. A `Program` osztályban adja hozzá a következő metódust (ne felejtse el lecserélni a **connection string** (kapcsolati karakterlánc) és a **hub name** (központ neve) helyőrzőt):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Adja hozzá a következő sorokat a `Main` metódushoz:

         SendNotificationAsync();
         Console.ReadLine();

5. Nyomja le az F5 billentyűt az alkalmazás futtatásához. Néhány másodpercen belül meg kell jelennie egy leküldéses értesítésnek az eszközön. Függetlenül attól, hogy Wi-Fi- vagy mobil adatátviteli hálózathoz csatlakozik, ellenőrizze, hogy van-e aktív internetkapcsolat az eszközön.

Az összes lehetséges hasznos adatot megtalálja az Apple [helyi és leküldéses értesítések programozásával foglalkozó útmutatójában].

####(Választható) Értesítések küldése mobilszolgáltatásból

Ebben a szakaszban egy mobilszolgáltatás használatával küldünk leküldéses értesítéseket egy csomópontparancsfájl segítségével.

Ha mobilszolgáltatással kíván értesítést küldeni, kövesse [A Mobile Services használatának első lépéseit] című témakör utasításait, majd:

1. Jelentkezzen be a [klasszikus Azure portálra], majd jelölje ki a mobilszolgáltatást.

2. Válassza az oldal tetején található **Scheduler** fület.

    ![Klasszikus Azure portál – Scheduler][215]

3. Hozzon létre egy új ütemezett feladatot, szúrjon be egy nevet, és válassza az **On demand** (Igény szerint) lehetőséget.

    ![Klasszikus Azure portál – Új feladat létrehozása][216]

4. A feladat létrehozását követően kattintson a feladat nevére. Ezután válassza a felső sávon található **Script** (Parancsfájl) fület.

5. Szúrja be a következő parancsfájlt a Scheduler függvényébe. Cserélje le a helyőrzőket az értesítési központ nevére és a *DefaultFullSharedAccessSignature* kapcsolati karakterláncra, amelyet korábban szerzett be. Kattintson a **Save** (Mentés) gombra.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Kattintson az alsó sáv **Run Once** (Futtatás egyszer) parancsára. Az eszköznek egy riasztást kell fogadnia.

##Következő lépések

Ebben az egyszerű példában leküldéses értesítéseket küldött az összes iOS-eszközre. Ha adott felhasználóknak szeretne értesítést küldeni, tekintse meg [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyagot. Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakört. A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával] és [Notification Hubs iOS rendszeren való használatával] foglalkozó témakörben tekinthet meg további információt.


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Alkalmazáslap elküldése]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Saját alkalmazások]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[A Mobile Services használatának első lépéseit]: /develop/mobile/tutorials/get-started-xamarin-ios
[klasszikus Azure portálra]: https://manage.windowsazure.com/
[Notification Hubs használatával]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs útmutató iOS-hez]: http://msdn.microsoft.com/library/jj927168.aspx
[Az Xcode telepítése]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: /manage/services/notification-hubs/notify-users-aspnet
[A legfrissebb hírek elküldése a Notification Hubs használatával]: /manage/services/notification-hubs/breaking-news-dotnet

[helyi és leküldéses értesítések programozásával foglalkozó útmutatójában]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification szolgáltatás]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services összetevő]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Azure portálról]: https://portal.azure.com



<!--HONumber=Jun16_HO2-->


