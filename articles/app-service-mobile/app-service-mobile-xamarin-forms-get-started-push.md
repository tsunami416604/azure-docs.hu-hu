---
title: Leküldéses értesítések hozzáadása a Xamarin.Forms alkalmazáshoz
description: Ismerje meg, hogyan küldhet többplatformos leküldéses értesítéseket az Azure-szolgáltatásokkal a Xamarin.Forms-alkalmazásoknak.
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f23ac2d693492695c398893c103d5a77a0e93129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461470"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Leküldéses értesítések hozzáadása a Xamarin.Forms alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket adhat hozzá a [Xamarin.Forms gyorsindításból](app-service-mobile-xamarin-forms-get-started.md)származó összes projekthez. Ez azt jelenti, hogy a rendszer leküldéses értesítést küld minden platformfüggetlen ügyfélnek minden alkalommal, amikor egy rekordot beszúr.

Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, szüksége lesz a leküldéses értesítésbővítmény csomagra. További információt [a .NET háttérkiszolgáló SDK azure mobile apps szolgáltatásához című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Előfeltételek

IOS esetén [Apple Developer Program tagságra](https://developer.apple.com/programs/ios/) és fizikai iOS-eszközre lesz szüksége. Az [iOS-szimulátor nem támogatja a leküldéses értesítéseket.](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>A kiszolgálóprojekt frissítése leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Az Android-projekt konfigurálása és futtatása (nem kötelező)

Töltse ki ezt a szakaszt, hogy leküldéses értesítéseket a Xamarin.Forms Droid projekt Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Firebase cloud messaging (FCM) engedélyezése

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>A mobilalkalmazások háttértartalékának konfigurálása leküldéses kérelmek küldéséhez az FCM használatával

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Leküldéses értesítések hozzáadása az Android-projekthez

Az FCM-mel konfigurált háttérrendszerlel hozzáadhat összetevőket és kódokat az ügyfélhez az FCM-hez való regisztrációhoz. Az Azure Értesítési központokkal is regisztrálhat leküldéses értesítésekre a mobilalkalmazások háttérrendszerén keresztül, és értesítéseket kaphat.

1. A **Droid** projektben kattintson a jobb gombbal **a Hivatkozások > A NuGet csomagok kezelése ...**.
1. A NuGet csomagkezelő ablakban keresse meg a **Xamarin.Firebase.Messaging** csomagot, és adja hozzá a projekthez.
1. A **Droid** projekt projekt tulajdonságaiban állítsa be az alkalmazást az Android 7.0-s vagy újabb verziójával történő összeállításra.
1. Adja hozzá a Firebase konzolról letöltött **google-services.json** fájlt a **Droid** projekt gyökeréhez, és állítsa be a buildműveletet a **GoogleServicesJson-ra.** További információt [a Google Services JSON-fájl hozzáadása című témakörben](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File)talál.

#### <a name="registering-with-firebase-cloud-messaging"></a>Regisztráció a Firebase Cloud Messagingben

1. Nyissa meg az **AndroidManifest.xml** fájlt, és illessze a következő `<receiver>` elemeket a `<application>` elembe:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>A Firebase-példányazonosító szolgáltatás megvalósítása

1. Adjon hozzá egy új osztályt a **Droid** projekthez, `FirebaseRegistrationService`és győződjön meg arról, hogy a fájl tetején a következő `using` állítások találhatók:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Cserélje le `FirebaseRegistrationService` az üres osztályt a következő kódra:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    Az `FirebaseRegistrationService` osztály felelős a biztonsági jogkivonatok létrehozásáért, amelyek engedélyezik az alkalmazás hozzáférését az FCM-hez. A `OnTokenRefresh` metódus meghívása akkor történik meg, amikor az alkalmazás regisztrációs jogkivonatot kap az FCM-től. A metódus lekéri a `FirebaseInstanceId.Instance.Token` jogkivonatot a tulajdonságból, amelyet az FCM aszinkron módon frissít. A `OnTokenRefresh` metódus ritkán van meghívva, mert a jogkivonat csak akkor frissül, ha az alkalmazás telepítve van vagy eltávolítva, amikor a felhasználó törli az alkalmazásadatait, amikor az alkalmazás törli a példányazonosítót, vagy ha a jogkivonat biztonsága veszélybe került. Emellett az FCM-példány azonosító szolgáltatás kérni fogja, hogy az alkalmazás rendszeresen frissítse a jogkivonatot, általában 6 havonta.

    A `OnTokenRefresh` metódus is `SendRegistrationTokenToAzureNotificationHub` meghívja a metódust, amely a felhasználó regisztrációs jogkivonatának az Azure Értesítési központhoz társítására szolgál.

#### <a name="registering-with-the-azure-notification-hub"></a>Regisztráció az Azure Értesítési központban

1. Adjon hozzá egy új osztályt a **Droid** projekthez, `AzureNotificationHubService`és győződjön meg arról, hogy a fájl tetején a következő `using` állítások találhatók:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Cserélje le `AzureNotificationHubService` az üres osztályt a következő kódra:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    A `RegisterAsync` metódus létrehoz egy egyszerű értesítési üzenet sablont JSON néven, és regisztrálja a sablonértesítések fogadására az értesítési központból a Firebase regisztrációs jogkivonat használatával. Ez biztosítja, hogy az Azure Értesítési központból küldött értesítések a regisztrációs jogkivonat által képviselt eszközt célozzák meg.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Leküldéses értesítés tartalmának megjelenítése

1. Adjon hozzá egy új osztályt a **Droid** projekthez, `FirebaseNotificationService`és győződjön meg arról, hogy a fájl tetején a következő `using` állítások találhatók:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Cserélje le `FirebaseNotificationService` az üres osztályt a következő kódra:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            //Unique request code to avoid PendingIntent collision.
            var requestCode = new Random().Next();
            var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);
            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    A `OnMessageReceived` metódus, amely akkor hívható meg, amikor egy alkalmazás értesítést kap az `SendNotification` FCM-től, kinyeri az üzenet tartalmát, és meghívja a metódust. Ez a módszer az üzenet tartalmát helyi értesítéssé alakítja, amely az alkalmazás futása közben indul el, és az értesítés az értesítési területen jelenik meg.

Most már készen áll a leküldéses értesítések tesztelésére az Android-eszközön vagy az emulátoron futó alkalmazásban.

### <a name="test-push-notifications-in-your-android-app"></a>Leküldéses értesítések tesztelése az Android-alkalmazásban

Az első két lépés csak akkor szükséges, ha emulátoron tesztel.

1. Győződjön meg arról, hogy a Google Play-szolgáltatásokkal konfigurált eszközre vagy emulátorra telepít vagy hibakeresést tesz le. Ez ellenőrizhető annak ellenőrzésével, hogy a **Play-alkalmazások** telepítve vannak-e az eszközön vagy az emulátoron.
2. Adjon hozzá egy Google-fiókot az Android-eszközhöz az **Alkalmazások** > **beállításai** > **fiók hozzáadása**elemre kattintva. Ezután kövesse az utasításokat, hogy meglévő Google-fiókot adjon hozzá az eszközhöz, vagy hozzon létre egy újat.
3. A Visual Studio vagy a Xamarin Studio alkalmazásban kattintson a jobb gombbal a **Droid** projektre, és válassza a **Beállítás indítási projektként parancsra**.
4. Kattintson a **Futtatás** gombra a projekt létrehozásához és az alkalmazás androidos eszközén vagy emulátorán való elindításához.
5. Az alkalmazásban írjon be egy feladatot,**+** majd kattintson a plusz ( ) ikonra.
6. Ellenőrizze, hogy egy elem hozzáadásakor érkezik-e értesítés.

## <a name="configure-and-run-the-ios-project-optional"></a>Az iOS-projekt konfigurálása és futtatása (nem kötelező)

Ez a szakasz az iOS-eszközökhöz készült Xamarin iOS-projektek futtatásával foglalkozik. Kihagyhatja ezt a részt, ha nem dolgozik iOS-eszközökkel.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Az ÉRTESÍTÉSI-központ konfigurálása az APNS-hez

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Ezután konfigurálja az iOS-projekt beállítást a Xamarin Studio vagy a Visual Studio alkalmazásban.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Leküldéses értesítések hozzáadása az iOS-alkalmazáshoz

1. Az **iOS-projektben** nyissa meg AppDelegate.cs, és adja hozzá a következő utasítást a kódfájl tetejéhez.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Az **AppDelegate** osztályban adjon hozzá egy felülbírálási felülbírálatot a **RegisteredForRemoteNotifications** eseményhez az értesítések regisztrálásához:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. Az **AppDelegate**alkalmazásban adja hozzá a **DidReceiveRemoteNotification** eseménykezelő következő felülbírálatát is:

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Ez a módszer kezeli a bejövő értesítéseket, miközben az alkalmazás fut.

4. Az **AppDelegate** osztályban adja hozzá a következő kódot a **FinishedLaunch** metódushoz:

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Ez lehetővé teszi a távoli értesítések és kérelmek leküldéses regisztrációjának támogatását.

Az alkalmazás most frissül, hogy támogassa a leküldéses értesítéseket.

#### <a name="test-push-notifications-in-your-ios-app"></a>Leküldéses értesítések tesztelése az iOS-alkalmazásban

1. Kattintson a jobb gombbal az iOS-projektre, és válassza **a Beállítás indítási projektként parancsra.**
2. A **Futtatás** vagy az **F5** billentyűt a Visual Studióban a projekt létrehozásához és az alkalmazás iOS-eszközön való elindításához indítsa el. Ezután kattintson **az OK** gombra a leküldéses értesítések elfogadásához.

   > [!NOTE]
   > Explicit módon el kell fogadnia az alkalmazásleküldéses értesítéseket. Ez a kérés csak akkor fordul elő, amikor az alkalmazás első alkalommal fut.

3. Az alkalmazásban írjon be egy feladatot,**+** majd kattintson a plusz ( ) ikonra.
4. Ellenőrizze, hogy érkezett-e értesítés, majd kattintson az **OK** gombra az értesítés elvetéséhez.

## <a name="configure-and-run-windows-projects-optional"></a>Windows-projektek konfigurálása és futtatása (nem kötelező)

Ez a rész a Xamarin.Forms WinApp és WinPhone81 projektek Windows-eszközökre való futtatásához használható. Ezek a lépések támogatják az Univerzális Windows platform (UWP) projekteket is. Kihagyhatja ezt a részt, ha nem dolgozik Windows-eszközökkel.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Windows-alkalmazás regisztrálása leküldéses értesítésekhez a Windows értesítési szolgáltatással (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS értesítési központjának konfigurálása

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Leküldéses értesítések hozzáadása a Windows-alkalmazáshoz

1. A Visual Studióban nyissa meg **a App.xaml.cs** egy Windows-projektben, és adja hozzá a következő állításokat.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Cserélje `<your_TodoItemManager_portable_class_namespace>` le az `TodoItemManager` osztályt tartalmazó hordozható projekt névterére.

2. A App.xaml.cs adja hozzá a következő **InitNotificationsAsync** metódust:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Ez a módszer leadja a leküldéses értesítési csatornát, és regisztrál egy sablont, amely sablonértesítéseket kap az értesítési központból. A rendszer egy sablonértesítést küld az ügyfélnek, amely támogatja a *Param üzenetet.*

3. A App.xaml.cs frissítse az **OnLaunched** eseménykezelő `async` metódus definícióját a módosító hozzáadásával. Ezután a módszer végén adja hozzá a következő kódsort:

    ```csharp
    await InitNotificationsAsync();
    ```

    Ez biztosítja, hogy a leküldéses értesítés regisztrációja jön létre, vagy frissül minden alkalommal, amikor az alkalmazás elindul. Fontos, hogy ezt annak biztosítása érdekében, hogy a WNS push csatorna mindig aktív.  

4. A Visual Studio Megoldáskezelőjében nyissa meg a **Package.appxmanifest** fájlt, és állítsa a **Toast Capable (Igen)** lehetőséget az Értesítések **csoportban.** **Yes**
5. Építse fel az alkalmazást, és ellenőrizze, hogy nincsenek-e hibák. Az ügyfélalkalmazásnak most regisztrálnia kell a sablonértesítésekre a Mobilalkalmazások háttérrendszeréről. Ismételje meg ezt a szakaszt a megoldás minden Windows-projektjéhez.

#### <a name="test-push-notifications-in-your-windows-app"></a>Leküldéses értesítések tesztelése a Windows alkalmazásban

1. A Visual Studio programban kattintson a jobb gombbal egy Windows-projektre, és válassza **a Beállítás indítási projektként parancsra.**
2. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás elindításához.
3. Az alkalmazásban írja be egy új todoitem nevét, majd**+** a plusz ( ) ikonra kattintva adja hozzá.
4. Ellenőrizze, hogy az elem hozzáadásakor érkezik-e értesítés.

## <a name="next-steps"></a>További lépések

További információ a leküldéses értesítésekről:

* [Leküldéses értesítések küldése az Azure mobilalkalmazásokból](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Távoli értesítések firebase-felhőalapú üzenetküldéssel](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Leküldéses értesítési problémák diagnosztizálása](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Számos oka lehet annak, hogy az értesítések megszakadhatnak, vagy nem kerülnek az eszközökre. Ez a témakör bemutatja, hogyan elemezheti és találhatja ki a leküldéses értesítési hibák kiváltó okát.

Az alábbi oktatóanyagok egyikére is folytathatja:

* [Hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ismerje meg, hogyan hitelesítheti alkalmazása felhasználóit identitásszolgáltató használatával.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. Az offline szinkronizálással a felhasználók&mdash;akkor is kezelhetik a&mdash;mobilalkalmazásokat, hogy megtekintsék, hozzáadják vagy módosítsák az adatokat, ha nincs hálózati kapcsolat.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
