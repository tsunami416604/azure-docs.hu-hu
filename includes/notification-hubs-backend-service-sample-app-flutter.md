---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147805"
---
### <a name="create-the-flutter-solution"></a>A dobogó megoldás létrehozása

1. Nyisson meg egy új **Visual Studio Code**-példányt.

1. Nyissa meg a **parancs-palettát** (**SHIFT**  +  **parancs**  +  **P**).

1. Válassza ki a **dobogó: új projekt** parancsot, majd nyomja le az **ENTER**billentyűt.

1. Adja meg *push_demo* a **projekt nevét** , majd válassza ki a **projekt helyét**.

1. Ha a rendszer erre kéri, válassza a **csomagok beolvasása**lehetőséget.

1. **Vezérlő**  +  **Kattintson** a **Kotlin** mappára (az **app**  >  **src**  >  **Main**alatt), majd válassza a **Megjelenítés a Finderben**lehetőséget. Ezt követően nevezze át a gyermek-mappákat (a **Kotlin** mappában) a, és a () értékre ```com``` ```<your_organization>``` ```pushdemo``` .

    > [!NOTE]
    > A **Visual Studio Code** sablon használatakor a mappák alapértelmezett értéke a **com**, **például** **<project_name>**. Feltételezve, hogy a **mobcat** a **szervezet**számára van használatban, a indicatively a következőképpen kell megjelennie:
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. A **Visual Studio Code**-ban frissítse a **applicationId** értéket az **Android**  >  **app**  >  **Build. gradle** `com.<your_organization>.pushdemo` .

    > [!NOTE]
    > A *<your_organization>* helyőrzőhöz a saját szervezet nevét kell használnia. Például a **mobcat** használata esetén a szervezet a *com. mobcat. pushdemo* **csomag nevének** értékét fogja eredményezni.

1. Frissítse a **Package** attribútumot a **AndroidManifest.xml** fájlokban, a **src**  >  **Debug**, **src**  >  **Main**és **src**  >  **profil** alatt. Győződjön meg arról, hogy az értékek megegyeznek az előző lépésben használt **applicationId** .

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Frissítse az ```android:label``` attribútumot a **AndroidManifest.xml** fájlban a **src**  >  **Main** *PushDemo*. Ezután adja hozzá az ```android:allowBackup``` attribútumot közvetlenül a ```android:label``` elemnél, amely **hamis**értékre állítja az értékét.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Nyissa meg az App-Level **Build. gradle** fájlt (**Android**-  >  **alkalmazás**  >  **Build. gradle**), majd frissítse a *compileSdkVersion* (az **Android** szakaszban) az API **29**használatára. Ezután frissítse a *minSdkVersion* és a *targetSdkVersion* értékeket (a **defaultConfig** szakaszból), és **26** és **29** között.

    > [!NOTE]
    > Ebben az oktatóanyagban csak a **26. és újabb API-** t futtató eszközök támogatottak, de kiterjesztheti a régebbi verziókat futtató eszközök támogatására is.

1. **Vezérlő**  +  **Kattintson** az **iOS** mappára, majd válassza a **Megnyitás a Xcode**lehetőséget.

1. A **Xcode**-ben kattintson a **Runner** (a **xcodeproj** felül, nem pedig a mappában) elemre. Ezután válassza ki a **futó** célt, és válassza az **általános** lapot. Az **összes** kiépített konfiguráció beállításnál frissítse a **köteg azonosítóját** a következőre: `com.<your_organization>.PushDemo` .

    > [!NOTE]
    > A *<your_organization>* helyőrzőhöz a saját szervezet nevét kell használnia. Például a **mobcat** használata esetén a szervezet a *com. Mobcat. PushDemo* **köteg-azonosító** értékét fogja eredményezni.

1. Kattintson az **info. plist** elemre, majd frissítse a **köteg neve** értéket a **PushDemo** értékre.

1. A **Xcode** bezárásához és a **Visual Studio Code**-hoz való visszatéréshez.

1. A **Visual Studio Code**-ban nyissa meg a **pubspec. YAML**, és adja hozzá a [http](https://pub.dev/packages/http) -és [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) **DART-csomagokat** függőségként. Ezután mentse a fájlt, és kattintson a **csomagok beolvasása** elemre, amikor a rendszer erre kéri.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. A **terminálban**váltson át a könyvtárat az **iOS** -mappára (a dobogó projekthez). Ezután hajtsa végre a [**Pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) parancsot az új hüvelyek telepítéséhez (a [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) csomag szükséges).

1. **Vezérlő**  +  **Kattintson** a **lib** mappára, majd válassza az **új fájl** lehetőséget a menüből a *main_page. DART* használatával. Ezután adja hozzá a következő kódot.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. A **Main. dartban**cserélje le a sablonban szereplő kódot a következőre.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. A **Terminal**-ben az alkalmazás minden egyes megcélzott platformon kiépíthető és futtatható, hogy tesztelje a sablonban lévő alkalmazást az eszköz (ök) on. Győződjön meg arról, hogy a támogatott eszközök csatlakoztatva vannak.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>A platformok közötti összetevők implementálása

1. **Vezérlő**  +  **Kattintson** a **lib** mappára, majd válassza a menü **új mappa** parancsát a *modellek* **mappa neveként**.

1. **Vezérlő**  +  **Kattintson** a **modellek** mappára, majd válassza az **új fájl** lehetőséget a menüből a *device_installation. DART* használatával. Ezután adja hozzá a következő kódot.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Adjon hozzá egy új fájlt a *push_demo_action. DART* nevű **models** mappához, amely meghatározza az ebben a példában támogatott műveletek enumerálását.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Vegyen fel egy új mappát a **szolgáltatások** nevű projektbe, majd adjon hozzá egy új fájlt a *device_installation_service. DART* nevű mappához a következő implementációval.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > A *<your_organization>* helyőrzőhöz a saját szervezet nevét kell használnia. Ha például a **mobcat** -t használja, akkor a szervezet a *com. mobcat. Pushdemo/deviceinstallation* [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) nevét fogja eredményezni.
    >
    > Ez az osztály a mögöttes natív platformmal együttműködik a szükséges eszköz telepítési részleteinek megvásárlásához. A [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) lehetővé teszi a kétirányú aszinkron kommunikációt a mögöttes natív platformokkal. A Channel-specifikus munkaállomást a rendszer a későbbi lépésekben hozza létre.

1. Adjon hozzá egy másik fájlt a *notification_action_service. DART* nevű mappához a következő implementációval.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Ez egy egyszerű mechanizmus, amellyel központilag kezelhető az értesítési műveletek kezelése, így azok több platformon is kezelhetők, erősen gépelt enumerálással. A szolgáltatás lehetővé teszi, hogy az alapul szolgáló natív platform egy műveletet indítson el, ha az értesítési adattartalomban meg van adva. Azt is lehetővé teszi, hogy az általános kód visszamenőlegesen is meggyőződjön arról, hogy az alkalmazás indításakor meg lett-e adva egy művelet, ha készen áll a feldolgozásra. Ha például az alkalmazás elindul, koppintson az értesítési központ értesítésére.

1. Vegyen fel egy új fájlt a *notification_registration_service. DART* nevű **Services** mappába a következő implementációval.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Ez az osztály a **DeviceInstallationService** és a háttér-szolgáltatáshoz intézett kérések használatával végzi el a szükséges regisztrációt, a regisztráció megszüntetését és frissítését. A **apiKey** argumentum csak akkor szükséges, ha úgy dönt, hogy a [hitelesítő ÜGYFELEKet az API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) hajtja végre.

1. Adjon hozzá egy új fájlt a *config. DART* nevű **lib** -mappához a következő implementációval.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Ezt egyszerű módszerként használjuk az alkalmazás-titkok definiálásához. Cserélje le a helyőrző értékeket saját értékre. Ezeket a háttér-szolgáltatás létrehozásakor jegyezze fel. Az **API-alkalmazás URL-címének** kell lennie `https://<api_app_name>.azurewebsites.net/` . A **apiKey** -tag csak akkor szükséges, ha úgy döntött, hogy elvégzi az [ügyfelek HITELESÍTÉSét az API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) .
    >
    > Ügyeljen arra, hogy ezt a titkot a gitignore-fájlhoz adja, hogy ne véglegesítse ezeket a titkokat.

### <a name="implement-the-cross-platform-ui"></a>A platformfüggetlen felhasználói felület implementálása

1. A **main_page. dartban**cserélje le a **Build** függvényt a következőre.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Adja hozzá a szükséges importálásokat a **main_page. DART** fájl elejéhez.

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Adjon hozzá egy mezőt a **_MainPageState** osztályhoz, és tárolja a **NotificationRegistrationService**mutató hivatkozást.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. A **_MainPageState** osztályban a **OnPressed** -események **regisztrálása** és a regisztráció törlése gombok **Deregister** eseménykezelőit implementálja. Hívja meg a **regisztrálási regisztrációk**megfelelő / **Deregister** metódusait, majd jelenítse meg a riasztást az eredmény jelzéséhez.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. A **Main. dartben**most ellenőrizze, hogy az alábbi importálások szerepelnek-e a fájl elején.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Deklaráljon egy változót, amely a **NotificationActionService** egy példányára mutató hivatkozást tárol, és inicializálja azt.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Függvények hozzáadása a riasztás megjelenítésének kezeléséhez, ha egy művelet aktiválva van.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Frissítse a **fő** függvényt a **NotificationActionService** **actionTriggered** -adatfolyam megfigyeléséhez, és keresse meg az alkalmazás elindítása során rögzített műveleteket.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Ez egyszerűen a leküldéses értesítési műveletek fogadásának és propagálásának bemutatására szolgál. Ezeket általában csendesen kezeljük, például egy adott nézetre navigálva, vagy egy bizonyos adatfrissítéshez, és nem jeleníti meg a riasztásokat ebben az esetben.
