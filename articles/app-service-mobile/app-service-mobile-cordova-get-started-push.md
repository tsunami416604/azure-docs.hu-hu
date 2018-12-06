---
title: Leküldéses értesítések hozzáadása az Apache Cordova-alkalmazást az Azure App Service Mobile Apps szolgáltatással |} A Microsoft Docs
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az Apache Cordova-alkalmazást a Mobile Apps segítségével.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 40a7552ffd0bfcab173d2e35c52313a94ec3d0bb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960328"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Az Apache Cordova-alkalmazáshoz a leküldéses értesítések hozzáadása

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a leküldéses értesítések hozzáadása az [Apache Cordova rövid] [ 5] projekthez, hogy a leküldéses értesítést küld az eszköz minden alkalommal, amikor a rendszer beszúr egy rekordot.

Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, a leküldéses értesítési kiterjesztési csomag kell. További információkért lásd: [használható a .NET háttérkiszolgáló-SDK-t az a Mobile Apps][1].

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy az Apache Cordova-alkalmazáshoz, amelyek célja a Visual Studio 2015. Ez az eszköz a Google Android Emulator, egy Android-eszközön, a Windows-eszköz vagy egy iOS-eszközön kell futtatnia.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* A számítógép [Visual Studio Community 2015] [ 2] vagy újabb
* [A Visual Studio Nástroje Pro Apache Cordova][4]
* Egy [aktív Azure-fiók][3]
* A befejezett [Apache Cordova rövid] [ 5] projekt
* (Android) A [Google-fiók] [ 6] ellenőrzött e-mail-címmel
* (iOS) Egy [Apple Fejlesztőprogrambeli tagság] [ 7] és a egy iOS-eszközön (iOS-szimulátor nem támogatja a leküldéses értesítések)
* (Windows) A [Microsoft Store fejlesztői fiók] [ 8] és a egy Windows 10 rendszerű eszköz

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Ebben a szakaszban szereplő lépéseket ismertető videó][9].

## <a name="update-the-server-project"></a>A kiszolgálói projekt frissítése

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Módosítsa a Cordova-alkalmazáshoz

Annak érdekében, hogy az Apache Cordova-alkalmazás projekt készen áll a leküldéses értesítések kezeléséhez, a Cordova beépülő modul leküldéses és platformspecifikus leküldéses szolgáltatások telepítéséhez.

#### <a name="update-the-cordova-version-in-your-project"></a>Frissítse a projekt a Cordova-verzió.

A projekt Apache Cordova 6.1.1 verziónál korábbi verzióját használja, ha az ügyfél projekt frissítése. A projekt frissítéséhez tegye a következőket:

* A konfigurációtervező megnyitásához kattintson a jobb gombbal `config.xml`.
* Válassza ki a **platformok** fülre.
* Az a **Cordova CLI** szövegbeviteli mezőben válasszon ki **6.1.1**. 
* A projekt frissítéséhez válassza ki **összeállítása**, majd válassza ki **megoldás fordítása**.

#### <a name="install-the-push-plugin"></a>A leküldéses beépülő modul telepítése

Az Apache Cordova-alkalmazások natív módon nem kezeli a eszköz vagy a hálózatkezelő képességeit.  Ezek a képességek által biztosított beépülő modulokat, amelyek a közzétett vagy [npm] [ 10] vagy a Githubon. A `phonegap-plugin-push` beépülő modul kezeli a hálózati leküldéses értesítéseket.

A leküldéses beépülő modul a következő módokon telepíthető:

**A parancssorba:**

Futtassa az alábbi parancsot:

    cordova plugin add phonegap-plugin-push

**A Visual Studión belül:**

1. A Megoldáskezelőben nyissa meg a `config.xml` fájlt. Majd **beépülő modulok** > **egyéni**. Válassza ki **Git** telepítési forrásként.

2. Adja meg `https://github.com/phonegap/phonegap-plugin-push` forrásaként.

    ![Nyissa meg a soubor Config.XML v fájlt a Megoldáskezelőben][img1]

3. Válassza ki a telepítési forrás melletti nyílra.

4. A **SENDER_ID**, ha már rendelkezik egy numerikus projekt Azonosítóját, a Google Developer Console projekt, akkor is Itt veheti fel. Ellenkező esetben adja meg a helyőrző értéket, például 777777. Android céloz meg, ha ezt az értéket később a soubor Config.XML v fájlban frissítheti.

    >[!NOTE]
    >2.0.0-s verziójával kezdődően a google-services.json telepítve kell lennie a gyökérmappájában lévő mappának a projekt konfigurálásához a feladó azonosítója. További információkért lásd: a [ügyféltelepítési dokumentációban.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Válassza a **Hozzáadás** lehetőséget.

A leküldéses beépülő modul már telepítve van.

#### <a name="install-the-device-plugin"></a>Az eszköz beépülő modul telepítése

Kövesse ugyanazt az eljárást a leküldéses beépülő modul telepítéséhez is használt. Az eszköz beépülő modul hozzáadása a Core beépülő modulok listában. (Is megkeresheti, válassza ki a **beépülő modulok** > **Core**.) Ez a platform nevének beszerzése a beépülő modul van szüksége.

#### <a name="register-your-device-when-the-application-starts"></a>Regisztrálja az eszközt, az alkalmazás indításakor 

Minimális kód kezdetben Android tartalmazza. Az alkalmazás futtatását az iOS-vagy Windows 10-es később módosíthatja.

1. Adja hozzá egy hívást **registerForPushNotifications** a visszahívás a bejelentkezési folyamat során. Másik lehetőségként felveheti alján a **onDeviceReady** módszer:

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    Ez a példa bemutatja a hívó **registerForPushNotifications** sikeres hitelesítést követően. Meghívhatja `registerForPushNotifications()` gyakran szükség.

2. Az új **registerForPushNotifications** módszert az alábbiak szerint:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. (Android) Cserélje le a fenti kóddal, `Your_Project_ID` numerikus project azonosítója, az alkalmazás a [Google Developer Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Nem kötelező) Konfigurálja és futtassa az alkalmazást Android rendszeren

Ez a szakasz az Android leküldéses értesítések engedélyezéséhez végezze el.

#### <a name="enable-gcm"></a>Engedélyezze a Firebase Cloud Messaging

Céloz meg a Google Android platform kezdetben, mivel a Firebase Cloud Messaging kell engedélyeznie.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>A mobilalkalmazás háttérrendszere FCM használatával leküldéses kérelmek küldésére konfigurálása

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Az Android a Cordova-alkalmazás konfigurálása

Nyissa meg a Cordova-alkalmazás **soubor Config.XML v**. Ezután cserélje le `Your_Project_ID` numerikus project azonosítója, az alkalmazás a [Google Developer Console][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Nyissa meg **index.js**. Frissítse a kódot, a projekt numerikus azonosítója.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Az USB-hibakeresés Android-eszköz konfigurálása

Az Android-eszközön az alkalmazás telepítése előtt szüksége USB-hibakeresés engedélyezése. Az alábbi lépéseket az Androidos telefonján:

1. Lépjen a **beállítások** > **névjegye**. Koppintson a **buildszám** mindaddig, amíg a fejlesztői mód engedélyezve van (körülbelül hét alkalommal).
2. Térjen vissza **beállítások** > **fejlesztői beállítások**, engedélyezze **USB-hibakeresés**. Az Androidos telefonján majd csatlakozni a fejlesztési számítógép USB-kábellel.

Tesztelt Ez egy Android 6.0 (Marshmallow) rendszert futtató Google Nexus 5 X-eszköz használatával. Azonban a technikák által közösen bármelyik modern Androidos verzióját.

#### <a name="install-google-play-services"></a>Google Play-szolgáltatások telepítése

A leküldéses beépülő modul leküldéses értesítések az Android Google Play szolgáltatások támaszkodik.

1. A Visual Studióban válassza ki a **eszközök** > **Android** > **Android SDK Manager**. Ezután bontsa ki a **kiegészítő funkciók** mappát. Jelölje be a megfelelő győződjön meg arról, hogy a következő SDK-k mindegyike van telepítve:

   * Android 2.3 vagy újabb
   * Google-tárház változat 27-es vagy újabb
   * A Google Play-szolgáltatások 9.0.2-es vagy újabb

2. Válassza ki **csomagok telepítéséhez**. Várjon, amíg a telepítés befejeződik.

A jelenlegi szükséges kódtárak szerepelnek a [phonegap-beépülő modul – leküldéses ügyféltelepítési dokumentáció][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Teszt leküldéses értesítések az alkalmazásban androidon

Így most teszt leküldéses értesítések az alkalmazás futtatása és az elem beszúrása a TodoItem táblába. Ugyanazon az eszközön, illetve egy második eszköz, amennyiben az ugyanazon háttérrendszer használata tesztelheti. Az Android platformon a Cordova-alkalmazás teszteléséhez a következő módszerek egyikével:

* *A fizikai eszközön:* Android-eszköz csatlakoztatása a fejlesztési számítógép USB-kábellel.  Helyett **Google Android Emulator**válassza **eszköz**. Visual Studio telepíti az alkalmazást az eszközön, és futtatja az alkalmazást. Ezután használhatja az alkalmazást az eszközön.

  Képernyő-megosztási alkalmazások, mint például [Mobizen] [ 20] is segítséget nyújt a Android-alkalmazások fejlesztéséhez használható. Mobizen-projektek, az Android képernyő egy webböngészőt a számítógépen.

* *Egy Android-emulátor:* vannak további konfigurációs lépések, amelyek szükségesek, amikor az emulátor használata.

    Ellenőrizze, hogy a virtuális eszköz, amely rendelkezik a Google API-kat, állítsa be a cél, ahogyan az az Android virtuális eszközt (AVD) kezelő végzi.

    ![Android virtuális eszközt kezelője](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Ha szeretné használni a gyorsabb x86 emulátorban, [a HAXM illesztőprogramot telepítse][11], majd konfigurálja az emulátorban, használhatja azt.

    Google-fiók hozzáadása az Android-eszköz kiválasztásával **alkalmazások** > **beállítások** > **fiók hozzáadása**. Ezután kövesse az utasításokat.

    ![Google-fiók hozzáadása az Android-eszközön](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    A todolist alkalmazást előtt futtassa, és a egy új teendő elem beszúrása. Ennek során egy értesítési ikon jelenik meg az értesítési területen. A teljes szöveg a bejelentés megtekintése az értesítési fiókot is megnyithatja.

    ![Értesítés megtekintése](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Nem kötelező) Konfigurálhat és IOS-eszközökön futtathatja

Ez a szakasz olyan iOS-eszközökön a Cordova-projekt futtatása. Ha nem dolgozik iOS-eszközök, kihagyhatja ezt a szakaszt.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Telepítse és futtassa az IOS-es távoli fordító-ügynökhöz a Mac vagy felhőalapú szolgáltatásként

Cordova-alkalmazás futtatása a Visual Studio használatával iOS rendszeren, előtt végignézzük a lépéseket a a [iOS beállítási útmutató] [ 12] telepíteni és futtatni a távoli fordító-ügynökhöz.

Ellenőrizze, hogy az IOS-alkalmazást hozhat létre. A beállítási útmutató a lépések szükségesek az IOS-alkalmazás létrehozása a Visual Studióból. Ha nem rendelkezik a Mac, hozhat létre iOS-hez a távoli fordító-ügynökhöz hasonlóan MacInCloud szolgáltatás használatával. További információkért lásd: [az iOS-alkalmazás futtatása a felhőben][21].

> [!NOTE]
> Xcode 7 vagy újabb IOS-eszközökön a leküldéses beépülő modul használata szükséges.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Keresse meg az Azonosítót használja, az App-ID

Az alkalmazást a leküldéses értesítésekre, open config.xml a Cordova-alkalmazáshoz, a regisztráció előtt keresse meg a `id` attribútum a widget elemben lévő értéket, és másolja azt későbbi használatra. A következő XML-ben az azonosító: `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Ez az azonosító később, akkor használja, ha az Apple fejlesztői portálján hozzon létre egy App ID. Ha a fejlesztői portálon hoz létre egy másik alkalmazás azonosítója, néhány további lépést kell végeznie az oktatóanyag későbbi részében. A widget elem Azonosítóját meg kell egyeznie az Alkalmazásazonosítót a fejlesztői portálon.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Regisztrálja az alkalmazást leküldéses értesítésekhez az Apple fejlesztői portálján

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Leküldéses értesítések küldése az Azure konfigurálása

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Győződjön meg arról, hogy az alkalmazás azonosítója egyezik-e a Cordova-alkalmazáshoz

Ha az alkalmazás azonosítója, amelyet az Apple Developer-fiók már a soubor Config.XML v fájlban a widget elem azonosítója megegyezik, kihagyhatja ezt a lépést. Ha az azonosító nem egyezik, tegye a következőket:

1. A platformok mappa törlése a projektből.
2. A beépülő modulok mappa törlése a projektből.
3. A node_modules mappa törlése a projektből.
4. Frissítse az azonosító attribútum a widget elem a soubor Config.XML v fájlban, az alkalmazás azonosítója, amelyet az Apple developer-fiók használata.
5. A projekt újraépítéséhez.

##### <a name="test-push-notifications-in-your-ios-app"></a>Teszt leküldéses értesítéseket az iOS-alkalmazás

1. A Visual Studióban, győződjön meg arról, hogy **iOS** van kiválasztva, a központi telepítési céllal. Válassza ki **eszköz** futtatásához a leküldéses értesítéseket a csatlakoztatott iOS-eszközön.

    A leküldéses értesítések iOS-eszközön, amely kapcsolódik a számítógéphez, az iTunes futtathatja. Az iOS-szimulátor nem támogatja a leküldéses értesítéseket.

2. Válassza ki a **futtatása** gomb vagy **F5** a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást az iOS-eszközök a Visual studióban. Válassza ki **OK** leküldéses értesítések fogadásához.

   > [!NOTE]
   > Az alkalmazás első futtatása során a leküldéses értesítések megerősítését kéri.

3. Az alkalmazásban írjon be egy feladatot, és válassza a plusz **(+)** ikonra.
4. Győződjön meg arról, hogy az értesítés érkezett. Válassza ki **OK** az értesítés elvetéséhez.

## <a name="optional-configure-and-run-on-windows"></a>(Nem kötelező) Konfigurálja és futtassa a Windows

Ez a szakasz ismerteti, hogyan futtathat a Apache Cordova-projektet (a PhoneGap leküldéses beépülő modul támogatja a Windows 10-es) a Windows 10-es eszközökön. Ha nem dolgozik Windows-eszközök, kihagyhatja az ebben a szakaszban.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>A leküldéses értesítések Windows alkalmazás regisztrálása a WNS-sel

A Store beállítások használatához a Visual Studióban, válassza ki a Windows-tároló a listából megoldás platformok, mint például **Windows-x64** vagy **Windows-x86**. (Elkerülése érdekében **Windows-AnyCPU** leküldéses értesítésekre való.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Tekintse meg hasonló lépéseket ismertető videót][13]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS az értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>A Cordova-alkalmazás támogatja a Windows leküldéses értesítések konfigurálása

A konfigurációtervező megnyitásához kattintson a jobb gombbal **soubor Config.XML v**. Válassza ki **adatforrásnézet-tervezőből**. Ezután válassza ki a **Windows** lapra, és válassza ki **Windows 10-es** alatt **Cílová Verze Windows**.

Az alapértelmezett (Hibakeresés) buildeket támogatja a leküldéses értesítések, nyissa meg a **build.json** fájlt. Ezután másolja a "release" konfiguráció a hibakeresési konfigurációhoz.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

A frissítés után a **build.json** fájlnak tartalmaznia kell a következő kódot:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Az alkalmazás létrehozása, és ellenőrizze, hogy rendelkezik-e hibák. Az ügyfélalkalmazás most regisztráljanak az értesítések a Mobile Apps háttéralkalmazásból. Ismételje meg minden Windows-projektet a megoldásban ez a szakasz.

#### <a name="test-push-notifications-in-your-windows-app"></a>Teszt leküldéses értesítéseket a Windows-alkalmazás

A Visual Studióban, győződjön meg arról, hogy egy Windows-platform van kiválasztva a telepítés céljaként például **Windows-x64** vagy **Windows-x86**. Futtassa az alkalmazást a Visual Studio futtató Windows 10 rendszerű, válassza a **helyi gép**.

1. Válassza ki a **futtatása** gombot a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást.

2. Az alkalmazásban írjon be egy nevet az új beállíthatnánk, és válassza a plusz **(+)** ikonra kattintva adja hozzá.

Győződjön meg arról, hogy értesítés érkezik, az elem hozzáadásakor.

## <a name="next-steps"></a>Következő lépések

* További információ [Notification Hubs] [ 17] a leküldéses értesítések megismerése.
* Ha még nem tette meg, továbbra is az oktatóanyag által [hitelesítés hozzáadása] [ 14] az Apache Cordova-alkalmazáshoz.

Ismerje meg, hogyan használható a következő SDK-k:

* [Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
