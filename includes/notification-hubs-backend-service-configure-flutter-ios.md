---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147794"
---
### <a name="configure-the-runner-target-and-infoplist"></a>A Runner cél és info. plist fájl konfigurálása

1. A **Visual Studio Code**- **Control**ban  +  **kattintson** a Control ( **iOS** ) mappára, majd válassza a **Megnyitás a Xcode**lehetőséget.

1. A **Xcode**-ben kattintson a **Runner** (a felül található **xcodeproj** elemre, nem a mappára), majd válassza ki a **futó** célt, majd **aláírja & képességeit**. Az **összes** kiépített konfiguráció kiválasztásával válassza ki a **csapat**fejlesztői fiókját. Győződjön meg arról, hogy az "aláírás automatikus kezelése" beállítás be van jelölve, és az aláíró tanúsítvány és a létesítési profil automatikusan ki van választva.

    > [!NOTE]
    > Ha nem látja az új létesítési profil értékét, próbálja meg frissíteni a profilokat az aláíró identitáshoz a **Xcode**beállítások fiók kiválasztásával,  >  **Preferences**  >  **Account** majd a profilok letöltéséhez kattintson a **manuális profilok letöltése** gombra.

1. Kattintson a **+ képesség**lehetőségre, majd keressen **leküldéses értesítéseket**. A funkció hozzáadásához **kattintson duplán** a **leküldéses értesítések** elemre.

1. Nyissa meg az **info. plist** fájlt, és állítsa be a **minimális rendszerverziót** **13,0**-re.

    > [!NOTE]
    > Ebben az oktatóanyagban csak az **iOS 13,0-es vagy újabb** verzióját futtató eszközök támogatottak, de kiterjesztheti a régebbi verziókat futtató eszközök támogatására is.

1. Nyissa meg a **Runner. jogosultságokat** , és győződjön meg arról, hogy az **APS-környezet** beállítása **fejlesztésre**van beállítva.

### <a name="handle-push-notifications-for-ios"></a>Leküldéses értesítések kezelése iOS-hez

1. **Vezérlő**  +  **Kattintson** a **Runner** mappára (a futó projekten belül), majd válassza az **új csoport** lehetőséget a **szolgáltatások** használata néven.

1. **Vezérlő**  +  **Kattintson** a **szolgáltatások** mappára, majd válassza az **új fájl**... lehetőséget. Ezután válassza a **Swift-fájl** lehetőséget, majd kattintson a **tovább**gombra. Adja meg a **DeviceInstallationService** nevet, majd kattintson a **Létrehozás**gombra.

1. Implementálja a **DeviceInstallationService. Swift** -t az alábbi kód használatával.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály megvalósítja a platform-specifikus partnert a `com.<your_organization>.pushdemo/deviceinstallation` csatornához. Ez az alkalmazás a **DeviceInstallationService. DART**-ban található, a dobogón belüli részében lett meghatározva. Ebben az esetben a rendszer a közös kódból érkező hívásokat a natív gazdagépre hívja. Ne felejtse el lecserélni **<your_organization>** a saját szervezetére, bárhol is legyenek.
    >
    > Ez az osztály egyedi azonosítót biztosít (a [UIDevice. identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) érték használatával) az értesítési központ regisztrációs adatforgalmának részeként.

1. Adjon hozzá egy újabb **Swift-fájlt** a *NotificationRegistrationService*nevű **Services** mappához, majd adja hozzá a következő kódot.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály megvalósítja a platform-specifikus partnert a `com.<your_organization>.pushdemo/notificationregistration` csatornához. Ez az alkalmazás a **NotificationRegistrationService. DART**-ban található, a dobogón belüli részében lett meghatározva. Ebben az esetben a rendszer a natív gazdagépről érkező hívásokat a közös kódra kéri. Még egyszer ügyeljen arra, hogy a **<your_organization>** a saját szervezete helyére cserélje le, bárhol is legyen használva.

1. Adjon hozzá egy újabb **Swift-fájlt** a *NotificationActionService*nevű **Services** mappához, majd adja hozzá a következő kódot.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály megvalósítja a platform-specifikus partnert a `com.<your_organization>.pushdemo/notificationaction` csatornához. Ez az alkalmazás a **NotificationActionService. DART**-on belüli, a feltört részében lett meghatározva. Ebben az esetben a hívásokat mindkét irányban lehet elvégezni. Ne felejtse el lecserélni **<your_organization>** a saját szervezetére, bárhol is legyenek.

1. A **AppDelegate. Swift**-ben adjon hozzá változókat a korábban létrehozott szolgáltatásokra mutató hivatkozás tárolására.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Adjon hozzá egy **processNotificationActions** nevű függvényt az értesítési adat feldolgozásához. A művelet feltételes elindítása vagy tárolása későbbi használatra, ha a művelet feldolgozás alatt áll az alkalmazás indításakor.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Bírálja felül a **didRegisterForRemoteNotificationsWithDeviceToken** függvényt a **DeviceInstallationService** **jogkivonat** -értékének beállításával. Ezután hívja meg a **refreshRegistration** a **NotificationRegistrationService**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Bírálja felül a **didReceiveRemoteNotification** függvényt, amely a **userInfo** argumentumot átadja a **processNotificationActions** függvénynek.

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. A hiba naplózásához bírálja felül a **didFailToRegisterForRemoteNotificationsWithError** függvényt.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > Ez nagyon sok helyőrző. Az éles környezetekben megfelelő naplózási és hibakezelés-kezelést szeretne megvalósítani.

1. A **didFinishLaunchingWithOptions**-ben hozza létre a **deviceInstallationService**, a **notificationRegistrationService**és a **notificationActionService** változókat.

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. Ugyanebben a függvényben feltételesen kérheti az engedélyezést, és regisztrálja a távoli értesítéseket.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Ha a **launchOptions** tartalmazza a **remoteNotification** kulcsot, a **didFinishLaunchingWithOptions** függvény végén hívja meg a **processNotificationActions** . Adja át az eredményül kapott **userInfo** objektumot, és használja az *igaz* értéket a *launchAction* argumentumhoz. A *true* érték azt jelzi, hogy a művelet feldolgozása folyamatban van az alkalmazás indításakor.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
