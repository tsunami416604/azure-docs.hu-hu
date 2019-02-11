---
title: Az Apache Cordova beépülő modul használata az Azure Mobile Appsban
description: Az Apache Cordova beépülő modul használata az Azure Mobile Appsban
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
ms.openlocfilehash: cddb3769cfc5a2ba002e19036d986f4165670dc1
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962451"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Az Apache Cordova ügyféloldali kódtár használata az Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ez az útmutató bemutatja, hogy a legújabb használatával általános forgatókönyveinek végrehajtásával [Az Apache Cordova beépülő modul az Azure Mobile Appsban]. Ha most ismerkedik az Azure Mobile Apps, először hajtsa végre [Azure Mobile Apps alkalmazások gyors üzembe helyezési] egy háttérrendszer létrehozásához hozzon létre egy táblát, és töltse le egy előre elkészített Apache Cordova-projektet. Ebben az útmutatóban koncentrálunk az ügyféloldali Apache Cordova beépülő modult.

## <a name="supported-platforms"></a>Támogatott platformok
Ez az SDK támogatja az Apache Cordova v6.0.0, és később iOS, Android és Windows eszközökhöz.  A platform támogatja a következőképpen történik:

* Android API 19 – 24 (KitKat Nougat keresztül).
* iOS 8.0 és újabb verziók.
* Windows Phone 8.1.
* Univerzális Windows Platform.

## <a name="Setup"></a>A telepítő és Előfeltételek
Ez az útmutató feltételezi, hogy létrehozott egy táblát a háttérrendszernek. Ez az útmutató feltételezi, hogy a tábla ezek az oktatóanyagok a táblákként ugyanazzal a sémával rendelkezik. Ez az útmutató azt is feltételezi, hogy hozzáadta az Apache Cordova beépülő modul a kódot.  Nem tette, ha az Apache Cordova beépülő modult adhat hozzá a projekthez a parancssorban:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

További létrehozásával kapcsolatos információkat [az első Apache Cordova-alkalmazás], azok dokumentációjában talál.

## <a name="ionic"></a>Ionos v2 alkalmazás beállítása

Megfelelő konfigurálásához ionos v2-projektben, először hozzon létre egy alapszintű alkalmazást, és adja hozzá a Cordova beépülő modult:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Adja hozzá a következő sorokat `app.component.ts` az ügyfél-objektum létrehozásához:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Most már lefordíthatja, és futtassa a projektet a böngészőben:

```
ionic platform add browser
ionic run browser
```

Az Azure Mobile Apps Cordova beépülő modullal mindkét ionos v1 és v2-es alkalmazásokat támogatja.  Csak a ionos v2-alkalmazások a kiegészítő nyilatkozat megkövetelése a `WindowsAzure` objektum.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>kézikönyv: Felhasználók hitelesítése
Az Azure App Service támogatja a hitelesítés és engedélyezés az alkalmazás felhasználóinak különböző külső identitásszolgáltató használatával: Facebook, Google, Microsoft-fiókjával, és a Twitter. Beállíthatja, hogy a engedélyeit azokon a táblákon, az adott műveletek csak a hitelesített felhasználók a hozzáférés korlátozásához. Az engedélyezési szabályok megvalósításához a kiszolgálóoldali parancsprogramok is használhatja a hitelesített felhasználók identitását. További információkért lásd: a [hitelesítés első lépései] oktatóanyag.

Ha hitelesítési Apache Cordova-alkalmazást használ, a következő Cordova beépülő modulok elérhetőnek kell lennie:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Két hitelesítési folyamatok támogatottak: a server flow és a egy ügyfél folyamatot.  A server flow a legegyszerűbb felhasználói hitelesítés támaszkodik a szolgáltató webes hitelesítés felületet nyújt. A client flow lehetővé teszi, hogy az eszköz specifikus képességek mélyebb integrációjuk például single-sign-on, a szolgáltatóhoz tartozó eszközspecifikus SDK-k támaszkodik.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>kézikönyv: A Mobile App Service konfigurálása külső átirányítási URL-címek.
Az Apache Cordova-alkalmazások számos különböző egy visszacsatolási képesség segítségével OAuth felhasználói felület folyamatok kezeléséhez.  A localhost OAuth felhasználói felület folyamatok problémákat okozhat, mivel a hitelesítési szolgáltatás csak tudja, hogyan használják a szolgáltatást alapértelmezés szerint.  Problémás OAuth felhasználói felület folyamatok közé:

* A Ripple emulátort.
* Töltse be újra a ionos élő.
* A mobil háttérszolgáltatásban helyi futtatása
* A mobil háttérszolgáltatásban fut, mint az egy biztosító hitelesítési különböző Azure App Service-ben.

Kövesse az alábbi utasításokat, hogy a helyi beállításokban adja hozzá a konfigurációhoz:

1. Jelentkezzen be az [Azure Portal]
2. Válassza ki **összes erőforrás** vagy **App Services** majd kattintson a Mobile App nevére.
3. Kattintson a **eszközök**
4. Kattintson a **erőforrás-kezelő** OBSERVE menüben kattintson a **Go**.  Megnyílik egy új ablakot vagy lapot.
5. Bontsa ki a **config**, **authsettings** a webhely bal oldali navigációs csomópontok.
6. Kattintson a **szerkesztése**
7. Keresse meg a "allowedExternalRedirectUrls" elemet.  NULL értékű vagy egy olyan értéktömböt számbavételekhez állítható.  Módosítsa az értéket a következő értékre:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Cserélje le az URL-címeket a szolgáltatás URL-címeket.  Ilyenek például "http://localhost:3000" (az a Node.js sample szolgáltatás), vagy a "http://localhost:4400" (a Ripple-szolgáltatás).  Az URL-címek azonban olyan példák – a helyzet, beleértve a szolgáltatások a példákban eltérő lehet.
8. Kattintson a **olvasási/írási** gombra a képernyő jobb felső sarkában.
9. Kattintson a zöld **PUT** gombra.

A beállítások ezen a ponton lesznek mentve.  Ne zárja be a böngészőablakot amíg tart a beállítások mentése folyamatban van.
Ezek visszacsatolási URL-címeket is hozzáadhat az App Service CORS-beállítások:

1. Jelentkezzen be az [Azure Portal]
2. Válassza ki **összes erőforrás** vagy **App Services** majd kattintson a Mobile App nevére.
3. A beállítások panel automatikusan megnyílik.  Ha nem, kattintson a gombra **minden beállítás**.
4. Kattintson a **CORS** az API menüpont alatt.
5. Az URL-cím hozzáadása a mezőbe a kívánt megadott, és nyomja le az Enter billentyűt.
6. Adja meg a további URL-címek, szükség szerint.
7. Kattintson a **Mentés** gombra a beállítások mentéséhez.

Körülbelül 10 – 15 másodperc az új beállítások érvénybe léptetéséhez vesz igénybe.

## <a name="register-for-push"></a>kézikönyv: A leküldéses értesítések regisztrálása
Telepítse a [phonegap-beépülő modul – leküldéses] leküldéses értesítések kezeléséhez.  Ez a beépülő modul is könnyen hozzáadhatók használatával a `cordova plugin add` parancsot a parancssorban vagy a Git-beépülő modul telepítő Visual Studión belül.  Az Apache Cordova-alkalmazásban a következő kód regisztrálja az eszközt a leküldéses értesítésekhez:

```javascript
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

A Notification Hubs SDK használatával leküldéses értesítések küldése a kiszolgálóról.  Soha ne küldjön leküldéses értesítéseket közvetlenül az ügyfelektől. Így felhasználható aktiválhat egy szolgáltatásmegtagadási támadást a Notification Hubs vagy a pns-sel szemben.  A pns-sel sikerült bA eredményeként az ilyen támadásokat a forgalmat.

## <a name="more-information"></a>További információ

A részletes API részletei a [API-dokumentáció](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Azure Mobile Apps alkalmazások gyors üzembe helyezési]: app-service-mobile-cordova-get-started.md
[Hitelesítés első lépései]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Az Apache Cordova beépülő modul az Azure Mobile Appsban]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[az első Apache Cordova-alkalmazás]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-beépülő modul – leküldéses]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
