---
title: "Ismerkedés a Notification Hubs Xamarin.Android-alkalmazásokkal való használatával | Microsoft Docs"
description: "Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin Android-alkalmazásokba."
author: jwhitedev
manager: kpiteira
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7f978ecd128115e5f2fe562da46d8b29324e3d04
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Ismerkedés a Notification Hubs Xamarin.Android-alkalmazásokkal való használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin.Android-alkalmazásokba. Létre fog hozni egy üres Xamarin.Android-alkalmazást, amely leküldéses értesítéseket fogad a Firebase Cloud Messaging (FCM) használatával. Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre. A befejezett kód a minta [NotificationHubs alkalmazásban][GitHub] érhető el.

Ez az oktatóanyag az egyszerű küldési forgatókönyvet mutatja be a Notification Hubs használatával.

## <a name="before-you-begin"></a>Előkészületek
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Az oktatóanyag teljes kódja a GitHubon, [itt][GitHub] található meg.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyaghoz az alábbiakra lesz szükség:

* Windows rendszeren [Visual Studio és Xamarin], vagy OS X rendszeren [Visual Studio for Mac ].
* Aktív Google-fiók

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Xamarin.Android-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>A Firebase Cloud Messaging engedélyezése
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Az értesítési központ konfigurálása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Válassza az oldal tetején található <b>Konfigurálás</b> lapot, adja meg az <b>API-kulcs</b> az előző szakaszban beszerzett értékét, majd kattintson a <b>Mentés</b> parancsra.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Az értesítési központ konfigurálva van az FCM-mel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálására értesítések fogadásához és leküldéses értesítések küldéséhez.

## <a name="connect-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz
Először hozzon létre egy új projektet. 

1. A Visual Studióban válassza az **New Solution** (Új megoldás)  > **Android App** (Android-alkalmazás) elemet, majd kattintson a **Next** (Tovább) elemre.
   
      ![Visual Studio – Új Android-projekt létrehozása][22]

2. Adja meg az **App name** (Alkalmazás neve) és az **Identifier** (Azonosító) értékét. Válassza ki a támogatni kívánt **Target Plaforms-** (Célplatformok) értékeit, majd válassza a **Next** (Tovább), majd a **Create** (Létrehozás) elemet.
   
      ![Visual Studio – Android-alkalmazás konfigurációja][23]

    Ezzel létrehoz egy új Android-projektet.

3. Nyissa meg a projekt tulajdonságait a Solution (Megoldás) nézetben a jobb gombbal az új projektre kattintva, és az **Options** (Beállítások) elemet választva. Válassza az **Android Application** (Android-alkalmazás) lehetőséget a **Build** (Felépítés) szakaszban.
   
    Ellenőrizze, hogy a **Package name** (Csomag neve) értéke kisbetűvel kezdődik.
   
   > [!IMPORTANT]
   > A csomagnév első betűjének kisbetűnek kell lennie. Ellenkező esetben az alkalmazásjegyzékkel kapcsolatos hibák lépnek fel, amikor az alábbi lépésben regisztrálja az alkalmazást a leküldéses értesítésekre.
   > 
   > 
   
      ![Visual Studio – Android-projekt beállításai][24]
4. A **Minimum Android version** (Minimális Android-verziót) beállíthatja egy másik API-szintre.
5. A **Target Android version** (Cél Android-verziót) beállíthatja másik cél API-verzióra (legalább 8. szintű API-nak kell lennie).
6. Kattintson az **OK** gombra, és zárja be a Project Options (Projektbeállítások) párbeszédpanelt.

### <a name="add-the-required-packages-to-your-project"></a>A szükséges csomagok hozzáadása a projekthez

1. Kattintson a jobb gombbal a projektre, és válassza a **Hozzáadás** > **NuGet-csomagok hozzáadása** lehetőséget.
2. Keresse meg a **Xamarin.Azure.NotificationHubs.Android** elemet, és adja hozzá a projekthez.
3. Keresse meg a **Xamarin.Firebase.Messaging** elemet, és adja hozzá a projekthez.

### <a name="set-up-notification-hubs-in-your-project"></a>Értesítési központok beállítása a projektben
1. Gyűjtse össze az alábbi információikat az Android-alkalmazásra és az értesítési központra vonatkozóan:
   
   * **Figyelési kapcsolati karakterlánc**: Az [Azure Portalról] irányítópultján válassza a **Kapcsolati karakterláncok megtekintése** elemet. Másolja a *DefaultListenSharedAccessSignature* kapcsolati karakterláncot ezen értékhez.
   * **Központ neve**: Ez a központ neve az [Azure Portalról]. Például: *mynotificationhub2*.
     
2. Hozzon létre egy **Constants.cs** osztályt a Xamarin-projekthez, és definiálja a következő konstans értékeket az osztályban. A helyőrzőket cserélje le az értékekkel.
    
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

6. Kattintson a jobb gombbal a projektre, és adja hozzá a Firebase-projektből korábban letöltött `google-services.json` fájlt. Kattintson a jobb gombbal a hozzáadott fájlra, és az összeállítási művelet beállításaként adja meg a `GoogleServicesJson` értéket

    ![Visual Studio – A google-services.json konfigurálása][25]

7. Hozzon létre egy új, **MyFirebaseIIDService** nevű osztályt.

8. Adja hozzá a következő using utasításokat a **MyFirebaseIIDService.cs** osztályhoz:
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. A **MyFirebaseIIDService** osztályban adja hozzá az alábbiakat az **osztálydeklaráció** feletti részben, és örököltesse az osztállyal a **FirebaseInstanceIdService** tulajdonságait:
   
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
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Firebase.Messaging;
    ```

13. Adja hozzá az alábbiakat az osztálydeklaráció feletti részben, és örököltesse az osztállyal a **FirebaseMessagingService** tulajdonságait:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseIIDService : FirebaseMessagingService
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

15. Az alkalmazás futtatása az eszközén vagy a betöltött emulátoron

## <a name="send-notifications-from-the-portal"></a>Értesítések küldése a portálról
Az [Azure Portalról] *Tesztküldés* lehetőségével tesztelheti az alkalmazásban az értesítések fogadását. Ez egy leküldéses tesztértesítést küld az eszközre.

![Azure Portal – Küldés tesztelése][30]

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. Ha a háttérszolgáltatáshoz nem érhető el kódtár, az értesítések küldéséhez használhatja közvetlenül a REST API-t is.

Az alábbi listában egyéb oktatóprogramok találhatók, amelyek az értesítések küldésével kapcsolatosak:

* ASP.NET: [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára].
* Azure Notification Hubs Java SDK: [How to use Notification Hubs from Java](notification-hubs-java-push-notification-tutorial.md) (A Notification Hubs használata Javából) oktatóprogram bemutatja, hogyan küldhetők értesítések a Javával. Ez az Eclipse-ben lett tesztelve Android-fejlesztéshez.
* PHP: [How to use Notification Hubs from PHP](notification-hubs-php-push-notification-tutorial.md) (A Notification Hubs használata PHP-ből).

## <a name="next-steps"></a>További lépések
Ebben az egyszerű példában értesítéseket küldött az összes Android-eszközre. Ha adott felhasználóknak szeretne értesítést küldeni, tekintse meg [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyagot. Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakört. A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával] és [Notification Hubs Android rendszeren való használatával] foglalkozó témakörben tekinthet meg további információt.

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
[Visual Studio for Mac ]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portalról]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs használatával]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs Android rendszeren való használatával]: http://msdn.microsoft.com/library/dn282661.aspx

[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
