---
title: "Leküldéses értesítések hozzáadása a Xamarin.Forms-alkalmazás |} Microsoft Docs"
description: "Útmutató: Azure-szolgáltatások segítségével több platformra leküldéses értesítések küldéséhez a Xamarin.Forms-alkalmazásokra."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: 912367636f1b26b3b07fbd5fe3fe8ed053218fd5
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
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

1. Az a **Droid** projektre, kattintson a jobb gombbal a **összetevők** mappára, majd kattintson **több összetevők beolvasása...** . Majd keresse meg a **Google Cloud Messaging Client** összetevő, és adja hozzá a projekthez. Ez az összetevő a Xamarin Android-projekt leküldéses értesítések támogat.
2. Nyissa meg a MainActivity.cs projektfájlt, és a fájl elejéhez adja hozzá a következő utasítást:

        using Gcm.Client;
3. Adja hozzá a következő kódot a **OnCreate** metódus hívása után **LoadApplication**:

        try
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }
4. Adjon hozzá egy új **CreateAndShowDialog** segítő módszert az alábbiak szerint:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. Adja hozzá a következő kódot a **MainActivity** osztály:

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Ez mutatja az aktuális **MainActivity** példány, ezért a fő felhasználói felület szálán végezhetünk.
6. Inicializálni a `instance` változó elején a **OnCreate** módszert az alábbiak szerint.

        // Set the current instance of MainActivity.
        instance = this;
7. Új osztály fájl hozzáadásához a **Droid** nevű projekt `GcmService.cs`, és győződjön meg arról, hogy a következő **használatával** utasításokat a fájl elején szerepelnek:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;
8. A következő engedélykéréseket tetején található a fájl hozzáadása után a **használatával** utasítások és előtt a **névtér** nyilatkozatot.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. A következő osztálydefiníció hozzáadása a névtérhez.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > Cserélje le **< PROJECT_NUMBER >** a projekt számát, amelyet korábban feljegyzett.    
   >
   >
10. Cserélje le az üres **GcmService** osztály a következő kóddal, amely az új szórásos receiver használja:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. Adja hozzá a következő kódot a **GcmService** osztály. A rendszer felülírja a **OnRegistered** eseménykezelő és megvalósít egy **regisztrálása** metódust.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

    Vegye figyelembe, hogy ezt a kódot használja a `messageParam` paraméter a sablon regisztrációhoz.
12. Az alábbi kódot, amely megvalósítja az **OnMessage**:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Ez a bejövő értesítések kezeli, és elküldi azokat a notification manager megjeleníteni.
13. **GcmServiceBase** is szükséges, hogy implementálja a **OnUnRegistered** és **hibára** kezelő módszerrel, amely a következőképpen teheti:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

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

## <a name="next-steps"></a>Következő lépések
További tudnivalók leküldéses értesítések:

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
