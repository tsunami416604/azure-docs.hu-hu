---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 1dc491084f65bc90397b0897de6b6cfe4f2fd410
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448770"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Az info. plist és a jogosultságok konfigurálása. plist

1. Győződjön meg arról, hogy bejelentkezett az **Apple Developer-fiókjába** a **Visual Studio**  >  **beállításaiban..**  >  . **Közzététel**  >  folyamatban Az **Apple fejlesztői fiókjai** és a megfelelő *tanúsítvány* -és *létesítési profil* letöltése megtörtént. Ezeket az eszközöket az előző lépések részeként kell létrehoznia.

1. A **PushDemo. iOS**fájlban nyissa meg az **info. plist** fájlt, és ellenőrizze, hogy a **BundleIdentifier** megegyezik-e az [Apple Developer Portal](https://developer.apple.com)megfelelő létesítési profiljához használt értékkel. A **BundleIdentifier** formátuma volt ``com.<organization>.PushDemo`` .

1. Ugyanebben a fájlban állítsa be a **minimális rendszerverziót** **13,0**-re.

    > [!NOTE]
    > Ebben az oktatóanyagban csak az **iOS 13,0-es vagy újabb** verzióját futtató eszközök támogatottak, de kiterjesztheti a régebbi verziókat futtató eszközök támogatására is.

1. Nyissa meg a **PushDemo. iOS** **projekt beállításait** (kattintson duplán a projektre).

1. A **projekt beállításai**között, a **Build > iOS-köteg aláírása**területen győződjön meg arról, hogy a fejlesztői fiókja ki van választva a **csapat**területen. Ezután győződjön meg arról, hogy az "aláírás automatikus kezelése" lehetőség be van jelölve, és az aláíró tanúsítvány és a létesítési profil automatikusan ki van választva.

    > [!NOTE]
    > Ha az *aláíró tanúsítvány* és *létesítési profil* nem lett automatikusan kiválasztva, válassza a **manuális kiépítés**lehetőséget, majd kattintson a **köteg-aláírási beállítások**elemre. Győződjön meg arról, hogy a *csapat* van kiválasztva az **aláírási identitáshoz** , és a *PushDemo* -specifikus létesítési profil ki van választva a **kiépítési profilhoz** mind a **hibakeresési** , mind a **kiadási** konfigurációk esetében, hogy mindkét esetben a **platformhoz** **iPhone** legyen kiválasztva.

1. A **PushDemo. iOS**-ben nyissa meg a **jogosultságok. plist** fájlt, és ellenőrizze, hogy a **jogosultságok** lapon meg van-e jelölve a **leküldéses értesítések engedélyezése** jelölőnégyzet. Ezt követően győződjön meg arról, hogy az **APS-környezet** beállítás a **forrás** lapon megtekintett **fejlesztésre** van beállítva.

### <a name="handle-push-notifications-for-ios"></a>Leküldéses értesítések kezelése iOS-hez

1. **Vezérlő**  +  **Kattintson** a **PushDemo. iOS** projektre, válassza **az új mappa** lehetőséget a **Hozzáadás** menüben, majd kattintson a **Hozzáadás** a *szolgáltatások* használatával **mappa neveként**elemre.

1. **Vezérlő**  +  **Kattintson** a **szolgáltatások** mappára, majd válassza az **új fájl...** lehetőséget a **Hozzáadás** menüből.

1. Válassza az **általános**  >  **üres osztály**lehetőséget, írja be a *DeviceInstallationService.cs* **nevet**, majd kattintson az **új** alkalmazás hozzáadása elemre.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály egyedi azonosítót (az [UIDevice. IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) értéket) és az értesítési központ regisztrációs adattartalmát tartalmazza.

1. Vegyen fel egy új mappát a **PushDemo. iOS** -projekt *kiterjesztések* nevű mappájába, majd adjon hozzá egy **üres osztályt** a *NSDataExtensions.cs* nevű mappához a következő implementációval.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. A **AppDelegate.cs**-ben ellenőrizze, hogy a következő névterek hozzá lettek-e adva a fájl elejéhez.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. A **IPushDemoNotificationActionService**, a **INotificationRegistrationService**és a **IDeviceInstallationService** megvalósítására vonatkozó hivatkozásokat a saját tulajdonságok és a hozzájuk tartozó mezők hozzáadásával tárolhatja.

    ```csharp
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
    ```

1. Adja hozzá a **RegisterForRemoteNotifications** metódust a felhasználói értesítési beállítások regisztrálásához, majd a **APNS**-sel folytatott távoli értesítésekhez.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Adja hozzá a **CompleteRegistrationAsync** metódust a `IDeviceInstallationService.Token` tulajdonság értékének megadásához. Frissítse a regisztrációt, és gyorsítótárazza az eszköz jogkivonatát, ha az a legutóbbi tárolás óta frissült.

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. Adja hozzá a **ProcessNotificationActions** metódust a **NSDictionary** értesítési adatainak feldolgozásához és a **NotificationActionService. TriggerAction**feltételes meghívásához.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Bírálja felül a **RegisteredForRemoteNotifications** metódust, amely a **deviceToken** argumentumot átadja a **CompleteRegistrationAsync** metódusnak.

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Bírálja felül a **ReceivedRemoteNotification** metódust, amely a **userInfo** argumentumot átadja a **ProcessNotificationActions** metódusnak.

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. A hiba naplózásához bírálja felül a **FailedToRegisterForRemoteNotifications** metódust.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > Ez nagyon sok helyőrző. Az éles környezetekben megfelelő naplózási és hibakezelés-kezelést szeretne megvalósítani.

1. Frissítse a **FinishedLaunching** metódust, hogy `Bootstrap.Begin` közvetlenül a hívás után hívja meg a `Forms.Init` **IDeviceInstallationService**-t a platform-specifikus implementációban.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. Ugyanebben a metódusban a feltételes kérelem engedélyezését és a távoli értesítések regisztrálását követően azonnal regisztrálja magát `Bootstrap.Begin` .

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. Továbbra is a **FinishedLaunching**-ben, közvetlenül a hívása után hívja meg a **ProcessNotificationActions** , `LoadApplication` Ha a **Options** argumentum tartalmazza a **UIApplication. LaunchOptionsRemoteNotificationKey** , amely a létrejövő **userInfo** objektumban halad.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
