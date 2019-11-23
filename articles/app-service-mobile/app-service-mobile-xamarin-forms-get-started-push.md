---
title: Leküldéses értesítések hozzáadása a Xamarin. Forms alkalmazáshoz | Microsoft Docs
description: Ismerje meg, hogyan küldhet többplatformos leküldéses értesítéseket az Azure-szolgáltatásokkal a Xamarin. Forms alkalmazásokban.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: ce5750a5071ee0dfc257498f83f41b6d59c99a8b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388518"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Leküldéses értesítések hozzáadása a Xamarin. Forms alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá az összes olyan projekthez, amely a [Xamarin. Forms gyors üzembe helyezését](app-service-mobile-xamarin-forms-get-started.md)eredményezte. Ez azt jelenti, hogy a rendszer minden egyes rekord beszúrásakor leküldéses értesítést küld az összes platformfüggetlen ügyfélnek.

Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, szüksége lesz a leküldéses értesítési bővítmény csomagra. További információ: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

IOS esetén szüksége lesz az [Apple Developer program tagságára](https://developer.apple.com/programs/ios/) és egy fizikai iOS-eszközre. Az [iOS-szimulátor nem támogatja a leküldéses értesítéseket](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>A kiszolgálói projekt frissítése leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Az Android-projekt konfigurálása és futtatása (nem kötelező)

Ennek a szakasznak a végrehajtásával engedélyezheti a leküldéses értesítéseket a Xamarin. Forms droid Project for Android platformon.

### <a name="enable-firebase-cloud-messaging-fcm"></a>A Firebase Cloud Messaging (FCM) engedélyezése

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>A Mobile Apps-háttér konfigurálása a leküldéses kérések az FCM használatával történő küldéséhez

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Leküldéses értesítések hozzáadása az Android-projekthez

Az FCM-mel konfigurált háttér-beállításokkal összetevőket és kódokat adhat hozzá az ügyfélhez az FCM-sel való regisztrációhoz. A leküldéses értesítések az Azure Notification Hubs segítségével is regisztrálhatók a Mobile Apps háttérrel, és értesítéseket kaphatnak.

1. A **droid** projektben kattintson a jobb gombbal a **referenciák > NuGet-csomagok kezelése..** . elemre.
1. A NuGet csomagkezelő ablakban keresse meg a **Xamarin. Firebase. Messaging** csomagot, és adja hozzá a projekthez.
1. A **droid** projekthez tartozó projekt tulajdonságainál állítsa az alkalmazást az Android 7,0-es vagy újabb verziójának fordításához.
1. Adja hozzá a Firebase-konzolról letöltött **Google-Services. JSON** fájlt a **droid** -projekt gyökeréhez, és állítsa a Build műveletet a **GoogleServicesJson**értékre. További információ: [Add The Google Services JSON-fájl](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

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

#### <a name="implementing-the-firebase-instance-id-service"></a>A Firebase példány-azonosító szolgáltatás implementálása

1. Adjon hozzá egy új osztályt a `FirebaseRegistrationService`nevű **droid** -projekthez, és győződjön meg arról, hogy az alábbi `using` utasítások szerepelnek a fájl tetején:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Cserélje le az üres `FirebaseRegistrationService` osztályt a következő kódra:

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

    A `FirebaseRegistrationService` osztály feladata olyan biztonsági jogkivonatok létrehozása, amelyek engedélyezik az alkalmazás számára az FCM elérését. A `OnTokenRefresh` metódus akkor kerül meghívásra, ha az alkalmazás egy regisztrációs jogkivonatot kap az FCM-ből. A metódus lekéri a jogkivonatot a `FirebaseInstanceId.Instance.Token` tulajdonságból, amelyet az FCM aszinkron módon frissít. A `OnTokenRefresh` metódus ritkán van meghívva, mert a token csak akkor frissül, ha az alkalmazás telepítve van vagy el lett távolítva, amikor a felhasználó törli az alkalmazásadatok számát, amikor az alkalmazás törli a példány AZONOSÍTÓját, vagy ha a token biztonsága sérült. Emellett az FCM instance ID szolgáltatás azt is kéri, hogy az alkalmazás rendszeres időközönként frissíti a tokent, általában 6 havonta.

    A `OnTokenRefresh` metódus a `SendRegistrationTokenToAzureNotificationHub` metódust is meghívja, amely a felhasználó regisztrációs jogkivonatának az Azure Notification hub szolgáltatással való hozzárendelésére szolgál.

#### <a name="registering-with-the-azure-notification-hub"></a>Regisztrálás az Azure Notification hub-ban

1. Adjon hozzá egy új osztályt a `AzureNotificationHubService`nevű **droid** -projekthez, és győződjön meg arról, hogy az alábbi `using` utasítások szerepelnek a fájl tetején:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Cserélje le az üres `AzureNotificationHubService` osztályt a következő kódra:

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

    A `RegisterAsync` metódus egy egyszerű értesítési üzenetet hoz létre JSON-ként, és regisztrálja az értesítési központban található sablon-értesítések fogadását a Firebase regisztrációs jogkivonat használatával. Ez biztosítja, hogy az Azure Notification hub által küldött értesítések a regisztrációs jogkivonat által jelzett eszközt célozzák meg.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Leküldéses értesítés tartalmának megjelenítése

1. Adjon hozzá egy új osztályt a `FirebaseNotificationService`nevű **droid** -projekthez, és győződjön meg arról, hogy az alábbi `using` utasítások szerepelnek a fájl tetején:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Cserélje le az üres `FirebaseNotificationService` osztályt a következő kódra:

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
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

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

    A `OnMessageReceived` metódus, amely akkor jelenik meg, amikor egy alkalmazás értesítést kap az FCM-ből, kibontja az üzenet tartalmát, és meghívja a `SendNotification` metódust. Ez a módszer az üzenet tartalmát az alkalmazás futása közben indított helyi értesítésre konvertálja, és az értesítési területen megjelenik az értesítés.

Most már készen áll az Android-eszközön vagy az emulátoron futó alkalmazásban futtatott leküldéses értesítések tesztelésére.

### <a name="test-push-notifications-in-your-android-app"></a>Leküldéses értesítések tesztelése Android-alkalmazásokban

Az első két lépés csak akkor szükséges, ha egy emulátoron végez tesztelést.

1. Győződjön meg arról, hogy a Google Play-szolgáltatásokkal konfigurált eszközön vagy emulátoron helyezi üzembe vagy végzi a hibakeresést. Ezt ellenőrizheti, ha ellenőrzi, hogy a **Play** -alkalmazások telepítve vannak-e az eszközön vagy az emulátoron.
2. Vegyen fel egy Google-fiókot az Android-eszközre az **alkalmazások** > **Beállítások** > **fiók hozzáadása**lehetőségre kattintva. Ezután kövesse az utasításokat, és adjon hozzá egy meglévő Google-fiókot az eszközhöz, vagy hozzon létre egy újat.
3. A Visual Studióban vagy a Xamarin Studióban kattintson a jobb gombbal a **droid** projektre, majd kattintson a **beállítás indítási projektként**lehetőségre.
4. Kattintson a **Futtatás** gombra a projekt felépítéséhez és az alkalmazás elindításához az Android-eszközön vagy az emulátoron.
5. Az alkalmazásban írjon be egy feladatot, majd kattintson a plusz ( **+** ) ikonra.
6. Ellenőrizze, hogy a rendszer értesítést kap-e az elemek hozzáadásakor.

## <a name="configure-and-run-the-ios-project-optional"></a>Az iOS-projekt konfigurálása és futtatása (nem kötelező)

Ez a szakasz az iOS-eszközökhöz készült Xamarin iOS-projektek futtatásával foglalkozik. Kihagyhatja ezt a részt, ha nem dolgozik iOS-eszközökkel.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>A APNS tartozó értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Ezután konfigurálja az iOS-projekt beállításait a Xamarin Studióban vagy a Visual Studióban.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Leküldéses értesítések hozzáadása iOS-alkalmazáshoz

1. Az **iOS** -projektben nyissa meg a AppDelegate.cs, és adja hozzá a következő utasítást a fájl elejéhez.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. A **AppDelegate** osztályban adjon meg egy felülbírálást a **RegisteredForRemoteNotifications** esemény számára az értesítések regisztrálásához:

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

3. A **AppDelegate**-ben adja hozzá a következő felülbírálást a **DidReceiveRemoteNotification** -eseménykezelőhöz:

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

    Ez a metódus a bejövő értesítéseket kezeli az alkalmazás futása közben.

4. A **AppDelegate** osztályban adja hozzá a következő kódot a **FinishedLaunching** metódushoz:

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

    Ez lehetővé teszi a távoli értesítések támogatását, és leküldéses regisztrációt kér.

Az alkalmazás most már frissítve van a leküldéses értesítések támogatásához.

#### <a name="test-push-notifications-in-your-ios-app"></a>Leküldéses értesítések tesztelése iOS-alkalmazásokban

1. Kattintson a jobb gombbal az iOS-projektre, majd kattintson a **beállítás indítási projektként**lehetőségre.
2. A projekt létrehozásához és az alkalmazás iOS-eszközben való elindításához nyomja le a **Futtatás** gombot vagy az **F5** billentyűt a Visual Studióban. Ezután kattintson **az OK** gombra a leküldéses értesítések fogadásához.

   > [!NOTE]
   > Explicit módon el kell fogadnia a leküldéses értesítéseket az alkalmazásból. Ez a kérelem csak az alkalmazás futásának első indításakor fordul elő.

3. Az alkalmazásban írjon be egy feladatot, majd kattintson a plusz ( **+** ) ikonra.
4. Ellenőrizze, hogy érkezett-e értesítés, majd kattintson az **OK** gombra az értesítés elvetéséhez.

## <a name="configure-and-run-windows-projects-optional"></a>Windows-projektek konfigurálása és futtatása (nem kötelező)

Ez a szakasz a Xamarin. Forms WinApp és a WinPhone81 projektek Windows-eszközökön való futtatására szolgál. Ezek a lépések a Univerzális Windows-platform-(UWP-) projekteket is támogatják. Kihagyhatja ezt a részt, ha nem dolgozik Windows-eszközökkel.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Windows-alkalmazás regisztrálása leküldéses értesítésekhez a Windows Notification Service (WNS) szolgáltatással

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS tartozó értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Leküldéses értesítések hozzáadása a Windows-alkalmazáshoz

1. A Visual Studióban nyissa meg a **app.XAML.cs** egy Windows-projektben, és adja hozzá a következő utasításokat.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Cserélje le a `<your_TodoItemManager_portable_class_namespace>`t a `TodoItemManager` osztályt tartalmazó hordozható projekt névterére.

2. A App.xaml.cs-ben adja hozzá a következő **initnotificationsasync metódusdefiníciót** metódust:

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

    Ez a metódus lekéri a leküldéses értesítési csatornát, és regisztrálja a sablont, hogy fogadja a sablonra vonatkozó értesítéseket az értesítési központban. A rendszer a *messageParam* támogató sablont küldi el az ügyfélnek.

3. A App.xaml.cs-ben frissítse a **OnLaunched** eseménykezelő metódus definícióját a `async`-módosító hozzáadásával. Ezután adja hozzá a következő kódrészletet a metódus végén:

    ```csharp
    await InitNotificationsAsync();
    ```

    Ezzel biztosítható, hogy a leküldéses értesítések regisztrációja az alkalmazás minden indításakor létrejöjjön vagy frissül. Ez azért fontos, mert garantálni kell, hogy a WNS leküldéses csatorna mindig aktív legyen.  

4. Megoldáskezelő a Visual Studióban nyissa meg a **Package. appxmanifest** fájlt, és **állítsa be** az **Igen** lehetőséget az **értesítések**területen.
5. Hozza létre az alkalmazást, és ellenőrizze, hogy nincsenek-e hibák. Az ügyfélalkalmazás most regisztrálnia kell a sablon értesítéseire a Mobile Apps háttérből. Ismételje meg ezt a szakaszt minden Windows-projekt esetében a megoldásban.

#### <a name="test-push-notifications-in-your-windows-app"></a>Leküldéses értesítések tesztelése a Windows-alkalmazásban

1. A Visual Studióban kattintson a jobb gombbal a Windows-projektre, majd kattintson a **beállítás indítási projektként**lehetőségre.
2. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás elindításához.
3. Az alkalmazásban írjon be egy új todoitem nevét, majd kattintson a plusz ( **+** ) ikonra a hozzáadásához.
4. Ellenőrizze, hogy a rendszer értesítést kap-e az adott elemmel kapcsolatban.

## <a name="next-steps"></a>Következő lépések

További információ a leküldéses értesítésekről:

* [Leküldéses értesítések küldése az Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Távoli értesítések a Firebase Cloud Messaging szolgáltatással](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Leküldéses értesítésekkel kapcsolatos problémák diagnosztizálása](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Számos oka lehet annak, hogy az értesítések miért kerülhetnek el vagy nem állnak le az eszközökön. Ez a témakör bemutatja, hogyan elemezheti és derítheti ki a leküldéses értesítések meghibásodásának okát.

A következő oktatóanyagok egyikét is folytathatja:

* [Hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ismerje meg, hogyan hitelesítheti alkalmazása felhasználóit identitásszolgáltató használatával.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálással a felhasználók a Mobile apps szolgáltatással kezelhetik az adat&mdash;t&mdash;megtekinteni, hozzáadni vagy módosítani, még akkor is, ha nincs hálózati kapcsolat.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
