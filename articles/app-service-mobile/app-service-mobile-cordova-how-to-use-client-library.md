---
title: Az Apache Cordova beépülő modul használata
description: Az Apache Cordova beépülő modul használata az Azure Mobile Apps
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ecca8f719a01abe68b368987fce4ea883193e844
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668502"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Az Apache Cordova ügyféloldali kódtár használata az Azure-ban Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan végezheti el a gyakori forgatókönyveket az [Apache Cordova beépülő modul az Azure Mobile Appshoz]használatával. Ha még nem ismeri az Azure Mobile Appst, először fejezze be az [azure Mobile Apps gyorskonfigurálás] a háttérrendszer létrehozásához, egy tábla létrehozásához és egy előre elkészített Apache Cordova-projekt letöltéséhez. Ebben az útmutatóban az ügyféloldali Apache Cordova beépülő modulra fogunk összpontosítani.

## <a name="supported-platforms"></a>Támogatott platformok
Ez az SDK az Apache Cordova v 6.0.0 és újabb verzióit támogatja az iOS-, Android-és Windows-eszközökön.  A platform támogatása a következő:

* Android API 19-24 (KitKat – nugát).
* iOS 8,0-es és újabb verziók.
* Windows Phone-telefon 8,1.
* Univerzális Windows-platform.

## <a name="Setup"></a>Telepítés és előfeltételek
Ez az útmutató azt feltételezi, hogy létrehozott egy táblázatot tartalmazó hátteret. Ez az útmutató azt feltételezi, hogy a tábla ugyanazzal a sémával rendelkezik, mint az oktatóanyagokban szereplő táblák. Az útmutató azt is feltételezi, hogy hozzáadta az Apache Cordova beépülő modult a kódhoz.  Ha még nem tette meg, az Apache Cordova beépülő modult hozzáadhatja a projekthez a parancssorban:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Az [az első Apache Cordova-alkalmazás]létrehozásával kapcsolatos további információkért tekintse meg a dokumentációját.

## <a name="ionic"></a>Egy ionos v2-alkalmazás beállítása

Egy ionos v2-projekt megfelelő konfigurálásához először hozzon létre egy alapszintű alkalmazást, és adja hozzá a Cordova beépülő modult:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Adja hozzá a következő sorokat a `app.component.ts`hoz az ügyfél-objektum létrehozásához:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Most már létrehozhatja és futtathatja a projektet a böngészőben:

```
ionic platform add browser
ionic run browser
```

Az Azure Mobile Apps Cordova beépülő modulja az ionos v1 és v2 alkalmazásokat egyaránt támogatja.  Csak az ionos v2-alkalmazások igénylik a `WindowsAzure` objektumra vonatkozó további deklarációt.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Útmutató: felhasználók hitelesítése
Azure App Service támogatja az alkalmazások felhasználóinak hitelesítését és engedélyezését különféle külső identitás-szolgáltatók használatával: Facebook, Google, Microsoft-fiók és Twitter. A táblákra vonatkozó engedélyeket úgy állíthatja be, hogy az adott műveletekhez való hozzáférést csak a hitelesített felhasználókra korlátozza. A hitelesített felhasználók identitását is használhatja a kiszolgálói parancsfájlok engedélyezési szabályainak megvalósításához. További információ: Ismerkedés [Ismerkedés a hitelesítéssel] oktatóanyaggal.

Ha egy Apache Cordova-alkalmazásban használ hitelesítést, a következő Cordova beépülő modulnak elérhetőnek kell lennie:

* [Cordova-beépülő modul-eszköz]
* [Cordova-beépülő modul – inappbrowser]

A rendszer két hitelesítési folyamatot támogat: a kiszolgáló és az ügyfél folyamatát.  A kiszolgálói folyamat biztosítja a legegyszerűbb hitelesítési felületet, mivel a szolgáltató webes hitelesítési felületén alapul. Az ügyfél folyamata nagyobb integrációt tesz lehetővé az eszközre jellemző képességekkel, például az egyszeri bejelentkezéssel, mivel a szolgáltatóra jellemző, az eszközre jellemző SDK-ra támaszkodik.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Útmutató: Mobil App Service konfigurálása külső átirányítási URL-címekhez.
Az Apache Cordova-alkalmazások számos típusa visszacsatolási képességgel rendelkezik a OAuth felhasználói felületi folyamatainak kezeléséhez.  A OAuth felhasználói felülete a localhost-on keresztül problémákat okozhat, mivel a hitelesítési szolgáltatás csak azt tudja, hogyan használhatja a szolgáltatást alapértelmezés szerint.  Példák a problémás OAuth felhasználói felületi folyamataira:

* A hullám emulátora.
* Élő reload az ionos szolgáltatással.
* A mobil háttérrendszer helyi futtatása
* A mobil háttérrendszer futtatása más Azure App Serviceon, mint a hitelesítés biztosítása.

Kövesse az alábbi utasításokat a helyi beállítások a konfigurációhoz való hozzáadásához:

1. Jelentkezzen be az [Azure Portalra]
2. Válassza a **minden erőforrás** vagy **app Services** lehetőséget, majd kattintson a saját mobil alkalmazásának nevére.
3. Kattintson az **eszközök** elemre
4. A megfigyelés menüben kattintson az **erőforrás-kezelő** elemre, majd az **Ugrás**gombra.  Megnyílik egy új ablak vagy lap.
5. Bontsa **ki**a **authsettings elemre** csomópontjait a bal oldali navigációs sávon a helyhez.
6. Kattintson a **Szerkesztés** gombra
7. Keresse meg a "allowedExternalRedirectUrls" elemet.  A beállítás értéke null vagy értékek tömbje lehet.  Módosítsa az értéket a következő értékre:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Cserélje le az URL-címeket a szolgáltatás URL-címeire.  Ilyenek például a következők: `http://localhost:3000` (a Node. js-minta szolgáltatáshoz), vagy `http://localhost:4400` (a lüktető szolgáltatáshoz).  Ezek az URL-címek például a helyzetek, például a példákban említett szolgáltatások esetében eltérőek lehetnek.
8. Kattintson a képernyő jobb felső sarkában található **olvasás/írás** gombra.
9. Kattintson a zöld **put** gombra.

A beállítások ezen a ponton lesznek mentve.  Ne zárjuk be a böngészőablakot, amíg a beállítások nem lettek mentve.
Adja hozzá ezeket a visszacsatolási URL-címeket is a App Service CORS-beállításaihoz:

1. Jelentkezzen be az [Azure Portalra]
2. Válassza a **minden erőforrás** vagy **app Services** lehetőséget, majd kattintson a saját mobil alkalmazásának nevére.
3. A beállítások panel automatikusan megnyílik.  Ha nem, kattintson **a minden beállítás**elemre.
4. Az API menüben kattintson a **CORS** elemre.
5. Adja meg a hozzáadni kívánt URL-címet a megadott mezőbe, majd nyomja le az ENTER billentyűt.
6. Szükség szerint adja meg a további URL-címeket.
7. Kattintson a **Mentés** gombra a beállítások mentéséhez.

Az új beállítások érvénybe léptetéséhez körülbelül 10-15 másodperc szükséges.

## <a name="register-for-push"></a>Útmutató: a leküldéses értesítések regisztrálása
Telepítse a [telefon-beépülő modult] a leküldéses értesítések kezeléséhez.  Ez a beépülő modul egyszerűen hozzáadható a parancssorban a `cordova plugin add` parancs használatával, vagy a Visual studión belüli git beépülő modullal.  Az Apache Cordova-alkalmazás következő kódja regisztrálja az eszközt a leküldéses értesítésekhez:

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

A Notification Hubs SDK használatával leküldéses értesítéseket küldhet a kiszolgálóról.  Soha ne küldjön leküldéses értesítéseket közvetlenül az ügyfelektől. Ezzel felhasználható a szolgáltatásmegtagadási támadás elutasítása Notification Hubs vagy a PNS ellen.  A PNS az ilyen támadások miatt megtilthatja a forgalmat.

## <a name="more-information"></a>További információ

Részletes API-részleteket az [API dokumentációjában](https://azure.github.io/azure-mobile-apps-js-client/)talál.

<!-- URLs. -->
[Azure Portalra]: https://portal.azure.com
[Azure Mobile Apps gyorskonfigurálás]: app-service-mobile-cordova-get-started.md
[Ismerkedés a hitelesítéssel]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova beépülő modul az Azure Mobile Appshoz]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[az első Apache Cordova-alkalmazás]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[telefon-beépülő modult]: https://www.npmjs.com/package/phonegap-plugin-push
[Cordova-beépülő modul-eszköz]: https://www.npmjs.com/package/cordova-plugin-device
[Cordova-beépülő modul – inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
