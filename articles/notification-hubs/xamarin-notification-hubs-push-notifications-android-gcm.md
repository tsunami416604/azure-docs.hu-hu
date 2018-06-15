---
title: Leküldéses értesítések küldése Xamarin.Android-alkalmazásokba az Azure Notification Hubs használatával | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin Android-alkalmazásokba.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776712"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése Xamarin.Android-alkalmazásokba az Azure Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin.Android-alkalmazásokba. Létre fog hozni egy üres Xamarin.Android-alkalmazást, amely leküldéses értesítéseket fogad a Firebase Cloud Messaging (FCM) használatával. Az értesítési központ használatával fog leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre. A befejezett kód a minta [NotificationHubs alkalmazásban][GitHub] érhető el.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Egy Firebase-projekt létrehozása és a Firebase Cloud Messaging engedélyezése
> * Értesítési központ létrehozása
> * Egy Xamarin.Android-alkalmazás létrehozása és csatlakoztatása az értesítési központhoz
> * Tesztértesítések küldése az Azure Portalról

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Windows rendszeren [Visual Studio és Xamarin], vagy OS X rendszeren [Visual Studio for Mac].
- Aktív Google-fiók

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Egy Firebase-projekt létrehozása és a Firebase Cloud Messaging engedélyezése
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Az értesítési központ GCM-beállításainak konfigurálása

1. Válassza a **Google (GCM)** lehetőséget a **NOTIFICATION SETTINGS** (Értesítési beállítások) részen. 
2. Adja meg a korábban a Google Firebase konzoljáról feljegyezett **Legacy server key** (Régi kiszolgálókulcs) értékét. 
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Az értesítési központ konfigurálva van az FCM-mel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálására értesítések fogadásához és leküldéses értesítések küldéséhez.

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>Egy Xamarin.Android-alkalmazás létrehozása és csatlakoztatása az értesítési központhoz

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Egy Visual Studio-projekt létrehozása és NuGet-csomagok hozzáadása
1. A Visual Studióban mutasson a **File** (Fájl) menüre, és válassza a **New** (Új), majd a **Project** (Projekt) elemet. 
   
      ![Visual Studio – Új Android-projekt létrehozása](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. A **Solution Explorer** (Megoldáskezelő) ablakában bontsa ki a **Properties** (Tulajdonságok) elemet, majd kattintson az **AndroidManifest.xml** fájlra. Frissítse a csomag nevét arra a csomagnévre, amelyet akkor adott meg, amikor hozzáadta a Firebase Cloud Messaginget a projekthez a Google Firebase konzolján. 

      ![Csomag neve a GCM-ben](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Kattintson a jobb gombbal a projektre, és válassza a **Manage Nuget Packages…** (NuGet-csomagok kezelése) lehetőséget. 
4. Válassza ki a **Browse** (Tallózás) lapot. Keressen rá a **Xamarin.GooglePlayServices.Base** kifejezésre. A találatok listájában válassza a **Xamarin.GooglePlayServices.Base** elemet. Ezt követően válassza az **Install** (Telepítés) parancsot. 

      ![Google Play-szolgáltatások NuGet-csomagjai](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban keressen rá a **Xamarin.Firebase.Messaging** kifejezésre. A találatok listájában válassza a **Xamarin.Firebase.Messaging** elemet. Ezt követően válassza az **Install** (Telepítés) parancsot. 
6. Ezután keressen rá a **Xamarin.Azure.NotificationHubs.Android** kifejezésre. A találatok listájában válassza a **Xamarin.Azure.NotificationHubs.Android** elemet. Ezt követően válassza az **Install** (Telepítés) parancsot. 

### <a name="add-the-google-services-json-file"></a>A Google-szolgáltatás JSON-fájljának hozzáadása

1. Másolja a Google Firebase konzoljáról letöltött **google-services.json** fájlt a projektmappába.
2. Adja hozzá a **google-services.json** fájlt a projekthez.
3. A **Solution Explorer** (Megoldáskezelő) ablakában válassza ki a **google-services.json** fájlt.
4. A **Properties** (Tulajdonságok) panelen a Build Action (Felépítési művelet) számára állítsa be a **GoogleServicesJson** értéket. Ha nem látja a **GoogleServicesJson** műveletet, zárja be a Visual Studiót, indítsa el újra, nyissa meg újra a projektet, és próbálkozzon újból. 

      ![GoogleServicesJson felépítési művelet](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Értesítési központok beállítása a projektben

#### <a name="registering-with-firebase-cloud-messaging"></a>Regisztráció a Firebase Cloud Messagingben

Nyissa meg az **AndroidManifest.xml** fájlt, és illessze a következő `<receiver>` elemeket a `<application>` elembe:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Gyűjtse össze az alábbi információikat az Android-alkalmazásra és az értesítési központra vonatkozóan:
   
   * **Figyelési kapcsolati karakterlánc**: Az [Azure Portalon] irányítópultján válassza a **Kapcsolati karakterláncok megtekintése** elemet. Másolja a *DefaultListenSharedAccessSignature* kapcsolati karakterláncot ezen értékhez.
   * **Központ neve**: A központ neve az [Azure Portalon]. Például: *mynotificationhub2*.
     
2. A **Solution Explorer** (Megoldáskezelő) ablakában kattintson a jobb gombbal a **project** (projekt) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson a **Class** (Osztály) lehetőségre. 
4. Hozzon létre egy **Constants.cs** osztályt a Xamarin-projekthez, és definiálja a következő konstans értékeket az osztályban. A helyőrzőket cserélje le az értékekkel.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. Adja hozzá a következő using utasításokat a **MainActivity.cs** osztályhoz:
   
    ```csharp
        using Android.Util;
    ```
4. Adjon hozzá egy példányváltozót a **MainActivity.cs** osztályhoz, amelyet egy figyelmeztető párbeszédpanel megjelenítésére használ a rendszer az alkalmazás futása során:
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. A **MainActivity.cs** osztályban adja hozzá az alábbi kódot az `OnCreate` elemhez a `base.OnCreate(savedInstanceState)` után:

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```
7. Hozzon létre egy új, **MyFirebaseIIDService** nevű osztályt ugyanúgy, ahogy a **Constants** osztályt létrehozta. 
8. Adja hozzá a következő using utasításokat a **MyFirebaseIIDService.cs** osztályhoz:
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. A **MyFirebaseIIDService** osztályban adja hozzá az alábbi **osztálydeklarációt**, és örököltesse az osztállyal a **FirebaseInstanceIdService** tulajdonságait:
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. Adja hozzá a következő kódot a **MyFirebaseIIDService.cs** osztályhoz:
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
11. Hozzon létre egy másik új osztályt a projekthez **MyFirebaseMessagingService** néven.
12. Adja hozzá a következő using utasításokat a **MyFirebaseMessagingService.cs** osztályhoz.
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. Adja hozzá az alábbiakat az osztálydeklaráció feletti részben, és örököltesse az osztállyal a **FirebaseMessagingService** tulajdonságait:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. Adja hozzá a következő kódot a **MyFirebaseMessagingService.cs** osztályhoz:
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **Hozza létre** a projektet. 
16. **Futtassa** az alkalmazást az eszközén vagy a betöltött emulátoron.

## <a name="send-test-notification-from-the-azure-portal"></a>Tesztértesítés küldése az Azure Portalról
Az [Azure Portalon] *Tesztküldés* lehetőségével tesztelheti az alkalmazásban az értesítések fogadását. A parancs egy leküldéses tesztértesítést küld az eszközre.

![Azure Portal – Küldés tesztelése](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. Ha a háttérszolgáltatáshoz nem érhető el kódtár, az értesítések küldéséhez használhatja közvetlenül a REST API-t is.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban szórásos értesítéseket küldött a háttérrendszerben regisztrált Android-eszközök mindegyikének. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott Android-eszközökre, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio és Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portalon]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
