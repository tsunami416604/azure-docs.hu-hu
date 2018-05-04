---
title: Apache Cordova beépülő modul használata az Azure Mobile Apps szolgáltatásban
description: Apache Cordova beépülő modul használata az Azure Mobile Apps szolgáltatásban
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 6fb8be96c9793e96f1f7d2ad8e212d056d7e9ba5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Az Azure Mobile Apps Apache Cordova ügyféloldali kódtár használata
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ez az útmutató útmutatást ad teszi a végrehajtását szolgáltatást a legújabb használó általános forgatókönyvhöz [Apache Cordova beépülő modul az Azure Mobile Apps]. Ha most ismerkedik az Azure Mobile Apps, először végezzen [Azure Mobile Apps gyors üzembe helyezés] -háttéralkalmazás létrehozása, hozzon létre egy táblát, és egy előre elkészített Apache Cordova-projekt letöltése. Az útmutató azt összpontosítanak az ügyféloldali Apache Cordova beépülő modul is.

## <a name="supported-platforms"></a>Támogatott platformok
Ez az SDK támogatja az Apache Cordova v6.0.0 és későbbi iOS, Android és Windows eszközökhöz.  A platform támogatása a következőképpen történik:

* Android API 19-24 (KitKat nugát keresztül).
* iOS 8.0-s és újabb verziók.
* Windows Phone 8.1-es.
* Az univerzális Windows Platform.

## <a name="Setup"></a>A telepítő és Előfeltételek
Ez az útmutató feltételezi, hogy létrehozott egy táblát a háttérkiszolgálón. Ez az útmutató feltételezi, hogy rendelkezik-e a tábla a táblák ugyanazon séma ezen oktatóprogram a. Ez az útmutató feltételezi, hogy hozzáadta az Apache Cordova beépülő modul a kódot.  Ha nem tette, előfordulhat, hogy adja hozzá az Apache Cordova beépülő modul a projekthez a parancssorban:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

További létrehozásával kapcsolatos információkat [az első Apache Cordova-alkalmazás], azok dokumentációjában talál.

## <a name="ionic"></a>Ionos v2 alkalmazás beállítása

Megfelelően konfigurálni ionos v2 projektben, hozzon létre egy alapszintű alkalmazást, majd adja hozzá a Cordova beépülő modul:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Adja hozzá a következő sorokat `app.component.ts` az ügyfél objektum létrehozásához:

```
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Most már létrehozhatja és a projekt futtatását a böngészőben:

```
ionic platform add browser
ionic run browser
```

Az Azure Mobile Apps Cordova beépülő modul mindkét ionos v1 és v2 alkalmazásokat támogatja.  Csak az ionos v2 alkalmazásokhoz szükséges további deklaráció található a `WindowsAzure` objektum.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Hogyan: hitelesíti a felhasználókat
Az Azure App Service támogat hitelesítése és engedélyezése a felhasználók alkalmazás különböző külső Identitásszolgáltatók: Facebook, Google, a Microsoft Account és Twitter. A engedélyeket korlátozhatja a hozzáférést a megadott művelet csak a hitelesített felhasználók táblákon. Hitelesített felhasználók identitásának használhatja, ha az engedélyezési szabályok megvalósítását a kiszolgáló parancsfájlokat. További információkért lásd: a [Bevezetés a hitelesítés használatába] oktatóanyag.

Ha hitelesítési Apache Cordova-alkalmazást használ, a következő Cordova beépülő modulok elérhetőnek kell lennie:

* [cordova-beépülőmodul-eszköz]
* [cordova-beépülőmodul-inappbrowser]

Két hitelesítési forgalom támogatottak: a kiszolgáló folyamata és egy ügyfél folyamatában.  A kiszolgáló folyamata nyújt a legegyszerűbb felhasználói hitelesítés, a szolgáltató webes hitelesítés felület támaszkodnak. Az ügyféltanúsítvány-folyamat lehetővé teszi, hogy szorosabb integrációt eszközspecifikus képességeket például single-sign-on, szolgáltatói eszközspecifikus SDK-k támaszkodnak az.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Útmutató: a Mobile App Service konfigurálása külső átirányítási URL-címeket.
Az Apache Cordova-alkalmazások számos különböző egy visszacsatolási funkció segítségével OAuth UI-forgalom kezelésére.  OAuth UI adatfolyamok – localhost problémákat okozhat, mert a hitelesítési szolgáltatás csak tudja, hol használják a szolgáltatás alapértelmezés szerint.  Hibás OAuth UI flow például a következők:

* A Fodrozás emulátor.
* Élő rendelkező ionos be újra.
* A mobil-háttéralkalmazást helyileg futó
* A mobil-háttéralkalmazást fut egy másik Azure App Service, mint a egy olyan hitelesítési.

Kövesse ezeket az utasításokat a helyi beállításokat hozzáadni a konfigurációhoz:

1. Jelentkezzen be az [Azure Portalra]
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson a mobilalkalmazás nevére.
3. Kattintson a **eszközök**
4. Kattintson a **erőforrás-kezelő** OBSERVE menüben kattintson a **Ugrás**.  Megnyílik egy új ablakot vagy lapot.
5. Bontsa ki a **config**, **authsettings** a helyhez, a bal oldali navigációs csomópontok.
6. Kattintson a **szerkesztése**
7. Keresse meg a "allowedExternalRedirectUrls" elemet.  NULL értékű vagy egy tömböt is beállítható.  Módosítsa az értéket a következő értéket:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Cserélje le az URL-címeket a szolgáltatás URL-címei.  Például "http://localhost:3000" (a Node.js sample szolgáltatáshoz tartozó), vagy "http://localhost:4400" (a Fodrozás szolgáltatás).  Azonban ezen URL-címei, példák - a helyzet, beleértve a szolgáltatások a példákban eltérő lehet.
8. Kattintson a **olvasási/írási** gombra a képernyő jobb felső sarkában.
9. Kattintson a zöld **PUT** gombra.

A beállítások mentése ezen a ponton.  Ne zárja be a böngészőablakot amíg tart a beállítások mentése.
Ezek visszacsatolási URL-címeket is hozzá az App Service CORS-beállítások:

1. Jelentkezzen be az [Azure Portalra]
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson a mobilalkalmazás nevére.
3. A beállítások panelről automatikusan megnyílik.  Ha nem, kattintson a gombra **összes beállítás**.
4. Kattintson a **CORS** az API menüjében.
5. A mezőbe a felvenni kívánt URL-cím megadva, és nyomja le az Enter billentyűt.
6. Adja meg a további URL-címek, szükség szerint.
7. Kattintson a **Mentés** gombra a beállítások mentéséhez.

Körülbelül 10 – 15 másodperc az új beállítások érvénybe léptetéséhez vesz igénybe.

## <a name="register-for-push"></a>Útmutató: a leküldéses értesítések regisztrálása
Telepítse a [phonegap-beépülőmodul-leküldéses] leküldéses értesítések kezeléséhez.  A beépülő modul könnyen segítségével adhatók a `cordova plugin add` parancsot a parancssorban, vagy a Git beépülő modul telepítő Visual Studio segítségével.  A következő kódot az Apache Cordova-alkalmazás regisztrálja az eszközt a leküldéses értesítések:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

A Notification Hubs SDK használatával leküldéses értesítéseket küldeni a kiszolgálóról.  Soha ne küldjön leküldéses értesítéseket közvetlenül az ügyfelektől. Ezzel a szolgáltatásmegtagadási támadások elleni Notification hubs használatával vagy a pns-sel való is használható.  A pns-sel sikerült bA a forgalom miatt az ilyen támadásokat.

## <a name="more-information"></a>További információ

A részletes API részletei a [API-JÁNAK dokumentációja](http://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portalra]: https://portal.azure.com
[Azure Mobile Apps gyors üzembe helyezés]: app-service-mobile-cordova-get-started.md
[Bevezetés a hitelesítés használatába]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova beépülő modul az Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[az első Apache Cordova-alkalmazás]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-beépülőmodul-leküldéses]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-beépülőmodul-eszköz]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-beépülőmodul-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
