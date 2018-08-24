---
title: Leküldéses értesítések hozzáadása a Xamarin.Forms-alkalmazás |} A Microsoft Docs
description: Ismerje meg, hogyan többplatformos leküldéses értesítések küldése a Xamarin.Forms-alkalmazások Azure-szolgáltatások használatával.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 2658dcb0d206086aad1443e2dc720b6f1f55906f
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818158"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>A Xamarin.Forms-alkalmazás leküldéses értesítések hozzáadása

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a leküldéses értesítések hozzáadása a projektek eredményeként a [Xamarin.Forms gyors üzembe helyezési](app-service-mobile-xamarin-forms-get-started.md). Ez azt jelenti, hogy egy leküldéses értesítés küldése platformok közötti összes ügyfélre, minden alkalommal, amikor a rendszer beszúr egy rekordot.

Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, szüksége lesz a leküldéses értesítési kiterjesztési csomag. További információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

IOS-, szüksége lesz egy [Apple Fejlesztőprogrambeli tagság](https://developer.apple.com/programs/ios/) és a egy fizikai iOS-eszközt. A [az iOS-szimulátor nem támogatja a leküldéses értesítések](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Leküldéses értesítések küldéséhez a kiszolgálói projekt frissítése

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurálja és futtassa az Android-projektre (nem kötelező)

Végezze el az ebben a szakaszban engedélyezze a leküldéses értesítések az Android a Xamarin.Forms Droid projektet.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Firebase Cloud Messaging (FCM) engedélyezése

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>A Mobile Apps-háttéralkalmazás leküldéses kérelmek küldése a FCM használatával konfigurálja

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Leküldéses értesítések hozzáadása az Android-projekt

A háttérrendszer az fcm-mel konfigurált hozzáadhat összetevők és a kódok, az ügyfél az fcm-mel regisztrálni. A Mobile Apps-háttéralkalmazás segítségével az Azure Notification Hubs leküldéses értesítések regisztráljon, és értesítések fogadása is.

1. Az a **Droid** projektre, kattintson a jobb gombbal **hivatkozások > NuGet-csomagok kezelése...** .
1. A NuGet-Csomagkezelő ablakban keresse meg a **Xamarin.Firebase.Messaging** csomagot, és adja hozzá a projekthez.
1. A projekt tulajdonságai között a **Droid** projektre, állítsa be az alkalmazás fordítása Android 7.0-s vagy újabb verzió használatával.
1. Adja hozzá a **google-services.json** gyökere a Firebase konzoljában a letöltött fájl, a **Droid** projektre, és állítsa a létrehozási művelet **GoogleServicesJson**. További információkért lásd: [adja hozzá a Google Services JSON-fájl](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

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

#### <a name="implementing-the-firebase-instance-id-service"></a>A Firebase Példányazonosító-szolgáltatás megvalósítása

1. Adjon hozzá egy új osztályt a **Droid** nevű projekt `FirebaseRegistrationService`, és ellenőrizze, hogy a következő `using` utasítások a fájl tetején találhatók:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Cserélje le az üres `FirebaseRegistrationService` osztályban az alábbi kódra:

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

    A `FirebaseRegistrationService` osztály biztonsági jogkivonatokat, engedélyezze az alkalmazás eléréséhez FCM létrehozásáért felelős. A `OnTokenRefresh` metódus meghívásakor kerül, amikor az alkalmazás fogad FCM-regisztrációs jogkivonatot. A metódus lekér a jogkivonatot a `FirebaseInstanceId.Instance.Token` tulajdonság, amely FCM aszinkron módon frissül. A `OnTokenRefresh` metódus ritkán hív, mert a jogkivonat csak akkor frissül, amikor az alkalmazás telepítésének vagy eltávolításának, amikor a felhasználó törli az alkalmazásadatok esetében, amikor az alkalmazás törli a példány Azonosítóját, vagy ha a biztonsági jogkivonat lett sérült a biztonsága. Emellett az FCM Példányazonosító-szolgáltatás kérni fogja, hogy az alkalmazás frissíti a tokent rendszeres időközönként, általában minden hatodik hónapban.

    A `OnTokenRefresh` metódus is meghívja a `SendRegistrationTokenToAzureNotificationHub` metódussal, amely a felhasználói regisztráció token társítása az Azure Notification Hub szolgál.

#### <a name="registering-with-the-azure-notification-hub"></a>Az Azure értesítési központon történő regisztrációt

1. Adjon hozzá egy új osztályt a **Droid** nevű projekt `AzureNotificationHubService`, és ellenőrizze, hogy a következő `using` utasítások a fájl tetején találhatók:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Cserélje le az üres `AzureNotificationHubService` osztályban az alábbi kódra:

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

    A `RegisterAsync` metódus az egyszerű értesítési üzenet sablonjának JSON, és regisztrál a sablon értesítéseket fogadjon az értesítési központ használatával a Firebase-regisztrációs jogkivonatot hoz létre. Ez biztosítja, hogy az Azure Notification Hub által küldött értesítések fogják használni az eszköz a regisztrációs jogkivonat képviseli.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Leküldéses értesítés tartalmának megjelenítése

1. Adjon hozzá egy új osztályt a **Droid** nevű projekt `FirebaseNotificationService`, és ellenőrizze, hogy a következő `using` utasítások a fájl tetején találhatók:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Cserélje le az üres `FirebaseNotificationService` osztályban az alábbi kódra:

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

    A `OnMessageReceived` módszer, amelyek akkor aktiválódnak, ha egy alkalmazás értesítést kap az FCM, kinyeri a tartalom az üzenetet, és meghívja az `SendNotification` metódust. Ezzel a módszerrel alakítja indul el, amíg az alkalmazás fut, az értesítést az értesítési területen jelennek meg a helyi értesítést az üzenet tartalma.

Most Ön készen teszt leküldéses értesítések Android-eszközön vagy az emulátor, az alkalmazásban.

### <a name="test-push-notifications-in-your-android-app"></a>Teszt leküldéses értesítések az Android-alkalmazás található

Az első két lépés elengedhetetlen, csak akkor, amikor az emulátor tesztelt.

1. Győződjön meg arról, hogy üzembe helyezése vagy egy eszközt vagy emulátort, amely a Google Play-szolgáltatások van beállítva a hibakereséshez. Ez úgy, hogy ellenőrizhető a **lejátszása** alkalmazások a eszközt vagy emulátort telepítve vannak.
2. Google-fiók hozzáadása az Android-eszközre kattintva **alkalmazások** > **beállítások** > **fiók hozzáadása**. Ezután kövesse az utasításokat, vegyen fel egy meglévő Google-fiókot az eszközön, vagy hozzon létre egy újat.
3. A Visual Studióban vagy a Xamarin Studióban, kattintson a jobb gombbal a **Droid** projektre, és kattintson a **beállítás kezdőprojektként**.
4. Kattintson a **futtatása** a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást az Android-eszköz vagy az emulátoron.
5. Az alkalmazásban írjon be egy feladatot, és kattintson a plusz (**+**) ikonra.
6. Győződjön meg arról, hogy értesítés érkezik, amikor új elem kerül.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurálja és futtassa az iOS-projektre (nem kötelező)

Ez a szakasz az iOS-eszközökhöz készült Xamarin iOS-projektek futtatásával foglalkozik. Kihagyhatja ezt a részt, ha nem dolgozik iOS-eszközökkel.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Az értesítési központ konfigurálása az APN Szolgáltatáshoz

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Ezután konfigurálhatja az iOS-projekt beállítása a Xamarin Studio vagy a Visual Studióban.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Leküldéses értesítések hozzáadása az iOS-alkalmazás

1. Az a **iOS** projekt, nyissa meg a AppDelegate.cs, és adja hozzá a következő utasítást a kód fájl elejéhez.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Az a **AppDelegate** osztály, vegyen fel egy felülbírálást a **RegisteredForRemoteNotifications** értesítések regisztrálása esemény:

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

3. A **AppDelegate**, a következő felülbírálást is hozzáadhat a **DidReceiveRemoteNotification** eseménykezelő:

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

    Ez a metódus kezeli a bejövő értesítések az alkalmazás futása közben.

4. Az a **AppDelegate** osztályhoz, adja hozzá a következő kódot a **FinishedLaunching** módszer:

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

    Ez lehetővé teszi a távoli értesítések támogatását, és a kérések leküldéses regisztrálása.

Az alkalmazás most már frissül, és támogatja a leküldéses értesítéseket.

#### <a name="test-push-notifications-in-your-ios-app"></a>Teszt leküldéses értesítéseket az iOS-alkalmazás

1. Kattintson a jobb gombbal az iOS-projektre, és kattintson a **Set as StartUp Project**.
2. Nyomja le az **futtatása** gomb vagy **F5** a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást az iOS-eszközök a Visual studióban. Kattintson a **OK** leküldéses értesítések fogadásához.

   > [!NOTE]
   > Az alkalmazásból explicit módon el kell fogadnia a leküldéses értesítések. Ezt a kérelmet csak akkor történik meg, amely az alkalmazás első alkalommal.

3. Az alkalmazásban írjon be egy feladatot, és kattintson a plusz (**+**) ikonra.
4. Győződjön meg arról, hogy értesítést kap, és kattintson **OK** az értesítés elvetéséhez.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurálja és futtassa a Windows-projektek (nem kötelező)

Ez a szakasz olyan fut a Xamarin.Forms WinApp és WinPhone81 projektek Windows-eszközökhöz. Ezeket a lépéseket is támogatja az univerzális Windows Platform (UWP) projekteket. Kihagyhatja ezt a részt, ha nem dolgozik Windows-eszközökkel.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>A Windows értesítési szolgáltatása (WNS) leküldéses értesítések Windows alkalmazás regisztrálása

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS az értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Leküldéses értesítések hozzáadása Windows-alkalmazáshoz

1. A Visual Studióban nyissa meg a **App.xaml.cs** a Windows a projekthez, és adja hozzá az alábbi utasításokat.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Cserélje le `<your_TodoItemManager_portable_class_namespace>` a névtérhez a hordozható projekt, amely tartalmazza a `TodoItemManager` osztály.

2. Az App.XAML.cs fájlban adja hozzá a következő **InitNotificationsAsync** módszer:

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

    Ez a módszer a leküldéses értesítési csatorna beolvasása, és regisztrálja a sablon értesítéseket fogadjon az értesítési központ egy olyan sablont. A sablon értesítést, amely támogatja a *messageParam* kézbesíti a rendszer ennek az ügyfélnek.

3. Az App.XAML.cs fájlban frissítse a **OnLaunched** esemény kezelő metódusdefiníciót hozzáadásával a `async` módosítót. Ezután adja hozzá a következő kódsort a metódus végén:

    ```csharp
    await InitNotificationsAsync();
    ```

    Ez biztosítja, hogy a leküldéses értesítési regisztrációban jön létre vagy frissül minden alkalommal, amikor az alkalmazás elindítása. Fontos ezzel garantálja, hogy a WNS leküldéses csatorna mindig aktív.  

4. A Visual Studio Megoldáskezelőben nyissa meg a **Package.appxmanifest** fájlt, és állítsa be **bejelentési képes** való **Igen** alatt **értesítések**.
5. Az alkalmazás létrehozása, és győződjön meg arról, hogy nincsenek hibák. Az ügyfélalkalmazás most regisztráljanak a sablon értesítések a Mobile Apps háttéralkalmazásból. Ismételje meg minden Windows-projektet a megoldásban ez a szakasz.

#### <a name="test-push-notifications-in-your-windows-app"></a>Teszt leküldéses értesítéseket a Windows-alkalmazás

1. A Visual Studióban kattintson a jobb gombbal egy Windows-projektet, és kattintson a **beállítás kezdőprojektként**.
2. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás elindításához.
3. Az alkalmazásban írjon be egy nevet az új beállíthatnánk, és kattintson a plusz (**+**) ikonra kattintva adja hozzá.
4. Győződjön meg arról, hogy értesítés érkezik, az elem hozzáadásakor.

## <a name="next-steps"></a>További lépések

Leküldéses értesítések többet tudhat meg:

* [Leküldéses értesítések küldése az Azure Mobile Appsban](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Távoli értesítések küldése a Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Leküldéses értesítés problémák diagnosztizálása](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Nincsenek különböző okok miatt, miért kerülhetnek vagy értesítések nem végül az eszközökön. Ez a témakör bemutatja, hogyan elemezheti, és döntse el, a leküldéses értesítési hibák okának.

Emellett továbbra is be a következő oktatóanyagok egyikét:

* [Hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. Offline szinkronizálás, a felhasználók kezelhessék a mobilalkalmazás&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
