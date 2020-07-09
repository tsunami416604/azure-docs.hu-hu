---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: de961aa36d690cf03e42707758bc70e5495f692e
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448710"
---
### <a name="validate-package-name-and-permissions"></a>A csomag nevének és engedélyeinek ellenőrzése

1. A **PushDemo. Android** **alkalmazásban** nyissa meg a **projekt beállításait** , majd az Android-alkalmazást a **Build** szakaszban.

1. Győződjön meg arról, hogy a **csomag neve** megegyezik a Firebase- [konzol](https://console.firebase.google.com) **PushDemo** projektben használt értékkel. A **csomag nevének** formátuma ``com.<organization>.pushdemo`` .

1. Állítsa be a **minimális Android-verziót** az **Android 8,0 (API-szint 26)** értékre, és a **cél Android-verziót** a legújabb API- **szintre**.

    > [!NOTE]
    > Ebben az oktatóanyagban csak a **26. és újabb API-** t futtató eszközök támogatottak, de kiterjesztheti a régebbi verziókat futtató eszközök támogatására is.

1. Győződjön meg arról, hogy az **Internet** és a **READ_PHONE_STATE** engedélyek engedélyezve vannak a **szükséges engedélyekkel**.

1. Kattintson **az OK** gombra

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Adja hozzá a Xamarin Google Play Services alap és Xamarin. Firebase. Messaging csomagokat.

1. A **PushDemo. Android**eszközben **Control**  +  **kattintson** a **csomagok** mappára, majd válassza a **NuGet-csomagok kezelése...** lehetőséget.

1. Keresse meg a **Xamarin. GooglePlayServices. Base** (nem **alagsor**) kifejezést, és ellenőrizze, hogy be van-e jelölve.

1. Keresse meg a **Xamarin. Firebase. Messaging** kifejezést, és ellenőrizze, hogy be van-e jelölve.

1. Kattintson a **csomagok hozzáadása**lehetőségre, majd az **elfogadás** gombra, amikor a rendszer kéri, hogy fogadja el a **licencfeltételeket**.

### <a name="add-the-google-services-json-file"></a>A Google Services JSON-fájljának hozzáadása

1. **Vezérlő**  +  **Kattintson** a `PushDemo.Android` projektre, majd a **Hozzáadás** menüben válassza a **meglévő fájl...** lehetőséget.

1. Válassza ki a [Firebase-konzolon](https://console.firebase.google.com) a **PushDemo** -projekt beállításakor korábban letöltött fájl *google-services.jsét* , majd kattintson a **Megnyitás**gombra.

1. Ha a rendszer kéri, válassza a **fájl másolását a könyvtárba**.

1. **Vezérlő**  +  Kattintson a fájl *google-services.jsa* projekten belül **lehetőségre** `PushDemo.Android` , és győződjön meg arról, hogy a **GoogleServicesJson** beállítása **Build művelet**.

### <a name="handle-push-notifications-for-android"></a>Leküldéses értesítések kezelése Android rendszerhez

1. **Vezérlő**  +  **Kattintson** a `PushDemo.Android` projektre, válassza **az új mappa** lehetőséget a **Hozzáadás** menüben, majd kattintson a **Hozzáadás** a *szolgáltatások* használatával **mappa neveként**elemre.

1. **Vezérlő**  +  **Kattintson** a **szolgáltatások** mappára, majd válassza az **új fájl...** lehetőséget a **Hozzáadás** menüből.

1. Válassza az **általános**  >  **üres osztály**lehetőséget, írja be a *DeviceInstallationService.cs* **nevet**, majd kattintson az **új** alkalmazás hozzáadása elemre.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for FCM");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály egyedi azonosítót biztosít (a [Secure. AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)használatával) az értesítési központ regisztrációs adatforgalmának részeként.

1. Adjon hozzá egy másik **üres osztályt** a *PushNotificationFirebaseMessagingService.cs*nevű **szolgáltatások** mappához, majd adja hozzá a következő implementációt.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. A **MainActivity.cs**-ben ellenőrizze, hogy a következő névterek hozzá lettek-e adva a fájl elejéhez.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. A **MainActivity.cs**-ben állítsa be a **LaunchMode** a **SingleTop** -be, hogy a **MainActivity** ne kerüljön újra létre a megnyitásakor.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Adja hozzá a privát tulajdonságokat és a megfelelő biztonsági mezőket a **IPushNotificationActionService** -és **IDeviceInstallationService** -implementációk hivatkozásának tárolására.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. A **IOnSuccessListener** felület implementálása az **Firebase** -jogkivonat lekéréséhez és tárolásához.

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Adjon hozzá egy **ProcessNotificationActions** nevű új metódust, amely azt vizsgálja, hogy egy adott **szándéknak** van-e extra értékű *művelete*. A művelet feltételes elindítása a **IPushDemoNotificationActionService** implementációjának használatával.

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Bírálja felül a **OnNewIntent** metódust a **ProcessNotificationActions** metódus meghívásához.

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Mivel a **tevékenység** **LaunchMode** a **SingleTop**értékre van állítva, a **rendszer a** **OnCreate** metódus helyett a **OnNewIntent** metódussal küldi el a meglévő **tevékenység** példányát, ezért a **OnCreate** és a **OnNewIntent** metódusokban is be kell állítania a bejövő szándékot.

1. Frissítse a **OnCreate** metódust, hogy `Bootstrap.Begin` közvetlenül a hívás után hívja meg a `base.OnCreate` **IDeviceInstallationService**-t a platform-specifikus implementációban.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. Ugyanebben a metódusban a **FirebaseApp** -példány **GetInstanceId** feltételeit, közvetlenül a hívása után hívja meg a `Bootstrap.Begin` **MainActivity** -t a **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. A **OnCreate**továbbra is azonnal hívja meg a **ProcessNotificationActions** , miután megtörtént a hívás az `LoadApplication` aktuális **szándékba**való átadásra.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> Minden egyes futtatásakor újra regisztrálnia kell az alkalmazást, és le kell állítania egy hibakeresési munkamenetből, hogy továbbra is megkapja a leküldéses értesítéseket.
