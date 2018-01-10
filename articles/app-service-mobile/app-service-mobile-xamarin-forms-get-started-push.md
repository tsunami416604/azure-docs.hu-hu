---
title: "Leküldéses értesítések hozzáadása a Xamarin.Forms-alkalmazás |} Microsoft Docs"
description: "Útmutató: Azure-szolgáltatások segítségével több platformra leküldéses értesítések küldéséhez a Xamarin.Forms-alkalmazásokra."
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 9c7d56b19a72ec82ff834929790e5049b9369797
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Leküldéses értesítések hozzáadása a Xamarin.Forms-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban leküldéses értesítések hozzáadása a projektek eredményeként a [Xamarin.Forms gyors üzembe helyezési](app-service-mobile-xamarin-forms-get-started.md). Ez azt jelenti, hogy leküldéses értesítés minden platformfüggetlen az ügyfeleknek kiküldött minden alkalommal, amikor egy olyan rekordot csatlakoztatva van.

Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, szüksége lesz a leküldéses értesítési kiterjesztési csomagot. További információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
Az iOS, szüksége lesz egy [Apple Fejlesztőprogrambeli tagság](https://developer.apple.com/programs/ios/) és egy fizikai iOS-eszközön. A [iOS-szimulátorban nem támogatja a leküldéses értesítések](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Frissítés a kiszolgáló projekt leküldéses értesítések küldéséhez
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurálja és futtassa az Android-projektre (nem kötelező)
Ez a szakasz az Android a Xamarin.Forms Droid-projektek a leküldéses értesítések engedélyezéséhez végezze el.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Firebase Cloud Messaging (FCM) engedélyezése
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>A Mobile Apps háttér leküldéses kérések küldése FCM használatával konfigurálása
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Leküldéses értesítések hozzáadása az Android-projekt
A háttérrendszer működésében FCM konfigurált adhat hozzá összetevők és kódok FCM regisztrálni az ügyfelet. A Mobile Apps háttér keresztül az Azure Notification Hubs leküldéses értesítések regisztrálása, és értesítéseket is.

1. Az a **Droid** projektre, kattintson a jobb gombbal **hivatkozások > Manage NuGet Packages...** .
1. A NuGet-Csomagkezelő ablakban keresse meg a **Xamarin.Firebase.Messaging** csomagot, majd adja hozzá a projekthez.
1. A projekt properies a a a **Droid** projektre, állítsa be az alkalmazás fordítása Android 7.0 vagy újabb verzió használatával.
1. Adja hozzá a **google-services.json** gyökérmappájában Firebase konzoljában letöltött fájlok a **Droid** projektre, és állítsa be a létrehozási művelet **GoogleServicesJson**. További információkért lásd: [adja hozzá a Google szolgáltatás JSON-fájl](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Regisztrálás az Firebase Cloud Messaging

1. Nyissa meg a **AndroidManifest.xml** fájlt, és illessze be a következő `<receiver>` az elemeket a `<application>` elem:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>A Firebase példány azonosítója szolgáltatás megvalósítása

1. Az új osztályt a **Droid** nevű projekt `FirebaseRegistrationService`, és győződjön meg arról, hogy a következő `using` utasításokat a fájl elején szerepelnek:

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. Cserélje le az üres `FirebaseRegistrationService` osztály az alábbi kódra:

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

    A `FirebaseRegistrationService` osztály biztonsági jogkivonatokat, amelyek az alkalmazás eléréséhez FCM létrehozásáért felelős. A `OnTokenRefresh` metódus nyílik meg, ha az alkalmazás egy regisztrációs jogkivonatot kap FCM. A metódus lekéri a jogkivonatot a `FirebaseInstanceId.Instance.Token` tulajdonságot, amelynek FCM aszinkron módon frissül. A `OnTokenRefresh` metódus ritkán meghívták, mert a jogkivonat csak akkor frissül, amikor az alkalmazás telepítésének vagy eltávolításának, amikor a felhasználó törli az adatokat, amikor az alkalmazás törli az azonosítója, vagy ha a biztonsági jogkivonat lett sérülés esetén. Emellett a FCM Példányazonosító szolgáltatás kérni fogja, hogy az alkalmazás frissíti a token rendszeres időközönként, általában minden hatodik hónapban.

    A `OnTokenRefresh` metódus Ezenfelül meghívja a `SendRegistrationTokenToAzureNotificationHub` metódus, amely a felhasználói regisztrációs jogkivonat társítása az Azure Notification Hub használatával.

#### <a name="registering-with-the-azure-notification-hub"></a>Az Azure Notification Hub regisztrálása

1. Az új osztályt a **Droid** nevű projekt `AzureNotificationHubService`, és győződjön meg arról, hogy a következő `using` utasításokat a fájl elején szerepelnek:

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. Cserélje le az üres `AzureNotificationHubService` osztály az alábbi kódra:

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

    A `RegisterAsync` hoz létre egy egyszerű értesítési üzenet sablon JSON-ban, és regiszterekben sablon értesítések fogadása az értesítési központnak a Firebase regisztrációs jogkivonat használatával. Ez biztosítja, hogy az Azure Notification Hub küldött értesítések által megcélzott az eszköz a regisztrációs jogkivonat képviseli.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Leküldéses értesítés tartalmának megjelenítése

1. Az új osztályt a **Droid** nevű projekt `FirebaseNotificationService`, és győződjön meg arról, hogy a következő `using` utasításokat a fájl elején szerepelnek:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Util;
        using Firebase.Messaging;

1. Cserélje le az üres `FirebaseNotificationService` osztály az alábbi kódra:

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

                var notificationBuilder = new Notification.Builder(this)
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


    A `OnMessageReceived` módszert, amelyet ha egy alkalmazás FCM értesítést kap, az üzenet a tartalom kibontása, és meghívja a `SendNotification` metódust. Ez a módszer az üzenet tartalma alakítja át, amely nincs elindítva, az alkalmazás futása közben, az értesítési területen megjelenő értesítéssel helyi értesítést.

Most már áll készen áll a teszt leküldéses értesítések Android-eszközön vagy az emulátor futnak az alkalmazásban.

### <a name="test-push-notifications-in-your-android-app"></a>Teszt leküldéses értesítések Android-alkalmazás
Az első két lépés csak egy emulátorának tesztelést esetén szükséges.

1. Győződjön meg arról, üzembe vagy hibakeresés egy virtuális eszközön, amely rendelkezik a Google API-k, célként beállítva, az Android virtuális eszközt kezelőjét alább látható módon vannak.
2. A Google-fiók kattintva vegyen fel új Android-eszköz **alkalmazások** > **beállítások** > **fiók hozzáadása**. Kövesse az utasításokat egy meglévő Google-fiók hozzáadása az eszközt, vagy hozzon létre egy újat.
3. A Visual Studio és Xamarin Studióban, kattintson a jobb gombbal a **Droid** projektre, kattintson **beállítás kezdőprojektként**.
4. Kattintson a **futtatása** a projekt felépítéséhez és az Android-eszköz vagy az emulátor indítsa el az alkalmazást.
5. Az alkalmazásban írjon be egy feladatot, és kattintson a plusz (**+**) ikonra.
6. Győződjön meg arról, hogy értesítés érkezik, amikor egy elem hozzá van adva.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurálja és futtassa az iOS-projektre (nem kötelező)
Ez a szakasz az iOS-eszközökhöz készült Xamarin iOS-projektek futtatásával foglalkozik. Kihagyhatja ezt a részt, ha nem dolgozik iOS-eszközökkel.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Az értesítési központ konfigurálása az APN Szolgáltatáshoz
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Az iOS-projekt beállítása a következő konfigurál a Xamarin Studióban vagy a Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Leküldéses értesítések az iOS-alkalmazás hozzáadása
1. Az a **iOS** projektre, nyissa meg a AppDelegate.cs, és adja hozzá a következő utasítás a kód fájl elejéhez.

        using Newtonsoft.Json.Linq;
2. Az a **AppDelegate** osztály, vegyen fel egy felülbírálást a **RegisteredForRemoteNotifications** esemény értesítések regisztrálása:

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
3. A **AppDelegate**, a következő felülbírálást is hozzáadhat a **DidReceiveRemoteNotification** eseménykezelő:

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

    Ez a metódus kezeli a bejövő értesítések az alkalmazás futtatása közben.
4. Az a **AppDelegate** osztály, adja hozzá a következő kódot a **FinishedLaunching** módszert:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Ez lehetővé teszi a távoli értesítések támogatása, és a kérelmek leküldéses regisztrálása.

Az alkalmazás most frissíteni leküldéses értesítések támogatásához használható.

#### <a name="test-push-notifications-in-your-ios-app"></a>Teszt leküldéses értesítéseket az iOS-alkalmazás
1. Kattintson a jobb gombbal az iOS-projektre, és kattintson a **beállítás kezdőprojektként**.
2. Nyomja meg a **futtatása** gomb vagy **F5** a projekt felépítéséhez és az alkalmazás elindításához az iOS-eszközök Visual Studio. Kattintson a **OK** leküldéses értesítések fogadásához.

   > [!NOTE]
   > Az alkalmazásból explicit módon el kell fogadnia a leküldéses értesítések. A kérelem csak akkor történik meg az első alkalommal futtatja az alkalmazást.
   >
   >
3. Az alkalmazásban írjon be egy feladatot, és kattintson a plusz (**+**) ikonra.
4. Győződjön meg arról, hogy értesítést kap, és kattintson **OK** az értesítés bezárásának engedélyezése.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurálja és futtassa a Windows-projektek (nem kötelező)
Ez a szakasz a a Xamarin.Forms WinApp és a Windows-eszközök WinPhone81 projektek futtatására szolgál. Ezeket a lépéseket is támogatja az univerzális Windows Platform (UWP) projektek. Kihagyhatja ezt a részt, ha nem dolgozik Windows-eszközökkel.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Alkalmazás regisztrálása a Windows leküldéses értesítéseket a Windows értesítési szolgáltatása (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS az értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Leküldéses értesítések hozzáadása a Windows-alkalmazás
1. A Visual Studióban nyissa meg a **App.xaml.cs** Windows projektre, és adja hozzá az alábbi utasításokat.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Cserélje le `<your_TodoItemManager_portable_class_namespace>` rájuk a névtérrel, a hordozható projekt, amely tartalmazza a `TodoItemManager` osztály.
2. App.xaml.cs fájlban adja hozzá a következő **InitNotificationsAsync** módszert:

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

    Ez a módszer lekérdezi a leküldéses értesítési csatornát, és regisztrál az értesítési központ sablon értesítések fogadása sablon. Egy sablon értesítés, amely támogatja a *messageParam* kézbesíti a rendszer az ügyfélnek.
3. App.xaml.cs fájlban frissítse a **OnLaunched** esemény kezelő metódusdefiníciót hozzáadásával a `async` módosítóval. Majd adja hozzá a következő kódsort a metódus végén:

        await InitNotificationsAsync();

    Ez biztosítja, hogy a leküldéses értesítési regisztrációban létrehozásakor vagy minden alkalommal frissíteni az alkalmazást elindítja. Fontos ezzel garantálható, hogy a WNS leküldéses csatorna mindig aktív.  
4. A Visual Studio Solution Explorerben nyissa meg a **Package.appxmanifest** fájlt, és állítsa be **bejelentési képes** való **Igen** alatt **értesítések**.
5. Az alkalmazás elkészítésére, és ellenőrizze, rendelkezik-e hibák. Az ügyfélalkalmazás most regisztrálni kell a sablon értesítések a Mobile Apps háttérből. Ismételje meg minden Windows-projektet a megoldásban ez a szakasz.

#### <a name="test-push-notifications-in-your-windows-app"></a>Teszt leküldéses értesítések a Windows-alkalmazásokban
1. A Visual Studióban, kattintson a jobb gombbal egy Windows-projektet, majd kattintson **beállítás kezdőprojektként**.
2. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás elindításához.
3. Az alkalmazásban írjon be egy nevet az új beállíthatnánk, és kattintson a plusz (**+**) ikonra kattintva vegye fel azt.
4. Győződjön meg arról, hogy értesítést kapott, a cikk felvételekor.

## <a name="next-steps"></a>További lépések
További tudnivalók leküldéses értesítések:

* [Leküldéses értesítések küldése az Azure Mobile Apps szolgáltatásban](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Távoli értesítések küldése a Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Leküldéses értesítési eseményadatokat](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Oka különböző miért kerülhetnek vagy értesítések nem végül az eszközökön. Ez a témakör bemutatja, hogyan elemezheti és mérje fel, az alapvető ok leküldéses értesítés sikertelen.

Is továbbra is valamelyik további anyagra:

* [Hitelesítés hozzáadása az alkalmazáshoz ](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. Kapcsolat nélküli szinkronizálás, a felhasználók használhatják a mobilalkalmazás&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
