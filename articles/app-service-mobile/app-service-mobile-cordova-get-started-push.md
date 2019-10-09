---
title: Leküldéses értesítések hozzáadása egy Apache Cordova-alkalmazáshoz a Azure App Service Mobile Apps funkciójával | Microsoft Docs
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az Apache Cordova-alkalmazásba Mobile Apps használatával.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: elamalani
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8fe8485926e2c10b7fe58836196132f2881699a4
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025718"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Leküldéses értesítések hozzáadása az Apache Cordova-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá az [Apache Cordova][5] -gyors projekthez, hogy a rendszer minden egyes rekord behelyezése után leküldéses értesítést küldjön az eszköznek.

Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, szüksége lesz a leküldéses értesítési bővítmény csomagra. További információ: [a Mobile apps .NET-alapú háttérrendszer-SDK használata][1].

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy rendelkezik egy, a Visual Studio 2015-mel fejlesztett Apache Cordova-alkalmazással. Az eszköznek a Google Android-emulátoron, egy Android-eszközön, egy Windows-eszközön vagy egy iOS-eszközön kell futnia.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* A [Visual Studio Community 2015][2] -es vagy újabb verzióját futtató számítógép
* [Visual Studio Tools for Apache Cordova][4]
* [Aktív Azure-fiók][3].
* Egy befejezett [Apache Cordova][5] gyors üzembe helyezési projekt
* Android Egy ellenőrzött e-mail-címmel rendelkező [Google-fiók][6]
* iOS Egy [Apple Developer program tagsága][7] és egy IOS-eszköz (az iOS-szimulátor nem támogatja a leküldéses értesítéseket)
* Windows Egy [Microsoft Store fejlesztői fiók][8] és egy Windows 10-es eszköz

## <a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Tekintse meg a jelen szakasz lépéseit bemutató videót][9].

## <a name="update-the-server-project"></a>A kiszolgálói projekt frissítése

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Cordova-alkalmazás módosítása

Annak biztosítása érdekében, hogy az Apache Cordova-alkalmazás projekt készen álljon a leküldéses értesítések kezelésére, telepítse a Cordova leküldéses beépülő modult és a platform-specifikus leküldéses szolgáltatásokat.

#### <a name="update-the-cordova-version-in-your-project"></a>Frissítse a Cordova verzióját a projektben.

Ha a projekt a 6.1.1-es verziónál korábbi Apache Cordova-verziót használ, frissítse az ügyfél projektjét. A projekt frissítéséhez hajtsa végre a következő lépéseket:

* A Configuration Designer megnyitásához kattintson a jobb gombbal a `config.xml` elemre.
* Válassza a **platformok** lapot.
* A **CORDOVA CLI** szövegmezőben válassza a **6.1.1**elemet. 
* A projekt frissítéséhez válassza a **Létrehozás**lehetőséget, majd válassza a **megoldás létrehozása**lehetőséget.

#### <a name="install-the-push-plugin"></a>A leküldéses beépülő modul telepítése

Az Apache Cordova-alkalmazások nem kezelik natív módon az eszköz-vagy hálózati képességeket.  Ezeket a képességeket a [NPM][10] vagy a githubon közzétett beépülő modulok biztosítják. A `phonegap-plugin-push` beépülő modul kezeli a hálózati leküldéses értesítéseket.

A leküldéses beépülő modult a következő módszerek egyikével telepítheti:

**A parancssorból:**

Futtassa a következő parancsot:

    cordova plugin add phonegap-plugin-push

**A Visual studióból:**

1. A Megoldáskezelőban nyissa meg a `config.xml` fájlt. Ezután válassza a **Plugins** > **Egyéni**lehetőséget. Ezután válassza a **git** lehetőséget a telepítési forrásként.

2. Adja meg a `https://github.com/phonegap/phonegap-plugin-push` értéket a forrásként.

    ![Nyissa meg a config. xml fájlt a Megoldáskezelő][img1]

3. Válassza a telepítési forrás melletti nyilat.

4. Ha a **SENDER_ID**már rendelkezik a Google fejlesztői konzol projekthez tartozó numerikus projekt-azonosítóval, itt adhatja hozzá. Ellenkező esetben adjon meg egy helyőrző értéket, például 777777. Ha az Androidot célozza meg, ezt az értéket később is frissítheti a config. xml fájlban.

    >[!NOTE]
    >A 2.0.0 verziójának megfelelően a Google-Services. JSON fájlt a projekt gyökérkönyvtárában kell telepíteni a küldő AZONOSÍTÓjának konfigurálásához. További információ: [telepítési dokumentáció.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Válassza a **Hozzáadás** lehetőséget.

A leküldéses beépülő modul már telepítve van.

#### <a name="install-the-device-plugin"></a>Az eszköz beépülő moduljának telepítése

Kövesse ugyanazt az eljárást, amelyet a leküldéses beépülő modul telepítéséhez használt. Adja hozzá az eszköz beépülő modult a Core plugins listáról. (A kereséshez válassza a **Plugins** > **mag**lehetőséget.) Erre a beépülő modulra szüksége lesz a platform nevének beszerzéséhez.

#### <a name="register-your-device-when-the-application-starts"></a>Az eszköz regisztrálása az alkalmazás indításakor 

Kezdetben az Android rendszerhez tartalmazunk néhány minimális kódot. Később módosíthatja az alkalmazást iOS vagy Windows 10 rendszeren való futtatásra.

1. Vegyen fel egy hívást a **registerForPushNotifications** a bejelentkezési folyamat visszahívása során. Azt is megteheti, hogy a **onDeviceReady** metódus alján adja hozzá a következőt:

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

    Ez a példa a **registerForPushNotifications** hívását mutatja a sikeres hitelesítés után. A `registerForPushNotifications()` meghívása a szükséges gyakorisággal elvégezhető.

2. Adja hozzá az új **registerForPushNotifications** metódust az alábbiak szerint:

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
3. Android Az előző kódban cserélje le a `Your_Project_ID` értéket az alkalmazás numerikus projekt-azonosítójával a [Google fejlesztői konzolon][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>Választható Az alkalmazás konfigurálása és futtatása Androidon

Az Android rendszerhez készült leküldéses értesítések engedélyezéséhez fejezze be ezt a szakaszt.

#### <a name="enable-gcm"></a>Firebase Cloud Messaging engedélyezése

Mivel kezdetben a Google Android platformot célozza meg, engedélyeznie kell a Firebase Cloud Messaging szolgáltatást.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>A Mobile App back végpontjának konfigurálása leküldéses kérések küldéséhez az FCM használatával

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Az Androidhoz készült Cordova alkalmazás konfigurálása

A Cordova alkalmazásban nyissa meg a **config. xml fájlt**. Ezután cserélje le a `Your_Project_ID` értéket az alkalmazás numerikus projekt-azonosítójával a [Google fejlesztői konzolon][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Nyissa meg az **index. js fájlt**. Ezután frissítse a kódot a numerikus projekt AZONOSÍTÓjának használatához.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Android-eszköz konfigurálása USB-hibakereséshez

Az alkalmazás androidos eszközre történő üzembe helyezéséhez engedélyeznie kell az USB-hibakeresést. Hajtsa végre az alábbi lépéseket az androidos telefonján:

1. Lépjen a **beállítások** > **a telefonról**. Ezután koppintson az **összeállítási számra** , amíg a fejlesztői mód engedélyezve van (körülbelül hétszer).
2. Vissza a **beállítások** > **fejlesztői beállítások lehetőségre**, és engedélyezze az **USB-hibakeresést**. Ezután csatlakoztassa androidos telefonját a fejlesztői számítógéphez USB-kábellel.

Ezt teszteltük egy Android 6,0 (Marshmallow) szoftvert futtató Google Nexus 5X-eszköz használatával. A technikák azonban gyakran előfordulnak a modern Android-kiadásokban.

#### <a name="install-google-play-services"></a>A Google Play-szolgáltatások telepítése

A leküldéses beépülő modul az Android Google Play-szolgáltatásokra támaszkodik leküldéses értesítésekhez.

1. A Visual Studióban válassza az **eszközök** > **Android** > **Android SDK-kezelő**elemet. Ezután bontsa ki az **extrák** mappát. A megfelelő mezők ellenőrzésével győződjön meg arról, hogy a következő SDK-k mindegyike telepítve van:

   * Android 2,3 vagy újabb verzió
   * A Google adattár 27-ös vagy újabb verziója
   * Google Play-szolgáltatások 9.0.2 vagy újabb verzió

2. Válassza a **csomagok telepítése**lehetőséget. Ezután várjon, amíg a telepítés befejeződik.

Az aktuálisan szükséges kódtárak a [telefon-plugin-push telepítési dokumentációban][19]vannak felsorolva.

#### <a name="test-push-notifications-in-the-app-on-android"></a>Leküldéses értesítések tesztelése az alkalmazásban Androidon

Mostantól tesztelheti a leküldéses értesítéseket, ha futtatja az alkalmazást, és beszúrja az elemeket a TodoItem táblába. A tesztelést ugyanarról az eszközről vagy egy második eszközről végezheti, ha ugyanazt a háttér-használatot használja. Tesztelje a Cordova alkalmazást az Android platformon az alábbi módszerek egyikével:

* *Fizikai eszközön:* Az Android-eszköz csatlakoztatása a fejlesztői számítógéphez USB-kábellel.  A **Google Android Emulator**helyett válassza az **eszköz**lehetőséget. A Visual Studio üzembe helyezi az alkalmazást az eszközön, és futtatja az alkalmazást. Ezután használhatja az alkalmazást az eszközön.

  A Screen-Sharing alkalmazások, például a [Mobizen][20] segíthetnek az Android-alkalmazások fejlesztésében. A Mobizen az Android-képernyőt a SZÁMÍTÓGÉPén található webböngészőbe tervezik.

* *Android-emulátoron:* Az emulátor használatakor további konfigurációs lépések szükségesek.

    Győződjön meg arról, hogy olyan virtuális eszközre helyez üzembe, amely célként megadott Google API-kkal rendelkezik, ahogy az Android virtuális eszköz (AVD) kezelőjében is látható.

    ![Androidos virtuális Eszközkezelő](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Ha gyorsabb x86-emulátort szeretne használni, [telepítse a HAXM illesztőprogramot][11], majd konfigurálja az emulátort a használatára.

    Vegyen fel egy Google-fiókot az Android-eszközre az **alkalmazások** > **Beállítások** > **fiók hozzáadása**lehetőség kiválasztásával. Ezután kövesse az utasításokat.

    ![Google-fiók hozzáadása az Android-eszközhöz](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Futtassa a ToDoList alkalmazást az előzőekben leírtak szerint, és szúrjon be egy új teendőt. Ekkor az értesítési területen megjelenik egy értesítési ikon. Az értesítés teljes szövegének megtekintéséhez nyissa meg az értesítési fiókot.

    ![Értesítés megtekintése](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>Választható Konfigurálás és Futtatás iOS rendszeren

Ez a szakasz a Cordova-projekt iOS-eszközökön való futtatására szolgál. Ha nem dolgozik iOS-eszközökön, akkor kihagyhatja ezt a szakaszt.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Az iOS távoli Build-ügynök telepítése és futtatása Mac vagy Cloud Service-en

Mielőtt a Visual Studióval futtasson egy Cordova alkalmazást az iOS-en, folytassa az [iOS telepítési útmutatójának][12] lépéseit a távoli Build ügynök telepítéséhez és futtatásához.

Győződjön meg arról, hogy az iOS-hez készült alkalmazást is felépítheti. A telepítési útmutató lépései szükségesek az iOS rendszerhez készült alkalmazás létrehozásához a Visual studióból. Ha nem rendelkezik Mac-kiszolgálóval, a távoli Build ügynök használatával hozhat létre iOS-t a MacInCloud-hez hasonló szolgáltatásokhoz. További információ: [iOS-alkalmazás futtatása a felhőben][21].

> [!NOTE]
> A leküldéses beépülő modul iOS rendszeren való használatához a Xcode 7 vagy újabb rendszer szükséges.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Az alkalmazás-AZONOSÍTÓként használandó azonosító megkeresése

Mielőtt regisztrálta az alkalmazást a leküldéses értesítésekhez, nyissa meg a config. xml fájlt a Cordova-alkalmazásban, keresse meg a `id` attribútum értékét a widget elemben, majd másolja a későbbi használatra. A következő XML-ben az azonosító `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Később ezt az azonosítót kell használnia, amikor létrehoz egy alkalmazás-azonosítót az Apple fejlesztői portálján. Ha egy másik alkalmazás-azonosítót hoz létre a fejlesztői portálon, akkor az oktatóanyag későbbi részében néhány további lépést is végre kell hajtania. A widget elemben szereplő AZONOSÍTÓnak meg kell egyeznie az alkalmazás-AZONOSÍTÓval a fejlesztői portálon.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Az alkalmazás regisztrálása leküldéses értesítésekhez az Apple fejlesztői portálon

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Az Azure konfigurálása leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Annak ellenőrzése, hogy az alkalmazás azonosítója megfelel-e a Cordova-alkalmazásnak

Ha az Apple Developer-fiókban létrehozott alkalmazás-azonosító már megegyezik a config. xml fájlban található widget-elem azonosítójával, akkor kihagyhatja ezt a lépést. Ha azonban az azonosítók nem egyeznek, hajtsa végre a következő lépéseket:

1. Törölje a platformok mappát a projektből.
2. Törölje a plugins mappát a projektből.
3. Törölje a node_modules mappát a projektből.
4. Frissítse a config. xml fájlban található widget elem ID attribútumát az Apple Developer-fiókjában létrehozott alkalmazás-azonosító használatára.
5. Hozza létre újra a projektet.

##### <a name="test-push-notifications-in-your-ios-app"></a>Leküldéses értesítések tesztelése iOS-alkalmazásokban

1. Győződjön meg arról, hogy a Visual Studióban az **iOS** van kiválasztva a telepítési célként. Ezután válassza az **eszköz** lehetőséget a leküldéses értesítések futtatásához a csatlakoztatott iOS-eszközön.

    A leküldéses értesítéseket futtathatja a számítógéphez csatlakoztatott iOS-eszközön az iTunes használatával. Az iOS-szimulátor nem támogatja a leküldéses értesítéseket.

2. Válassza a **Futtatás** gombot vagy az **F5 billentyűt** a Visual Studióban a projekt felépítéséhez és az alkalmazás iOS-eszközben való elindításához. Ezután kattintson **az OK gombra** a leküldéses értesítések fogadásához.

   > [!NOTE]
   > Az alkalmazás a leküldéses értesítések megerősítését kéri az első futtatás során.

3. Az alkalmazásban írjon be egy feladatot, majd válassza a pluszjel **(+)** ikont.
4. Ellenőrizze, hogy érkezett-e értesítés. Ezután kattintson az **OK gombra** az értesítés elvetéséhez.

## <a name="optional-configure-and-run-on-windows"></a>Választható Konfigurálás és Futtatás Windows rendszeren

Ez a szakasz azt ismerteti, hogyan futtatható az Apache Cordova alkalmazás-projekt Windows 10-es eszközökön (a telefon leküldéses beépülő modulja támogatott a Windows 10 rendszeren). Ha nem Windows-eszközökkel dolgozik, kihagyhatja ezt a szakaszt.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Windows-alkalmazás regisztrálása leküldéses értesítésekhez a WNS-mel

Ha a Visual Studióban szeretné használni az áruház beállításait, válasszon ki egy Windows-célt a megoldás-platformok listából, például **Windows-x64** vagy **Windows-x86**. (Kerülje a **Windows-anycpu** a leküldéses értesítésekhez.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Tekintse meg hasonló lépéseket ismertető videót][13]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS tartozó értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>A Cordova-alkalmazás konfigurálása a Windows leküldéses értesítések támogatásához

A Configuration Designer megnyitásához kattintson a jobb gombbal a **config. XML**fájlra. Ezután válassza a **tervező megtekintése**lehetőséget. Ezután válassza a **Windows** fület, majd válassza a Windows **10** lehetőséget a **cél verziója**területen.

A leküldéses értesítések támogatásához az alapértelmezett (hibakeresési) buildeken nyissa meg a **Build. JSON** fájlt. Ezután másolja a "kiadás" konfigurációt a hibakeresési konfigurációba.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

A frissítés után a **Build. JSON** fájlnak tartalmaznia kell a következő kódot:

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

Hozza létre az alkalmazást, és ellenőrizze, hogy nincsenek-e hibák. Az ügyfélalkalmazás ekkor regisztrálnia kell az értesítéseket a Mobile Apps háttérből. Ismételje meg ezt a szakaszt minden Windows-projekt esetében a megoldásban.

#### <a name="test-push-notifications-in-your-windows-app"></a>Leküldéses értesítések tesztelése a Windows-alkalmazásban

Győződjön meg arról, hogy a Visual Studióban egy Windows-platform van kiválasztva központi telepítési célként, például **Windows-x64** vagy **Windows-x86**. Ha a Visual studiót futtató Windows 10 rendszerű számítógépen szeretné futtatni az alkalmazást, válassza a **helyi gép**lehetőséget.

1. Kattintson a **Futtatás** gombra a projekt felépítéséhez és az alkalmazás elindításához.

2. Az alkalmazásban írjon be egy új todoitem nevét, majd válassza a plusz **(+)** ikont a hozzáadásához.

Ellenőrizze, hogy a rendszer értesítést kap-e az adott elemmel kapcsolatban.

## <a name="next-steps"></a>Következő lépések

* További információ a leküldéses értesítésekről: [Notification Hubs][17] .
* Ha még nem tette meg, folytassa az oktatóanyagot az Apache Cordova-alkalmazáshoz való [hitelesítés hozzáadásával][14] .

Ismerje meg, hogyan használhatja a következő SDK-kat:

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
