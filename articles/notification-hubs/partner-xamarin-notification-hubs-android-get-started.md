<properties
    pageTitle="Ismerkedés az Azure Notification Hubs Xamarin.Android-alkalmazásokkal való használatával | Microsoft Azure"
    description="Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin Android-alkalmazásokba."
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="02/16/2016"
    ms.author="wesmc"/>

# Ismerkedés a Notification Hubs Xamarin Android-alkalmazásokkal való használatával

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Áttekintés

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin.Android-alkalmazásokba.
Létre fog hozni egy üres Xamarin.Android-alkalmazást, amely leküldéses értesítéseket fogad a Google Cloud Messaging (GCM) használatával. Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre. A befejezett kód a minta [NotificationHubs-alkalmazásban][GitHub] érhető el.

Ez az oktatóanyag az egyszerű küldési forgatókönyvet mutatja be a Notification Hubs használatával.


## Előkészületek

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Az oktatóanyag teljes kódja a GitHubon, [itt](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid) található meg.



##Előfeltételek

Az oktatóanyaghoz az alábbiakra lesz szükség:

+ Windows rendszeren Visual Studio with Xamarin, vagy Mac OS X rendszeren Xamarin Studio. A teljes telepítési útmutatás itt található: [Setup and Install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése).
+ Aktív Google-fiók
+ [Azure Messaging összetevő]
+ [Google Cloud Messaging Client összetevő]

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Xamarin.Android-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

> [AZURE.IMPORTANT] Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##A Google Cloud Messaging engedélyezése

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Az értesítési központ konfigurálása

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Kattintson az oldal tetején található <b>Konfigurálás</b> fülre, adja meg az <b>API-kulcs</b> előző szakaszban beszerzett értékét, majd kattintson a <b>Mentés</b> gombra.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


Az értesítési központ konfigurálva lett a GCM-mel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálására értesítések fogadásához és leküldéses értesítések küldéséhez.

##Az alkalmazás csatlakoztatása az értesítési központhoz

###Új projekt létrehozása

1. A Xamarin Studióban kattintson a **New Solution** (Új megoldás), az **Android App** (Android-alkalmazás), majd a **Next** (Tovább) elemre.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Adja meg az **App name** (Alkalmazás neve) és az **Identifier** (Azonosító) értékét. Kattintson a támogatni kívánt **Target Plaforms** (Célplaformok) elemekre, majd a **Next** (Tovább), végül a **Create** (Létrehozás) gombra.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    Ezzel létrehoz egy új Android-projektet.

2. Nyissa meg a projekt tulajdonságait a Solution (Megoldás) nézetben a jobb gombbal az új projektre kattintva, és az **Options** (Beállítások) elemet választva. Válassza az **Android Application** (Android-alkalmazás) lehetőséget a **Build** (Felépítés) szakaszban.

    Ellenőrizze, hogy a **Package name** (Csomag neve) értéke kisbetűvel kezdődik.

    > [AZURE.IMPORTANT] A csomagnév első betűjének kisbetűnek kell lennie. Különben az alkalmazásjegyzékkel kapcsolatos hibák lépnek fel a **BroadcastReceiver** és az **IntentFilter** leküldéses értesítésekre való alábbi regisztrálása során.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. A **Minimum Android version** (Minimális Android-verziót) beállíthatja egy másik API-szintre.

4. A **Target Android version** (Cél Android-verziót) beállíthatja másik cél API-verzióra (legalább 8. szintű API-nak kell lennie).

Kattintson az **OK** gombra, és zárja be a Project Options (Projektbeállítások) párbeszédpanelt.


###A szükséges összetevők hozzáadása a projekthez

A Xamarin Component Store áruházban elérhető Google Cloud Messaging Client egyszerűbbé teszi a leküldéses értesítések támogatását a Xamarin.Androidban.

1. Kattintson a jobb gombbal a Components (Összetevők) mappára a Xamarin.Android-alkalmazásban, és válassza a **Get More Components** (További összetevők beszerzése) lehetőséget.

2. Keresse meg az **Azure Messaging** összetevőt, és adja hozzá a projekthez.

3. Keresse meg a **Google Cloud Messaging Client** összetevőt, és adja hozzá a projekthez.


###Értesítési központok beállítása a projektben

1. Gyűjtse össze az alábbi információikat az Android-alkalmazásra és az értesítési központra vonatkozóan:

    - **GoogleProjectNumber** (Google-projektszám): A projektszám értéke az alkalmazás áttekintésében található Google fejlesztői portálján. Korábban, az alkalmazásnak a portálon való létrehozása során már feljegyezte ezt az értéket.
    - **Listen connection string** (Figyelési kapcsolati karakterlánc): A [klasszikus Azure portál] irányítópultján kattintson a **View connection strings** (Kapcsolati karakterláncok megtekintése) parancsra. Másolja a *DefaultListenSharedAccessSignature* kapcsolati karakterláncot ezen értékhez.
    - **Huba name** (Központ neve): A központ neve a [klasszikus Azure portál]. Például: *mynotificationhub2*.

    Hozzon létre egy **Constants.cs** osztályt a Xamarin-projekthez, és definiálja a következő konstans értékeket az osztályban. A helyőrzőket cserélje le az értékekkel.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Adja hozzá a következő using utasításokat a **MainActivity.cs** osztályhoz:

        using Android.Util;
        using Gcm.Client;

3. Adjon hozzá egy példányváltozót a `MainActivity` osztályhoz, amelyet egy figyelmeztető párbeszédpanel megjelenítésére használ a rendszer az alkalmazás futása során:

        public static MainActivity instance;


3. Hozza létre a következő metódust a **MainActivity** osztályban:

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. A **MainActivity.cs** `OnCreate` metódusában inicializálja az `instance` változót, és adjon hozzá egy `RegisterWithGCM`-hívást:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Hozzon létre az új **MyBroadcastReceiver** osztályt.

    > [AZURE.NOTE] Alább végigvezetjük a **BroadcastReceiver** osztály létrehozásának folyamatán az alapoktól kezdve. A **MyBroadcastReceiver.cs** manuális létrehozásának egy gyors alternatívája lehet azonban, ha a **GcmService.cs** fájlt használja, amely a Xamarin.Android-mintaprojektben található a [NotificationHubs-mintákban][GitHub]. A **GcmService.cs** másolása, majd az osztálynevek módosítása szintén remek kiindulási pont lehet.

5. Adja hozzá a következő using utasításokat a **MyBroadcastReceiver.cs** osztályhoz (a korábban hozzáadott összetevőre és szerelvényre hivatkozva):

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. A **MyBroadcastReceiver.cs** osztályban adja hozzá a következő engedélykéréseket a **using** utasítások és a **namespace** deklaráció között:

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. A **MyBroadcastReceiver.cs** osztályban módosítsa a **MyBroadcastReceiver** osztályt, hogy egyezzen az alábbiakkal:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. A **MyBroadcastReceiver.cs** osztályban adjon hozzá egy másik, **PushHandlerService** nevű osztályt, amely a **GcmServiceBase** osztályból származik. Alkalmazza a **Service** attribútumot az osztályra:

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. A **GcmServiceBase** az **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** és **OnError()** metódust valósítja meg. A **PushHandlerService** megvalósítási osztálynak felül kell bírálnia ezeket a metódusokat, és a metódusok az értesítési központtal való interakcióra válaszként aktiválódnak.


9. Bírálja felül az **OnRegistered()** metódust a **PushHandlerService** osztályban az alábbi kód használatával:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] A fenti **OnRegistered()** kódban láthatja azt a lehetőséget, amellyel adott üzenetkezelési csatornák számára regisztrálható címkék adhatók meg.

10. Bírálja felül az **OnMessage** metódust a **PushHandlerService** osztályban az alábbi kód használatával:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Adja hozzá a következő **createNotification** és **dialogNotify** metódust a **PushHandlerService** osztályhoz a felhasználók értesítésére értesítés fogadásakor.

    >[AZURE.NOTE] Az Android 5.0-s és újabb verzióiban az értesítések kialakítása jelentősen eltér a korábbi verzióktól. Ha a tesztelést az Android 5.0-s vagy újabb verziójában végzi, az alkalmazásnak futnia kell az értesítés fogadásához. További információ: [Android-értesítések](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Bírálja felül az **OnUnRegistered()**, **OnRecoverableError()** és **OnError()** absztrakt tagot a kód lefordításához:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##Az alkalmazás futtatása az emulátorban

Ha az emulátorban futtatja az alkalmazást, olyan Android virtuális eszközt (AVD) használjon, amely támogatja a Google API-kat.

> [AZURE.IMPORTANT] Leküldéses értesítések fogadásához be kell állítania egy Google-fiókot az Android virtuális eszközön. (Az emulátorban lépjen a **Settings** (Beállítások) részre, és kattintson az **Add Account** (Fiók hozzáadása) parancsra.) Továbbá ellenőrizze, hogy az emulátor csatlakozik-e az internethez.

>[AZURE.NOTE] Az Android 5.0-s és újabb verzióiban az értesítések kialakítása jelentősen eltér a korábbi verzióktól. További információ: [Android-értesítések](http://go.microsoft.com/fwlink/?LinkId=615880).


1. A **Tools** (Eszközök) részen kattintson az **Open Android Emulator Manager** (Android-emulátorkezelő megnyitása) elemre, jelölje ki az eszközt, majd kattintson az **Edit** (Szerkesztés) gombra.

    ![][18]

2. A **Target** (Cél) értékeként válassza a **Google APIs** (Google API-k) lehetőséget, majd kattintson az **OK** gombra.

    ![][19]

3. A felső eszköztáron kattintson a **Run** (Futtatás) elemre, majd jelölje ki az alkalmazást. Ekkor elindul az emulátor, és futtatja az alkalmazást.

  Az alkalmazás lekéri a *registrationId* azonosítót a GCM-ből, és regisztrál az értesítési központban.

##Értesítések küldése a háttérrendszerből


Az értesítések fogadásának az alkalmazásban való teszteléséhez értesítéseket küldhet a [klasszikus Azure portál] az értesítési központ hibakeresési lapján keresztül, az alábbi képernyőn látható módon.

![][30]


A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. Az értesítések küldéséhez használhatja közvetlenül a REST API-t is, ha a háttérszolgáltatáshoz nem érhető el kódtár.

Az alábbi listában egyéb oktatóprogramok találhatók, amelyek az értesítések küldésével kapcsolatosak:

- ASP.NET: [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára].
- Azure Notification Hubs Java SDK: [How to use Notification Hubs from Java](notification-hubs-java-backend-how-to.md) (A Notification Hubs használata Javából) oktatóprogram bemutatja, hogyan küldhetők értesítések a Javával. Ez az Eclipse-ben lett tesztelve Android-fejlesztéshez.
- PHP: [How to use Notification Hubs from PHP](notification-hubs-php-backend-how-to.md) (A Notification Hubs használata PHP-ből).


Az oktatóanyag következő alszakaszaiban értesítéseket küld egy .NET-konzolalkalmazás, illetve egy mobilszolgáltatás használatával egy csomópontparancsfájl segítségével.

####(Választható) Értesítések küldése .NET-alkalmazás használatával

Ebben a szakaszban egy .NET-konzolalkalmazás használatával küldünk értesítéseket.

1. Hozzon létre egy új Visual C#-konzolalkalmazást:

    ![][20]

2. A Visual Studióban kattintson az **Eszközök**, a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Package Manager Console** (Csomagkezelő konzol) elemre.

    Megjelenik a Package Manager Console (Csomagkezelő konzol) ablak a Visual Studióban.

3. A Package Manager Console (Csomagkezelő konzol) ablakban az **Alapértelmezett projekt** értékeként adja meg az új konzolalkalmazás-projektet, majd a konzolablakban hajtsa végre az alábbi parancsot:

        Install-Package Microsoft.Azure.NotificationHubs

    Ezzel hozzáad a <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-csomagot</a> használó Azure Notification Hubs SDK-ra mutató hivatkozást.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Nyissa meg a Program.cs fájlt, majd adja hozzá a következő `using` utasítást:

        using Microsoft.Azure.NotificationHubs;

5. A `Program` osztályban adja hozzá a következő metódust. Cserélje a helyőrzők szövegét a *DefaultFullSharedAccessSignature* kapcsolati karakterlánccal és a központnak a [klasszikus Azure portál] látható nevével.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Adja hozzá a következő sorokat a **Main** metódushoz:

         SendNotificationAsync();
         Console.ReadLine();

7. Nyomja le az F5 billentyűt az alkalmazás futtatásához. Egy értesítést kell kapnia az alkalmazásban.

    ![][21]

####(Választható) Értesítések küldése mobilszolgáltatás használatával

1. Kövesse [A Mobile Services használatának első lépései] című témakör utasításait.

1. Jelentkezzen be a [klasszikus Azure portál], majd jelölje ki a mobilszolgáltatást.

2. Válassza az oldal tetején található **Scheduler** fület.

    ![][22]

3. Hozzon létre egy új ütemezett feladatot, szúrjon be egy nevet, és válassza az **On demand** (Igény szerint) lehetőséget.

    ![][23]

4. A feladat létrehozását követően kattintson a feladat nevére. Ezután válassza a felső sávon található **Script** (Parancsfájl) fület.

5. Szúrja be a következő parancsfájlt a Scheduler függvényébe. Cserélje le a helyőrzőket az értesítési központ nevére és a *DefaultFullSharedAccessSignature* kapcsolati karakterláncra, amelyet korábban szerzett be. Kattintson a **Save** (Mentés) gombra.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Kattintson az alsó sáv **Run Once** (Futtatás egyszer) parancsára. Egy bejelentési értesítést kell kapnia.

##Következő lépések

Ebben az egyszerű példában értesítéseket küldött az összes Android-eszközre. Ha adott felhasználóknak szeretne értesítést küldeni, tekintse meg [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyagot. Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakört. A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával] és [Notification Hubs Android rendszeren való használatával] foglalkozó témakörben tekinthet meg további információt.

<!-- Anchors. -->
[A Google Cloud Messaging engedélyezése]: #register
[Az értesítési központ konfigurálása]: #configure-hub
[Az alkalmazás csatlakoztatása az értesítési központhoz]: #connecting-app
[Az alkalmazás futtatása az emulátorral]: #run-app
[Értesítések küldése a háttérrendszerből]: #send
[Következő lépések]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Alkalmazáslap elküldése]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Saját alkalmazások]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[A Mobile Services használatának első lépései]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript és HTML]: /develop/mobile/tutorials/get-started-with-push-js

[klasszikus Azure portál]: https://manage.windowsazure.com/
[wns-objektum]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs használatával]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs útmutató Androidhoz]: http://msdn.microsoft.com/library/dn282661.aspx

[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: /manage/services/notification-hubs/notify-users-aspnet
[A legfrissebb hírek elküldése a Notification Hubs használatával]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient összetevő lapja]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub-lap]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Google Cloud Messaging Client összetevő]: http://components.xamarin.com/view/GCMClient/
[Azure Messaging összetevő]: http://components.xamarin.com/view/azure-messaging



<!--HONumber=Jun16_HO2-->


