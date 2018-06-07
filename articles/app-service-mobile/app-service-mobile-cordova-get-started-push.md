---
title: Leküldéses értesítések hozzáadása az Apache Cordova-alkalmazás az Azure App Service Mobile Apps szolgáltatással |} Microsoft Docs
description: Megtudhatja, hogyan küldhet leküldéses értesítéseket az Apache Cordova-alkalmazást a Mobile Apps segítségével.
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
ms.openlocfilehash: 13c1a53cfa3f998c9e3fa3ee1ee2dcec37357095
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597994"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Leküldéses értesítések hozzáadása az Apache Cordova-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban leküldéses értesítések hozzáadása a [Apache Cordova gyors üzembe helyezés] [ 5] projektre, hogy egy leküldéses értesítést küld az eszköz minden alkalommal, amikor egy olyan rekordot csatlakoztatva van.

Ha nem használja a letöltött gyorsútmutató-projekt, akkor a leküldéses értesítési kiterjesztési csomag. További információkért lásd: [használható a .NET SDK háttér-kiszolgáló az a Mobile Apps][1].

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy rendelkezik-e egy Apache Cordova-alkalmazás kifejlesztett a Visual Studio 2015-öt. Ez az eszköz a Google Android Emulator, Android-eszközön, a Windows-eszköz vagy az iOS-eszközök kell futtatnia.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Számítógép, amelyen fut [Visual Studio Community 2015] [ 2] vagy újabb verzió
* [Apache Cordova Visual Studio eszközök][4]
* Egy [aktív Azure-fiók][3]
* A befejezett [Apache Cordova gyors üzembe helyezés] [ 5] projekt
* (Android) A [Google-fiók] [ 6] egy hitelesített e-mail címmel
* (csak iOS) Egy [Apple Fejlesztőprogrambeli tagság] [ 7] és egy iOS-eszközön (iOS-szimulátor nem támogatja a leküldéses értesítések)
* (Windows) A [Microsoft áruház fejlesztői fiókjába] [ 8] és egy Windows 10 rendszerű eszköz

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Ebben a szakaszban lépéseket bemutató videót][9].

## <a name="update-the-server-project"></a>Frissítés a kiszolgáló-projekt
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Módosítsa a Cordova-alkalmazáshoz
Győződjön meg arról, hogy leküldéses értesítések kezeléséhez készen áll-e a Apache Cordova-alkalmazás projekt, telepítse a Cordova leküldéses beépülő modul és a platform-specifikus leküldéses szolgáltatások.

#### <a name="update-the-cordova-version-in-your-project"></a>Frissítse a Cordova-verzió a projektben.
A projekt egy Apache Cordova 6.1.1 verziónál korábbi verzióját használja, ha az ügyfél projekt frissítése. A projekt frissítéséhez tegye a következőket: 

* A configuration designer megnyitásához kattintson a jobb gombbal `config.xml`.
* Válassza ki a **platformok** fülre.
* Az a **Cordova CLI** szövegmezőben, jelölje be **6.1.1**. 
* Válassza ki a projekt frissítéséhez **Build**, majd válassza ki **megoldás fordítása**.

#### <a name="install-the-push-plugin"></a>A leküldéses beépülő modul telepítése
Apache Cordova-alkalmazás natív módon nem kezeli a eszköz vagy a hálózatkezelő képességeit.  Ezek a képességek által biztosított beépülő modulok, amelyek közzé vagy [npm] [ 10] vagy a Githubon. A `phonegap-plugin-push` beépülő modul kezeli a hálózati leküldéses értesítéseket.

A leküldéses beépülő modul a következő módokon telepítheti:

**A parancssorból:**

Futtassa az alábbi parancsot:

    cordova plugin add phonegap-plugin-push

**A Visual studióban:**

1. A Solution Explorerben nyissa meg a `config.xml` fájlt. Válassza ki, **beépülő modulok** > **egyéni**. Válassza ki **Git** telepítési forrásként. 
    
2. Adja meg `https://github.com/phonegap/phonegap-plugin-push` forrásaként.

    ![Nyissa meg a config.xml fájlt a Megoldáskezelőben][img1]

3. Válassza ki a telepítési forrás melletti nyílra.

4. A **SENDER_ID**, ha már telepítette a Google Developer Console projekt numerikus Projektazonosítónak, hozzáadhatja azt itt. Ellenkező esetben adja meg a helyőrző értékét, például 777777. Android céloz meg, ha ezt az értéket a config.xml fájl később frissítheti.

    >[!NOTE]
    >Től verzió 2.0.0 google-services.json telepítve kell lennie a gyökérmappában található, a projekt konfigurálásához a küldő azonosítója. További információkért lásd: a [dokumentáció.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)
5. Válassza a **Hozzáadás** lehetőséget.

A leküldéses beépülő modul telepítve van.

#### <a name="install-the-device-plugin"></a>Az eszköz beépülő modul telepítése
Kövesse ugyanezt az eljárást a leküldéses beépülő modul telepítéséhez is használt. Az eszköz beépülő modul hozzáadása a Core beépülő modulok listában. (Található, jelölje be **beépülő modulok** > **Core**.) Ez a platform nevének megszerzése beépülő van szüksége.

#### <a name="register-your-device-when-the-application-starts"></a>Regisztrálja az eszközt, ha az alkalmazás indítása 
Kezdetben magában foglalja az egyes minimális kód Android rendszerhez. Az alkalmazás futtatását iOS vagy Windows 10 később módosíthatja.

1. Adjon hozzá egy **registerForPushNotifications** a visszahívás a bejelentkezési folyamat során. Másik lehetőségként felveheti alján a **onDeviceReady** módszert:

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

    Ez a példa bemutatja hívása **registerForPushNotifications** sikeres hitelesítést követően. Hívása `registerForPushNotifications()` gyakran szükség.

2. Adja hozzá az új **registerForPushNotifications** módszert az alábbiak szerint:

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
3. (Android) Cserélje le az előzőekben látható kód `Your_Project_ID` numerikus project azonosítója a az alkalmazás a [Google Developer Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Választható) Konfigurálja és az Android-alkalmazás futtatása
Ez a szakasz az Android leküldéses értesítések engedélyezéséhez végezze el.

#### <a name="enable-gcm"></a>Engedélyezze a Firebase Cloud Messaging
Mivel a Google Android platform kezdetben céloz, engedélyeznie kell a Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>A Mobile Apps háttér FCM használatával leküldéses kérelmek küldésére konfigurálása
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurálja a Cordova-alkalmazás Android rendszerhez
Nyissa meg a Cordova-alkalmazáshoz config.xml. Ezután cserélje le `Your_Project_ID` numerikus project azonosítója a az alkalmazás a [Google Developer Console][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Nyissa meg a index.js. Ezután frissítse a kódot, és használja a numerikus projekt ID azonosítójával.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Android-eszköz az USB-hibakeresés konfigurálása
Az alkalmazás Android-eszköz telepítése előtt kell USB-hibakeresés engedélyezése. A következő lépéseket az Android-telefonon:

1. Ugrás a **beállítások** > **névjegye**. Koppintson a **buildszáma** mindaddig, amíg a fejlesztői mód engedélyezve van (körülbelül hét alkalommal).
2. Vissza a **beállítások** > **fejlesztői beállítások**, engedélyezése **USB-hibakeresés**. Androidos telefonja majd kapcsolódjon a fejlesztési számítógép USB-kábellel.

Tesztelt Ez egy Android 6.0 (Marshmallow) rendszert futtató Google Nexus 5 X-eszköz használatával. A módszerek azonban által közösen bármelyik modern Android verzióját.

#### <a name="install-google-play-services"></a>Google Play-szolgáltatások telepítése
A leküldéses beépülő modul az Android Google Play-szolgáltatások a leküldéses értesítések támaszkodik.

1. A Visual Studio válassza **eszközök** > **Android** > **Android SDK Manager**. Ezután bontsa ki a **kiegészítő funkciók** mappát. A megfelelő jelölőnégyzeteket győződjön meg arról, hogy a következő SDK-k mindegyikének telepítve van:

   * Android 2.3-as vagy újabb
   * Google-tárház változat 27 vagy nagyobb
   * Google Play-szolgáltatásokra 9.0.2 vagy újabb

2. Válassza ki **csomagok**. Várjon, amíg a telepítés befejezéséhez.

Az aktuális szükséges kódtárak láthatók a [phonegap-beépülőmodul-leküldéses telepítési dokumentáció][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Teszt leküldéses értesítések Android rendszeren az alkalmazásban
Most teszt leküldéses értesítések segítségével az alkalmazás fut, és a TodoItem tábla elemek beszúrása. Mindaddig, amíg az azonos háttér használata tesztelheti egy második eszköz, vagy ugyanarra az eszközre. Az Android platformon a Cordova-alkalmazás tesztelése a következő módszerek valamelyikével:

* *A fizikai eszközön:* a fejlesztési számítógép USB-kábellel csatlakoztassa az Android-eszköz.  Ahelyett, hogy **Google Android Emulator**, jelölje be **eszköz**. Visual Studio telepíti az alkalmazást az eszközre, és futtatja az alkalmazást. Ezután kommunikálhat az alkalmazást az eszközön.

  Képernyő-megosztási alkalmazások, mint például [Mobizen] [ 20] is segítséget nyújt a Android-alkalmazások fejlesztésével. Mobizen projekt az Android egy webes böngésző képernyőn a számítógépen.

* *Az Android-emulátorban:* további konfigurációs lépésekre, amelyek szükségesek az emulátor használatakor.

    Ellenőrizze, hogy a virtuális eszköz, amely rendelkezik a Google API-k, célként beállítva, ahogy az az Android virtuális eszközt (AVD) kezelő telepíti.

    ![Android virtuális eszközt kezelője](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Ha szeretné használni a gyorsabb x86 emulátor, [a HAXM illesztőprogram telepítése][11], majd adja meg az emulátorban történő használatát.

    A Google-fiók hozzáadása az Android-eszközön kiválasztásával **alkalmazások** > **beállítások** > **fiók hozzáadása**. Kövesse az utasításokat.

    ![A Google-fiók hozzáadása az Android-eszközön](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Futtassa a todolist alkalmazást előtt, és egy új teendő elem beszúrása. Ebben az esetben egy értesítési ikon jelenik meg az értesítési területen. Úgy is megnyithatja az értesítési fiókot, az értesítés a teljes szöveg megtekintéséhez.

    ![értesítési nézet](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Választható) Konfigurálására és futtatására IOS rendszerű eszközökön
Ez a szakasz az iOS-eszközök a Cordova-projekt futtatása. Ha nem dolgozik iOS-eszközök, ez a szakasz kihagyhatja.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Telepítse, és futtassa az iOS távoli build ügynök Mac vagy felhőalapú szolgáltatásként
A Cordova-alkalmazást a Visual Studio használatával iOS futtatása előtt halad át a lépéseket a [iOS beállítási útmutató] [ 12] telepíteni és futtatni a távoli build ügynök.

Győződjön meg arról, hogy az IOS-alkalmazást hozhat létre. A telepítő útmutató szükségesek az IOS-alkalmazás létrehozása a Visual Studio eszközből. Ha nem rendelkezik a Mac, hozhat létre iOS rendszerhez készült hasonló MacInCloud szolgáltatás a távoli build ügynök használatával. További információkért lásd: [az iOS-alkalmazás futtatása a felhőben][21].

> [!NOTE]
> XCode 7 vagy újabb IOS rendszerű eszközökön a leküldéses beépülő modul használatához szükséges.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Keresse meg az Alkalmazásazonosító használandó Azonosítóját
Leküldéses értesítések, nyitott config.xml a Cordova-alkalmazáshoz, az alkalmazás regisztrálása előtt található a `id` attribútum értéke a widget elemben, és másolja azt későbbi használatra. A következő XML-kódban, az azonosító: `io.cordova.myapp7777777`.

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
Ha az alkalmazás Azonosítóját, amely az Apple Developer-fiók már létrehozott a config.xml fájl widget elemének azonosítója megegyezik, kihagyhatja ezt a lépést. Azonban ha az azonosító nem egyezik, a következő lépéseket:

1. Törölje a platformok mappát a projektből.
2. Törölje a beépülő modulok mappát a projektből.
3. Törölje a node_modules mappát a projektből.
4. Az id attribútum használata az alkalmazás azonosítója, amelyet az Apple fejlesztői fiókban hozott létre a config.xml fájlban widget elem frissítése.
5. A projekt újraépítéséhez.

##### <a name="test-push-notifications-in-your-ios-app"></a>Teszt leküldéses értesítéseket az iOS-alkalmazás
1. Visual Studio, ügyeljen arra, hogy **iOS** központi telepítés céljaként van kiválasztva. Válassza ki **eszköz** csatlakoztatott iOS-eszközön a leküldéses értesítések futtatásához.

    A leküldéses értesítések iOS-eszközön, amely kapcsolódik a Számítógéphez, az iTunes futtatása. Az iOS-szimulátor nem támogatja a leküldéses értesítéseket.

2. Válassza ki a **futtatása** gomb vagy **F5** a projekt felépítéséhez és az alkalmazás elindításához az iOS-eszközök Visual Studio. Válassza ki **OK** leküldéses értesítések fogadásához.

   > [!NOTE]
   > Leküldéses értesítések első megerősítést kér az alkalmazás.

3. Az alkalmazásban írjon be egy feladatot, majd válassza ki a plusz **(+)** ikonra.
4. Győződjön meg arról, hogy egy értesítést fogadott a rendszer. Válassza ki **OK** az értesítés bezárásának engedélyezése.

## <a name="optional-configure-and-run-on-windows"></a>(Választható) Konfigurálja és futtassa a Windows
Ez a szakasz ismerteti az Apache Cordova-alkalmazás projekt futtatásáról a Windows 10-eszközök (a PhoneGap leküldéses beépülő modul támogatja a Windows 10). Ha nem dolgozik Windows-eszközökkel, ez a szakasz kihagyhatja.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Alkalmazás regisztrálása a Windows leküldéses értesítéseket a WNS-sel való
A tároló beállításait használja a Visual Studio alkalmazásban, válassza a Windows célja a megoldás platformok listája, például **Windows-x64** vagy **Windows-x86**. (Elkerülése érdekében **Windows-AnyCPU** leküldéses értesítésekre.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Egy hasonló lépéseket bemutató videó][13]

#### <a name="configure-the-notification-hub-for-wns"></a>A WNS az értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>A Cordova-alkalmazás támogatja a Windows leküldéses értesítéseket konfigurálása
A configuration designer megnyitásához kattintson a jobb gombbal **config.xml**. Válassza ki **adatforrásnézet-tervezőből**. Ezután válassza ki a **Windows** lapra, majd válassza ki **Windows 10** alatt **Windows célverzió**.

Az alapértelmezett (hibakereséshez) leküldéses értesítések támogatásához használható alkot, nyissa meg a build.json fájlt. Ezután másolja a "release" konfigurációs hibakeresési konfigurációjához.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

A frissítés után a build.json kell tartalmaznia a következő kódot:

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

Az alkalmazás elkészítésére, és ellenőrizze, hogy rendelkezik-e hibák. Az ügyfélalkalmazás most regisztrálni kell az értesítések a Mobile Apps háttérből. Ismételje meg minden Windows-projektet a megoldásban ez a szakasz.

#### <a name="test-push-notifications-in-your-windows-app"></a>Teszt leküldéses értesítések a Windows-alkalmazásokban
A Visual Studio, győződjön meg arról, hogy a Windows platform van kiválasztva a telepítés céljaként például **Windows-x64** vagy **Windows-x86**. Futtassa az alkalmazást a Visual Studio futtató Windows 10 rendszerű Számítógépeken, válassza a **helyi számítógép**.

1. Válassza ki a **futtatása** gombot a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást.

2. Az alkalmazásban írjon be egy új todoitem nevét, majd válassza ki a plusz **(+)** ikonra kattintva vegye fel azt.

Győződjön meg arról, hogy értesítést kapott, a cikk felvételekor.

## <a name="next-steps"></a>Következő lépések
* További információ a [Notification Hubs] [ 17] leküldéses értesítések tájékozódhat.
* Ha még nem tette meg, továbbra is az oktatóanyag által [hitelesítés hozzáadása] [ 14] az Apache Cordova-alkalmazáshoz.

Megtudhatja, hogyan használja a következő SDK-IT:

* [Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Node.js Server SDK][16]

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
