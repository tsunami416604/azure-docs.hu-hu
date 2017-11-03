---
title: "Leküldéses értesítések hozzáadása az Azure Mobile Apps Apache Cordova-alkalmazás |} Microsoft Docs"
description: "Megtudhatja, hogyan küldhet leküldéses értesítéseket az Apache Cordova-alkalmazás Azure Mobile Apps segítségével."
services: app-service\mobile
documentationcenter: javascript
manager: syntaxc4
editor: 
author: ggailey777
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: dc3cab0a6a8b4a56ab0fba1a02e5bba9d0ed1b1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Leküldéses értesítések hozzáadása az Apache Cordova-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban meg leküldéses értesítések hozzáadása a [Apache Cordova gyors üzembe helyezési] projekt, hogy egy leküldéses értesítést küld az eszköz minden alkalommal, amikor egy olyan rekordot csatlakoztatva van.

Ha nem használja a letöltött gyors üzembe helyezési projekt, a leküldéses értesítési kiterjesztési csomagot kell. További információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a][1].

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag hozzá van rendelve egy, a Visual Studio 2015-öt a Google Android Emulator, Android-eszközön, a Windows-eszköz és az iOS-eszközök futó létrehozott Apache Cordova-alkalmazást.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Számítógép, amelyen fut [Visual Studio Community 2015] [ 2] vagy újabb verzió.
* [Visual Studio Tools for Apache Cordova][4].
* Egy [aktív Azure-fiók][3].
* A befejezett [Apache Cordova gyors üzembe helyezési] [ 5] projekt.
* (Android) A [Google-fiók] [ 6] egy hitelesített e-mail címmel.
* (csak iOS) Egy [Apple Fejlesztőprogrambeli tagság] [ 7] és egy iOS-eszközön (iOS-szimulátor nem támogatja a leküldéses).
* (Windows) A [Windows áruház fejlesztői fiókjába] [ 8] és a Windows 10-eszközre.

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Ebben a szakaszban lépéseket bemutató videó megtekintése][9]

## <a name="update-the-server-project"></a>Frissítés a kiszolgáló-projekt
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Módosítsa a Cordova-alkalmazáshoz
Győződjön meg arról, a Apache Cordova-alkalmazás projekt készen áll a leküldéses értesítések kezeléséhez a Cordova leküldéses beépülő modul és a platform-specifikus leküldéses szolgáltatások telepítésével.

#### <a name="update-the-cordova-version-in-your-project"></a>Frissítse a Cordova-verzió a projektben.
A projekt Apache Cordova v6.1.1-nél korábbi verzióját használja, ha az ügyfél projekt frissítése. A projekt frissítése:

* Kattintson a jobb gombbal `config.xml` a configuration designer megnyitásához.
* Kattintson a platformok fülre.
* Válassza ki a 6.1.1 a **Cordova CLI** szövegmezőben.
* Válasszon **Build**, majd **megoldás fordítása** a projekt frissítéséhez.

#### <a name="install-the-push-plugin"></a>A leküldéses beépülő modul telepítése
Apache Cordova-alkalmazás natív módon nem kezeli a eszköz vagy a hálózatkezelő képességeit.  Ezek a képességek által biztosított beépülő modulok, amelyek közzé vagy [npm] [ 10] vagy a Githubon.  A `phonegap-plugin-push` beépülő modul hálózati leküldéses értesítések kezelésére használt.

A leküldéses beépülő modul az alábbi módszerek egyikével telepítheti:

**A parancssorból:**

Hajtsa végre a következő parancsot:

    cordova plugin add phonegap-plugin-push

**A Visual studióban:**

1. A Solution Explorerben nyissa meg a `config.xml` fájl kattintson **beépülő modulok** > **egyéni**, jelölje be **Git** telepítési forrásként, majd adja meg `https://github.com/phonegap/phonegap-plugin-push`forrásaként.

   ![][img1]

2. A telepítési forrás melletti nyílra.
3. A **SENDER_ID**, ha már telepítette a Google Developer Console projekt numerikus Projektazonosítónak, hozzáadhatja azt itt. Ellenkező esetben adja meg a helyőrző érték, például 777777.  Android céloz meg, ha ezt az értéket config.xml később frissítheti.
4. Kattintson az **Add** (Hozzáadás) parancsra.

A leküldéses beépülő modul telepítve van.

#### <a name="install-the-device-plugin"></a>Az eszköz beépülő modul telepítése
Kövesse ugyanezt az eljárást a leküldéses beépülő modul telepítéséhez is használt.  Az eszköz beépülő modul hozzáadása a Core beépülő modulok listában (kattintson **beépülő modulok** > **Core** azt található). Ez a platform nevének megszerzése beépülő van szüksége.

#### <a name="register-your-device-on-application-start-up"></a>Regisztrálja az eszközt, az alkalmazás indítási
Kezdetben magában foglalja az egyes minimális kód Android rendszerhez. Később módosítsa az alkalmazás iOS-vagy Windows 10 futtatásához.

1. Adjon hozzá egy **registerForPushNotifications** a visszahívást, vagy alján a bejelentkezési folyamat során a **onDeviceReady** módszert:

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    Ez a példa bemutatja hívása **registerForPushNotifications** sikeres hitelesítést követően.  Hívása `registerForPushNotifications()` gyakran szükség.

2. Adja hozzá az új **registerForPushNotifications** módszert az alábbiak szerint:

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
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
3. (Android) Cserélje le az előzőekben látható kód `Your_Project_ID` numerikus project azonosítója a az alkalmazás a [Google Developer Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Választható) Konfigurálja és az Android-alkalmazás futtatása
Ez a szakasz az Android leküldéses értesítések engedélyezéséhez végezze el.

#### <a name="enable-gcm"></a>Engedélyezze a Firebase Cloud Messaging
Mivel jelenleg céloz meg a Google Android platform kezdetben, engedélyeznie kell a Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>A Mobile Apps-háttéralkalmazás segítségével FCM leküldéses kérelmek küldésére konfigurálása
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurálja a Cordova-alkalmazás Android rendszerhez
A Cordova-alkalmazáshoz, nyissa meg a config.xml, és cserélje le `Your_Project_ID` numerikus project azonosítója a az alkalmazás a [Google Developer Console][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Nyissa meg a index.js, és frissítse a kódot, és használja a numerikus projekt ID azonosítójával.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Android-eszköz az USB-hibakeresés konfigurálása
Az alkalmazás Android-eszköz telepítése előtt kell USB-hibakeresés engedélyezése.  Androidos telefonja hajtsa végre a következő lépéseket:

1. Lépjen **beállítások** > **névjegye**, majd koppintson a **buildszáma** mindaddig, amíg a fejlesztői mód engedélyezve van (körülbelül hét alkalommal).
2. Vissza a **beállítások** > **fejlesztői beállítások** engedélyezése **USB-hibakeresés**, Androidos telefonja majd kapcsolódjon a fejlesztési számítógép USB-kábellel.

Tesztelt Ez egy Android 6.0 (Marshmallow) rendszert futtató Google Nexus 5 X-eszköz használatával.  A módszerek azonban által közösen bármelyik modern Android verzióját.

#### <a name="install-google-play-services"></a>Google Play-szolgáltatások telepítése
A leküldéses beépülő modul az Android Google Play-szolgáltatások a leküldéses értesítések támaszkodik.

1. A Visual Studióban kattintson **eszközök** > **Android** > **Android SDK Manager**, bontsa ki a **kiegészítő funkciók** mappa, és ellenőrizze a jelölőnégyzetet, győződjön meg arról, hogy a következő SDK-k mindegyikének telepítve van.

   * Android 2.3-as vagy újabb
   * Google-tárház változat 27 vagy nagyobb
   * Google Play-szolgáltatásokra 9.0.2 vagy újabb

2. Kattintson a **csomagok telepítése** és várja meg a telepítés befejezéséhez.

Az aktuális szükséges kódtárak láthatók a [phonegap-beépülőmodul-leküldéses telepítési dokumentáció][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Teszt leküldéses értesítések Android rendszeren az alkalmazásban
Most teszt leküldéses értesítések segítségével az alkalmazás fut, és a TodoItem tábla elemek beszúrása. Tesztelheti egy második eszköz, vagy ugyanarra az eszközre, amíg az azonos háttér használata. Az Android platformon a Cordova-alkalmazás tesztelése a következő módszerek valamelyikével:

* **A fizikai eszközön:** a fejlesztési számítógép USB-kábellel csatlakoztassa az Android-eszköz.  Ahelyett, hogy **Google Android Emulator**, jelölje be **eszköz**. Visual Studio telepíti az alkalmazást az eszközre, és majd futtatja az alkalmazást.  Ezután kommunikálhat az alkalmazást az eszközön.

  A fejlesztési felhasználói élmény javítása.  A képernyő megosztása például alkalmazások [Mobizen] [ 20] segíthet az Android alkalmazások fejlesztése.  Mobizen projekt az Android egy webes böngésző képernyőn a számítógépen.

* **Az Android-emulátorban:** az emulátor futtatásához szükséges további konfigurációs lépésekre.

    Ellenőrizze, hogy a virtuális eszköz, amely rendelkezik a Google API-k, célként beállítva, ahogy az az Android virtuális eszközt (AVD) kezelő telepíti.

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Ha szeretné használni a gyorsabb x86 emulátor, akkor [a HAXM illesztőprogram telepítése] [ 11] , és konfigurálja az emulátorban történő használatát.

    A Google-fiók kattintva vegyen fel új Android-eszköz **alkalmazások** > **beállítások** > **fiók hozzáadása**, majd kövesse a megjelenő utasításokat.

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Futtassa a todolist alkalmazást előtt, és egy új teendő elem beszúrása. Ebben az esetben egy értesítési ikon jelenik meg az értesítési területen. Úgy is megnyithatja az értesítési fiókot, az értesítés a teljes szöveg megtekintéséhez.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Választható) Konfigurálására és futtatására IOS rendszerű eszközökön
Ez a szakasz az iOS-eszközök a Cordova-projekt futtatása. Ha nem dolgozik iOS-eszközökkel, ez a szakasz kihagyhatja.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Telepítse, és futtassa az iOS távoli build ügynök Mac vagy felhőalapú szolgáltatásként
A Cordova-alkalmazást a Visual Studio használatával iOS futtatása előtt halad át a lépéseket a [iOS beállítási útmutatója] [ 12] telepíteni és futtatni a távoli build ügynök.

Győződjön meg arról, hogy az IOS-alkalmazást hozhat létre. A telepítő útmutató az IOS rendszerhez készült Visual Studio build szükségesek. Ha nem rendelkezik a Mac, az IOS rendszerhez készült hasonló MacInCloud szolgáltatás távoli build-ügynök használatával hozhat létre. További információk: [az iOS-alkalmazás futtatása a felhőben][21].

> [!NOTE]
> XCode 7 vagy újabb IOS rendszerű eszközökön a leküldéses beépülő modul használatához szükséges.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Keresse meg az Alkalmazásazonosító használandó Azonosítóját
Leküldéses értesítések, nyitott config.xml a Cordova-alkalmazáshoz, az alkalmazás regisztrálása előtt található a `id` attribútum értéke a widget elemben, és másolja azt a későbbi használatra. A következő XML-kódban, az azonosító: `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Ez az azonosító újabb verzióival való használatához, amikor egy alkalmazás azonosítója hoz létre az Apple fejlesztői portáljáról. Ha egy másik alkalmazás Azonosítót hoz létre a fejlesztői portálján, néhány további lépéseket kell végrehajtania az oktatóanyag későbbi részében. A widget elem Azonosítóját meg kell egyeznie az alkalmazás Azonosítóját a developer portálon.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Az alkalmazás az Apple fejlesztői portálján a leküldéses értesítések regisztrálása
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Leküldéses értesítések küldéséhez Azure konfigurálása
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Győződjön meg arról, hogy az alkalmazás azonosítója egyezik-e a Cordova-alkalmazáshoz
Ha az Alkalmazásazonosító már létrehozott az Apple fejlesztői fiókban lévő config.xml widget elem azonosítója megegyezik, kihagyhatja ezt a lépést. Azonban ha az azonosító nem egyezik, a következő lépéseket:

1. Törölje a platformok mappát a projektből.
2. Törölje a beépülő modulok mappát a projektből.
3. Törölje a node_modules mappát a projektből.
4. Az id attribútum használata az alkalmazás azonosítója, amelyet az Apple fejlesztői fiókban hozott létre config.xml widget elemének frissítéséhez.
5. A projekt újraépítéséhez.

##### <a name="test-push-notifications-in-your-ios-app"></a>Teszt leküldéses értesítéseket az iOS-alkalmazás
1. Visual Studio, ügyeljen arra, hogy **iOS** központi telepítés céljaként van kiválasztva, és válassza a **eszköz** futtatásához a csatlakoztatott iOS-eszközön.

    A Számítógéphez csatlakoztatott iOS-eszközön futtathatja, iTunes használatával. Az iOS-szimulátor nem támogatja a leküldéses értesítéseket.

2. Nyomja meg a **futtatása** gomb vagy **F5** a Visual Studio a projekt felépítéséhez és az iOS-eszközön indítsa el az alkalmazást, majd kattintson a **OK** leküldéses értesítések fogadásához.

   > [!NOTE]
   > Leküldéses értesítések első megerősítést kér az alkalmazás.

3. Az alkalmazásban írjon be egy feladatot, és kattintson a plusz (+) ikonra.
4. Győződjön meg arról, hogy értesítést kap, majd kattintson az OK gombra az értesítés bezárásának engedélyezése.

## <a name="optional-configure-and-run-on-windows"></a>(Választható) Konfigurálja és futtassa a Windows
Ez a szakasz az Apache Cordova-alkalmazás projekt futó Windows 10-eszközök (a PhoneGap leküldéses beépülő modul támogatja a Windows 10) van. Ha nem dolgozik Windows-eszközökkel, ez a szakasz kihagyhatja.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Alkalmazás regisztrálása a Windows leküldéses értesítéseket a WNS-sel való
A tároló beállításait használja a Visual Studio alkalmazásban, válassza a Windows célja a megoldás platformok listája, például **Windows-x64** vagy **Windows-x86** (elkerülése érdekében **Windows-AnyCPU** számára leküldéses értesítések).

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Egy hasonló lépéseket bemutató videó][13]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS az értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>A Cordova-alkalmazás támogatja a Windows leküldéses értesítéseket konfigurálása
Nyissa meg a configuration designer (kattintson a jobb gombbal a config.xml, és válassza **adatforrásnézet-tervezőből**) elemre, jelölje be a **Windows** lapot, és válassza a **Windows 10** alatt  **Windows verzió cél**.

Támogatja a leküldéses értesítések az alapértelmezett (hibakereséshez) létrehozta, nyitott build.json fájl. Másolja át a "release" konfigurációs hibakeresési konfigurációjához.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

A frissítés után a build.json tartalmaznia kell a következő kódot:

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

Az alkalmazás elkészítésére, és ellenőrizze, hogy rendelkezik-e hibák. Az ügyfélalkalmazás most regisztrálni kell a Mobile Apps-háttéralkalmazás értesítéseihez. Ismételje meg minden Windows-projektet a megoldásban ez a szakasz.

#### <a name="test-push-notifications-in-your-windows-app"></a>Teszt leküldéses értesítések a Windows-alkalmazásokban
A Visual Studio, győződjön meg arról, hogy a Windows platform van kiválasztva a telepítés céljaként például **Windows-x64** vagy **Windows-x86**. Futtassa az alkalmazást a Visual Studio üzemeltető Windows 10 rendszerű Számítógépeken, válassza a **helyi számítógép**.

A Futtatás gombra a projekt felépítéséhez és az alkalmazás indításához.

Az alkalmazásban írjon be egy nevet az új beállíthatnánk, és kattintson a plusz (+) ikonra kattintva vegye fel azt.

Győződjön meg arról, hogy értesítést kapott, a cikk felvételekor.

## <a name="next-steps"></a>Következő lépések
* További információ a [Notification Hubs] [ 17] leküldéses értesítések tájékozódhat.
* Ha még nem tette meg, továbbra is az oktatóanyag által [hozzáadása hitelesítési] [ 14] az Apache Cordova-alkalmazáshoz.

Útmutató az SDK-k használatához.

* [Apache Cordova-SDK][15]
* [ASP.NET Server SDK][1]
* [NODE.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/
