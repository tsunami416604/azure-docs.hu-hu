---
title: Leküldéses értesítések hozzáadása Apache Cordova alkalmazáshoz
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket a Mobilalkalmazásokkal az Apache Cordova alkalmazásba.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 08260437076728421cb6fa393f481d27b95b1782
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461606"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Push-értesítések hozzáadása az Apache Cordova alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá az [Apache Cordova gyorsindítási][5] projekthez, hogy a rendszer leküldéses értesítést küldjön az eszköznek minden alkalommal, amikor egy rekordot beszúr.

Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, szüksége van a leküldéses értesítésbővítmény-csomagra. További információt [a .NET háttérkiszolgáló SDK mobilalkalmazásokhoz szolgáltatása című témakörben talál.][1]

## <a name="prerequisites"></a><a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy van egy Apache Cordova alkalmazás, amelyet a Visual Studio 2015-tel fejlesztettek ki. Ennek az eszköznek Google Android Emulátoron, Android-eszközön, Windows-eszközön vagy iOS-eszközön kell futnia.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Visual [Studio-közösséggel rendelkező számítógép 2015-ben][2] vagy újabb verzióban
* [Visual Studio Tools for Apache Cordova][4]
* [Aktív Azure-fiók][3]
* Befejezett [Apache Cordova gyorsindítási][5] projekt
* (Nem, nem, nem, nem, nem, [Google-fiók][6] ellenőrzött e-mail címmel
* (iOS) [Apple Developer Program tagság][7] és iOS-eszköz (az iOS-szimulátor nem támogatja a leküldéses értesítéseket)
* (Windows) [Microsoft Store fejlesztői fiók][8] és Windows 10-es eszköz

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Tekintse meg az ebben a szakaszban található lépéseket bemutató videót.][9]

## <a name="update-the-server-project"></a>A kiszolgálóprojekt frissítése

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="modify-your-cordova-app"></a><a name="add-push-to-app"></a>A Cordova alkalmazás módosítása

Annak érdekében, hogy az Apache Cordova alkalmazásprojekt készen álljon a leküldéses értesítések kezelésére, telepítse a Cordova push plugint és minden platformspecifikus leküldéses szolgáltatást.

#### <a name="update-the-cordova-version-in-your-project"></a>Frissítse a Cordova verziót a projektben.

Ha a projekt az Apache Cordova 6.1.1-es verziónál korábbi verzióját használja, frissítse az ügyfélprojektet. A projekt frissítéséhez tegye a következő lépéseket:

* A konfigurációtervező megnyitásához kattintson a jobb gombbal a gombra. `config.xml`
* Válassza a **Platformok** lapot.
* A **Cordova CLI** szövegmezőben válassza a **6.1.1**. 
* A projekt frissítéséhez válassza a Build ( **Build**) lehetőséget, majd a **Megoldás összeállítása**lehetőséget.

#### <a name="install-the-push-plugin"></a>Telepítse a push plugin

Az Apache Cordova alkalmazások nem kezelik natív módon az eszköz- vagy hálózati képességeket.  Ezeket a képességeket az [npm-en][10] vagy a GitHubon közzétett bővítmények biztosítják. A `phonegap-plugin-push` bővítmény kezeli a hálózati leküldéses értesítéseket.

A push plugint az alábbi módokon telepítheti:

**A parancssorból:**

Futtassa az alábbi parancsot:

    cordova plugin add phonegap-plugin-push

**A Visual Studio-ból:**

1. A Megoldáskezelőben `config.xml` nyissa meg a fájlt. Ezután válassza **a Beépülő modulok** > **egyéni**lehetőséget. Ezután válassza a **Git** telepítési forrásként.

2. Forrásként adja meg `https://github.com/phonegap/phonegap-plugin-push` a beírást.

    ![A config.xml fájl megnyitása a Megoldáskezelőben][img1]

3. Jelölje ki a telepítési forrás melletti nyilat.

4. Ha **a SENDER_ID**rendelkezik numerikus projektazonosítóval a Google Developer Console projekthez, itt is hozzáadhatja. Ellenkező esetben adjon meg egy helyőrző értéket, például 777777. Ha az Androidot célozza, ezt az értéket később frissítheti a config.xml fájlban.

    >[!NOTE]
    >A 2.0.0-s verziótól a google-services.json fájlt telepíteni kell a projekt gyökérmappájába a feladó azonosítójának konfigurálásához. További információt a [telepítési dokumentációban talál.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Válassza a **Hozzáadás** lehetőséget.

A push plugin most telepítve van.

#### <a name="install-the-device-plugin"></a>A készülék beépülő moduljának telepítése

Kövesse ugyanazt az eljárást, amelyet a push plugin telepítéséhez használt. Adja hozzá a Device plugint a Core beépülő modulok listájából. (Ahhoz, hogy megtalálja, válassza **a Plugins** > **Core**.) Szüksége van erre a plugin, hogy megszerezze a platform nevét.

#### <a name="register-your-device-when-the-application-starts"></a>Az eszköz regisztrálása az alkalmazás indításakor 

Kezdetben néhány minimális kódot is tartalmazunk az Android számára. Később módosíthatja az alkalmazást, hogy iOS vagy Windows 10 rendszeren fusson.

1. Adjon hozzá egy hívást a **regisztrációhozForPushNotifications** a bejelentkezési folyamat visszahívása során. Másik lehetőségként hozzáadhatja az **onDeviceReady** metódus alján:

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

    Ez a példa a **registerForPushNotifications** hívását mutatja be a hitelesítés sikeresse után. Annyit `registerForPushNotifications()` hívhat, ahányszor csak szükséges.

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
3. (Nem, nem, nem, nem, nem, Az előző kódban `Your_Project_ID` cserélje le az alkalmazás numerikus projektazonosítóját a [Google Fejlesztői konzolról.][18]

## <a name="optional-configure-and-run-the-app-on-android"></a>(Nem kötelező) Az alkalmazás konfigurálása és futtatása Androidon

Töltse ki ezt a szakaszt az Android leküldéses értesítéseinek engedélyezéséhez.

#### <a name="enable-firebase-cloud-messaging"></a><a name="enable-gcm"></a>A Firebase Cloud Messaging engedélyezése

Mivel kezdetben a Google Android platformot célozza meg, engedélyeznie kell a Firebase Cloud Messaging szolgáltatást.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-the-mobile-app-back-end-to-send-push-requests-using-fcm"></a><a name="configure-backend"></a>A mobilalkalmazás háttértartalékának konfigurálása leküldéses kérelmek küldésére az FCM használatával

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Cordova alkalmazás konfigurálása Androidra

A Cordova alkalmazásban nyissa meg **a config.xml**fájlt. Ezután `Your_Project_ID` cserélje le az alkalmazás numerikus projektazonosítóját a [Google Fejlesztői konzolról.][18]

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Nyissa meg **az index.js .** Ezután frissítse a kódot a numerikus projektazonosító használatához.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-your-android-device-for-usb-debugging"></a><a name="configure-device"></a>Az Android-eszköz konfigurálása USB-hibakereséshez

Mielőtt telepítheti az alkalmazást az Android-eszközre, engedélyeznie kell az USB-hibakeresést. Tegye a következő lépéseket Android-telefonján:

1. Nyissa meg a **Telefon** > **beállításai**t. Ezután érintse meg a **Build számot,** amíg a fejlesztői mód engedélyezve van (körülbelül hétszer).
2. A **Beállítások fejlesztői** > **beállítások párbeszédpanelen**engedélyezze az **USB-hibakeresést.** Ezután csatlakoztassa androidos telefonját a fejlesztői számítógéphez USB-kábellel.

Ezt egy Android 6.0 (Marshmallow) rendszert futtató Google Nexus 5X eszközzel teszteltük. Azonban a technikák gyakoriak minden modern Android kiadásban.

#### <a name="install-google-play-services"></a>A Google Play-szolgáltatások telepítése

A push plugin támaszkodik Android Google Play Szolgáltatások push értesítéseket.

1. A Visual Studio alkalmazásban válassza **az Eszközök** > **Android** > **Android SDK-kezelő**lehetőséget. Ezután bontsa ki az **Extrák mappát.** Jelölje be a megfelelő négyzeteket, és győződjön meg arról, hogy az alábbi SDK-k mindegyike telepítve van:

   * Android 2.3 vagy újabb
   * A Google Repository 27-es vagy újabb verziója
   * Google Play-szolgáltatások 9.0.2 vagy újabb verzió

2. Válassza **a Csomagok telepítése**lehetőséget. Ezután várja meg, amíg a telepítés befejeződik.

A jelenlegi szükséges könyvtárak a [phonegap-plugin-push telepítési dokumentációban][19]találhatók.

#### <a name="test-push-notifications-in-the-app-on-android"></a>Leküldéses értesítések tesztelése az alkalmazásban Android-eszközön

Most már tesztelheti a leküldéses értesítéseket az alkalmazás futtatásával és a TodoItem táblába való beszúrással. Tesztelheti ugyanattól az eszközről vagy egy második eszközről, feltéve, hogy ugyanazt a háttért használja. Tesztelje Cordova alkalmazását az Android platformon az alábbi módokon:

* *Fizikai eszközön:* Csatlakoztassa Android-eszközét a fejlesztői számítógéphez USB-kábellel.  A **Google Android Emulátor**helyett válassza **az Eszköz**lehetőséget. A Visual Studio telepíti az alkalmazást az eszközre, és futtatja az alkalmazást. Ezután az eszközön kezelheti az alkalmazást.

  A képernyő-megosztó alkalmazások, mint például a [Mobizen,][20] segíthetnek az Android alkalmazások fejlesztésében. Mobizen projektek androidos képernyőn egy webböngésző a számítógépen.

* *Android emulátoron:* Az emulátor használata kor további konfigurációs lépések szükségesek.

    Győződjön meg arról, hogy olyan virtuális eszközre telepít, amelynek a Google API-k vannak beállítva a célként, ahogy az az Android virtuális eszköz (AVD) kezelőjében látható.

    ![Android virtuális eszközkezelő](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Ha gyorsabb x86-os emulátort szeretne használni, [telepítse a HAXM illesztőprogramot,][11]majd állítsa be az emulátort a használatára.

    Adjon hozzá egy Google-fiókot az Android-eszközhöz az **Alkalmazások** > **beállításai** > **fiók hozzáadása**lehetőséget választva. Ezután kövesse az utasításokat.

    ![Google-fiók hozzáadása az Android-eszközhöz](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Futtassa a todolista alkalmazást a korábban, és szúrjon be egy új teendőelemet. Ezúttal egy értesítési ikon jelenik meg az értesítési területen. Az értesítési fiók megnyitásával megtekintheti az értesítés teljes szövegét.

    ![Értesítés megtekintése](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Nem kötelező) Konfigurálás és futtatás iOS rendszeren

Ez a rész a Cordova projekt iOS-eszközökön való futtatásához készült. Ha nem iOS-eszközökkel dolgozik, kihagyhatja ezt a szakaszt.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Az iOS távoli buildügynök telepítése és futtatása Macen vagy felhőszolgáltatáson

Mielőtt futtatna egy Cordova alkalmazást iOS-en a Visual Studio használatával, hajtsa végre az [iOS telepítési útmutatójának lépéseit][12] a távoli buildügynök telepítéséhez és futtatásához.

Győződjön meg arról, hogy létre tudja hozni az alkalmazást iOS-re. Az iOS-es alkalmazás Visual Studio-ból történő létrehozásához a beállítási útmutató lépései szükségesek. Ha nem rendelkezik Mac számítógéppel, az iOS-hez a távoli buildügynök használatával hozhat létre egy olyan szolgáltatáson, mint a MacInCloud. További információ: [Az iOS-alkalmazás futtatása a felhőben][21]című témakörben talál.

> [!NOTE]
> Xcode 7 vagy nagyobb van szükség, hogy használja a push plugin iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Az alkalmazásazonosítóként használandó azonosító megkeresése

Mielőtt regisztrálná az alkalmazást leküldéses értesítésekre, nyissa meg a `id` config.xml fájlt a Cordova alkalmazásban, keresse meg az attribútum értékét a widget elemben, majd másolja későbbi használatra. A következő XML-ben az `io.cordova.myapp7777777`azonosító .

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Később ezt az azonosítót akkor használja, amikor alkalmazásazonosítót hoz létre az Apple fejlesztői portálján. Ha egy másik alkalmazásazonosítót hoz létre a fejlesztői portálon, az oktatóanyag későbbi részében néhány további lépést kell tennie. A widget elemazonosítójának meg kell egyeznie a fejlesztői portálon lévő alkalmazásazonosítóval.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Regisztrálja az alkalmazást leküldéses értesítésekhez az Apple fejlesztői portálján

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Az Azure konfigurálása leküldéses értesítések küldésére

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Ellenőrizze, hogy az alkalmazásazonosítója megegyezik-e a Cordova alkalmazással

Ha az Apple Developer Accountban létrehozott alkalmazásazonosító már megegyezik a config.xml fájlban lévő widgetelem azonosítójával, kihagyhatja ezt a lépést. Ha azonban az azonosítók nem egyeznek, tegye a következő lépéseket:

1. Törölje a platformok mappát a projektből.
2. Törölje a beépülő modulok mappáját a projektből.
3. Törölje a node_modules mappát a projektből.
4. Frissítse a config.xml fájlwidget-elemiazonosító-attribútumát az Apple fejlesztői fiókjában létrehozott alkalmazásazonosító használatához.
5. Építse újra a projektet.

##### <a name="test-push-notifications-in-your-ios-app"></a>Leküldéses értesítések tesztelése az iOS-alkalmazásban

1. A Visual Studio,győződjön meg arról, hogy **az iOS** van kiválasztva a telepítési cél. Ezután válassza az **Eszköz** lehetőséget a leküldéses értesítések futtatásához a csatlakoztatott iOS-eszközön.

    A leküldéses értesítéseket olyan iOS-eszközön futtathatja, amely az iTunes segítségével csatlakozik a számítógéphez. Az iOS-szimulátor nem támogatja a leküldéses értesítéseket.

2. A **Projekt** létrehozásához és az alkalmazás iOS-eszközön való indításához válassza a Futtatás vagy az **F5** lehetőséget a Visual Studióban. Ezután a leküldéses értesítések elfogadásához válassza az **OK gombot.**

   > [!NOTE]
   > Az alkalmazás megerősítést kér a leküldéses értesítésekhez az első futtatás során.

3. Az alkalmazásban írjon be egy feladatot, majd kattintson a plusz **(+)** ikonra.
4. Ellenőrizze, hogy érkezett-e értesítés. Ezután az **OK gombra** választva utasítsa el az értesítést.

## <a name="optional-configure-and-run-on-windows"></a>(Nem kötelező) A Windows konfigurálása és futtatása

Ez a szakasz azt ismerteti, hogyan futtathatom az Apache Cordova alkalmazásprojektet Windows 10-es eszközökön (a PhoneGap leküldéses bővítmény támogatott a Windows 10-en). Ha nem Windows-eszközökkel dolgozik, kihagyhatja ezt a szakaszt.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Windows-alkalmazás regisztrálása leküldéses értesítésekhez a WNS-ben

A Visual Studio Áruház beállításainak használatához válasszon ki egy Windows-célt a Megoldásplatformok listából, például **Windows-x64** vagy **Windows-x86**. (Kerülje a **Windows-AnyCPU** leküldéses értesítéseket.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Tekintse meg hasonló lépéseket ismertető videót][13]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS értesítési központjának konfigurálása

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Cordova alkalmazás konfigurálása a Windows leküldéses értesítések támogatásához

A konfigurációtervező megnyitása a jobb gombbal a **config.xml**fájlra kattintva. Ezután válassza **a Tervező megtekintése**lehetőséget. Ezután válassza a **Windows** lapot, majd a **Windows 10** lehetőséget a **Windows célverzió alatt.**

Az alapértelmezett (hibakeresési) buildekben lévő leküldéses értesítések támogatásához nyissa meg a **build.json** fájlt. Ezután másolja a "release" konfigurációt a hibakeresési konfigurációba.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

A frissítés után a **build.json** fájlnak a következő kódot kell tartalmaznia:

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

Készítse el az alkalmazást, és ellenőrizze, hogy nincsenek-e hibák. Az ügyfélalkalmazásnak most regisztrálnia kell a mobilalkalmazások háttérrendszeréről érkező értesítésekre. Ismételje meg ezt a szakaszt a megoldás minden Windows-projektjéhez.

#### <a name="test-push-notifications-in-your-windows-app"></a>Leküldéses értesítések tesztelése a Windows alkalmazásban

A Visual Studio alkalmazásban győződjön meg arról, hogy egy Windows-platform van kijelölve telepítési célként, például **Windows-x64** vagy **Windows-x86**. Ha az alkalmazást a Visual Studio alkalmazást üzemeltető Windows 10-es számítógépen szeretné futtatni, válassza a **Helyi számítógép**lehetőséget.

1. A **Projekt** létrehozásához és az alkalmazás elindításához válassza a Futtatás gombot.

2. Az alkalmazásban írja be egy új todoelem nevét, majd a plusz **(+)** ikonra a hozzáadásához.

Ellenőrizze, hogy az elem hozzáadásakor érkezik-e értesítés.

## <a name="next-steps"></a><a name="next-steps"></a>További lépések

* Olvassa el [az Értesítési központokat][17] a leküldéses értesítésekről.
* Ha még nem tette meg, folytassa az oktatóanyagot az Apache Cordova alkalmazás [hitelesítésének hozzáadásával.][14]

Ismerje meg a következő SDK-k használatát:

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
