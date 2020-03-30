---
title: Az Apache Cordova plugin használata
description: Az Apache Cordova beépülő modul használata az Azure mobilalkalmazásokhoz
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: cafeea8afe571fc81548833952eee72a695fed41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459361"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Az Apache Cordova ügyfélkönyvtár használata az Azure Mobile Apps szolgáltatáshoz
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogy gyakori forgatókönyveket hajthat végre az Azure Mobile Apps legújabb [Apache Cordova beépülő moduljával.] Ha most jön az Azure Mobile Apps, először teljes [Azure Mobile Apps gyorsan hozzon] létre egy háttérrendszer, hozzon létre egy táblázatot, és töltse le az előre elkészített Apache Cordova projekt. Ebben az útmutatóban az ügyféloldali Apache Cordova pluginra összpontosítunk.

## <a name="supported-platforms"></a>Támogatott platformok
Ez az SDK támogatja az Apache Cordova v6.0.0-s és újabb verzióit iOS, Android és Windows rendszerű eszközökön.  A platform támogatása a következő:

* Android API 19-24 (KitKat keresztül Nugát).
* az iOS 8.0-s és újabb verzióiban.
* Windows Phone 8.1.
* Univerzális Windows platform.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Telepítés és előfeltételek
Ez az útmutató feltételezi, hogy létrehozott egy háttérrendszer egy táblával. Ez az útmutató feltételezi, hogy a tábla ugyanazokkal a sémával rendelkezik, mint az oktatóanyagokban lévő táblák. Ez az útmutató azt is feltételezi, hogy hozzáadta az Apache Cordova plugint a kódhoz.  Ha még nem tette meg, hozzáadhatja az Apache Cordova beépülő modult a projekthez a parancssorban:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Az [első Apache Cordova alkalmazás]létrehozásával kapcsolatos további információkért tekintse meg a dokumentációt.

## <a name="setting-up-an-ionic-v2-app"></a><a name="ionic"></a>Ionos v2 alkalmazás beállítása

Az Ionic v2 projekt megfelelő konfigurálásához először hozzon létre egy alapalkalmazást, és adja hozzá a Cordova bővítményt:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Az ügyfélobjektum `app.component.ts` létrehozásához adja hozzá a következő sorokat:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Most már létrehozhatja és futtathatja a projektet a böngészőben:

```
ionic platform add browser
ionic run browser
```

Az Azure Mobile Apps Cordova beépülő modul támogatja az Ionic v1 és v2 alkalmazásokat is.  Csak az Ionic v2 alkalmazások igényelnek további deklarációt az `WindowsAzure` objektumhoz.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Útmutató: Felhasználók hitelesítése
Az Azure App Service támogatja az alkalmazásfelhasználók hitelesítését és engedélyezését különböző külső identitásszolgáltatók használatával: Facebook, Google, Microsoft-fiók és Twitter. A táblákra vonatkozó engedélyeket beállíthatja úgy, hogy az egyes műveletekhez csak hitelesített felhasználókra korlátozza a hozzáférést. A hitelesített felhasználók identitását is használhatja engedélyezési szabályok kiszolgálóparancsfájlokban való megvalósításához. További információt az Első lépések a [hitelesítéssel] oktatóanyagban talál.

Ha Apache Cordova alkalmazásban használ hitelesítést, a következő Cordova bővítményeknek kell rendelkezésre állniuk:

* [cordova-plugin-eszköz]
* [cordova-plugin-inappbrowser]

Két hitelesítési folyamat támogatott: egy kiszolgálói folyamat és egy ügyfélfolyamat.  A kiszolgálófolyamat a legegyszerűbb hitelesítési élményt nyújtja, mivel a szolgáltató webes hitelesítési felületére támaszkodik. Az ügyfélfolyamat lehetővé teszi az eszközspecifikus képességekkel való mélyebb integrációt, például az egyszeri bejelentkezést, mivel szolgáltatóspecifikus eszközspecifikus SDK-kra támaszkodik.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Útmutató: Konfigurálja a mobilapp-szolgáltatást külső átirányítási URL-címekhez.
Az Apache Cordova alkalmazások több típusa is visszacsatolási képességet használ az OAuth felhasználói felületi folyamatok kezeléséhez.  Az OAuth felhasználói felület a localhost-on problémákat okoz, mivel a hitelesítési szolgáltatás csak alapértelmezés szerint tudja a szolgáltatás kihasználását.  Példák a problémás OAuth felhasználói felület folyamataira:

* A Ripple emulátor.
* Élő újratöltés ionossal.
* A mobil háttér-háttérszámítógép helyi futtatása
* A mobil háttérrendszer futtatása egy másik Azure App Service-ben, mint a hitelesítést biztosító.

A helyi beállítások hozzáadásához kövesse az alábbi utasításokat:

1. Bejelentkezés az [Azure Portalra]
2. Válassza **az Összes erőforrás** vagy az App **Services** lehetőséget, majd kattintson a mobilalkalmazás nevére.
3. Kattintson **az Eszközök gombra**
4. Kattintson az **Erőforrás-kezelő** menü Megfigyelés menüjében az **Ugrás parancsra.**  Megnyílik egy új ablak vagy lap.
5. Bontsa ki a webhely **konfigurációs**, **hitelesítési** csomópontjait a bal oldali navigációs sávon.
6. Kattintson **a Szerkesztés gombra**
7. Keresse meg az "allowedExternalRedirectUrls" elemet.  Lehet, hogy null értékre vagy értéktömbre van állítva.  Módosítsa az értéket a következő értékre:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Cserélje le az URL-címeket a szolgáltatás URL-címeire.  Ilyenek `http://localhost:3000` például (a Node.js `http://localhost:4400` mintaszolgáltatáshoz) vagy (a Ripple szolgáltatáshoz).  Ezek az URL-ek azonban példák - az Ön helyzete, beleértve a példákban említett szolgáltatásokat is, eltérő lehet.
8. Kattintson az **Olvasás/írás** gombra a képernyő jobb felső sarkában.
9. Kattintson a zöld **PUT** gombra.

A beállítások ezen a ponton kerülnek mentésre.  Ne zárja be a böngészőablakot, amíg a beállítások mentése be nem fejeződött.
Adja hozzá ezeket a visszacsatolási URL-címeket az App Service CORS-beállításaihoz is:

1. Bejelentkezés az [Azure Portalra]
2. Válassza **az Összes erőforrás** vagy az App **Services** lehetőséget, majd kattintson a mobilalkalmazás nevére.
3. A Beállítások panel automatikusan megnyílik.  Ha nem, kattintson a **Minden beállítás gombra.**
4. Kattintson a **CORS** elemre az API menüben.
5. Írja be a hozzáadni kívánt URL-címet a megadott mezőbe, és nyomja le az Enter billentyűt.
6. Szükség szerint adjon meg további URL-címeket.
7. Kattintson a **Mentés** gombra a beállítások mentéséhez.

Az új beállítások érvénybe léptetése körülbelül 10–15 másodpercet vesz igénybe.

## <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Útmutató: Regisztráció leküldéses értesítésekre
Telepítse a [phonegap-plugin-push-ot] a leküldéses értesítések kezeléséhez.  Ez a plugin könnyen hozzá `cordova plugin add` a parancsot a parancssorból, vagy a Git plugin telepítő belül Visual Studio.  Az Apache Cordova alkalmazásban a következő kód regisztrálja a készüléket leküldéses értesítésekhez:

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

Az Értesítési központok SDK használatával leküldéses értesítéseket küldhet a kiszolgálóról.  Soha ne küldjön leküldéses értesítéseket közvetlenül az ügyfelektől. Ezzel szolgáltatásmegtagadási támadást indíthat az értesítési központok vagy a PNS ellen.  A PNS az ilyen támadások miatt betilthatja a forgalmat.

## <a name="more-information"></a>További információ

Részletes API-részleteket api [dokumentációnkban](https://azure.github.io/azure-mobile-apps-js-client/)talál.

<!-- URLs. -->
[Azure-portál]: https://portal.azure.com
[Az Azure mobilalkalmazásokkal való gyors indítás]: app-service-mobile-cordova-get-started.md
[Bevezetés a hitelesítés használatába]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova beépülő modul az Azure mobilalkalmazásokhoz]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[az első Apache Cordova alkalmazás]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-eszköz]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
