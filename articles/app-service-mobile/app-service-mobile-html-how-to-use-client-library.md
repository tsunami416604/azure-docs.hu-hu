---
title: A JavaScript SDK használata az Azure Mobile Appsban
description: V használata az Azure Mobile Apps-alkalmazáshoz
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 16871bdc59d141334bc2c95f26929f270d7971cf
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100533"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>A JavaScript ügyféloldali kódtár használata az Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ez az útmutató bemutatja, hogy a legújabb használatával általános forgatókönyveinek végrehajtásával [A JavaScript SDK for Azure Mobile Apps]. Ha most ismerkedik az Azure Mobile Apps, először végezzen [Azure Mobile Apps alkalmazások gyors üzembe helyezési] egy háttérrendszer létrehozása, és hozzon létre egy táblát. Ebben az útmutatóban koncentrálunk a mobil háttérszolgáltatásban használatával HTML/JavaScript webes alkalmazásokban.

## <a name="supported-platforms"></a>Támogatott platformok
Böngésző támogatása, a jelenlegi és a legutóbbi verzióra a főbb böngésző modelljeként:  Google Chrome, Microsoft Edge, Microsoft Internet Explorer, and Mozilla Firefox.  Várhatóan viszonylag modern böngészőkben függvényt az SDK-t.

A csomag terjesztése egy univerzális JavaScript-modult, így támogatja a globals, AMD, és CommonJS formázza.

## <a name="Setup"></a>A telepítő és Előfeltételek
Ez az útmutató feltételezi, hogy létrehozott egy táblát a háttérrendszernek. Ez az útmutató feltételezi, hogy a tábla ezek az oktatóanyagok a táblákként ugyanazzal a sémával rendelkezik.

Az Azure Mobile Apps JavaScript SDK telepítése keresztül lehetséges az `npm` parancsot:

```
npm install azure-mobile-apps-client --save
```

Az erőforrástár-ES2015 modulként CommonJS környezetekben, például Browserify és a webpack használatával készült, valamint az AMD-tár is használható.  Példa:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Egy előre elkészített verze sady SDK letöltésével közvetlenül a CDN-en is használhatja:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>kézikönyv: Felhasználók hitelesítése
Az Azure App Service támogatja a hitelesítés és engedélyezés az alkalmazás felhasználóinak különböző külső identitásszolgáltató használatával: Facebook, Google, Microsoft-fiókjával, és a Twitter. Beállíthatja, hogy a engedélyeit azokon a táblákon, az adott műveletek csak a hitelesített felhasználók a hozzáférés korlátozásához. Az engedélyezési szabályok megvalósításához a kiszolgálóoldali parancsprogramok is használhatja a hitelesített felhasználók identitását. További információkért lásd: a [hitelesítés első lépései] oktatóanyag.

Két hitelesítési folyamatok támogatottak: a server flow és a egy ügyfél folyamatot.  A server flow a legegyszerűbb felhasználói hitelesítés támaszkodik a szolgáltató webes hitelesítés felületet nyújt. A client flow lehetővé teszi, hogy az eszköz specifikus képességek mélyebb integrációjuk például single-sign-on, a szolgáltatóhoz tartozó SDK-k támaszkodik.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>kézikönyv: A Mobile App Service konfigurálása külső átirányítási URL-címek.
Számos különböző típusú JavaScript-alkalmazások egy visszacsatolási képesség segítségével OAuth felhasználói felület folyamatok kezeléséhez.  Ilyen képességek:

* A szolgáltatás helyben fut
* Élő Újrabetöltés használata a ionos keretrendszer
* App Service-ben átirányítása a hitelesítéshez.

Helyileg futó problémákat okozhat, mert alapértelmezés szerint az App Service hitelesítés csak konfigurált engedélyezze a hozzáférést a Mobile Apps-háttéralkalmazást. Használja az alábbi lépéseket az App Service-hitelesítés engedélyezése, ha helyileg futtatja a kiszolgáló beállításainak módosítása:

1. Jelentkezzen be az [Azure Portal]
2. Lépjen a Mobile Apps-háttéralkalmazást.
3. Válassza ki **erőforrás-kezelő** a a **FEJLESZTŐESZKÖZÖK** menü.
4. Kattintson a **Go** a Mobile Apps-háttéralkalmazás számára az erőforrás-kezelő megnyitása új lapon vagy ablakban.
5. Bontsa ki a **config** > **authsettings** csomópont az alkalmazáshoz.
6. Kattintson a **szerkesztése** gombra az erőforrás használatának engedélyezése.
7. Keresse meg a **allowedExternalRedirectUrls** elemet, amely null értékű kell legyen. Az URL-címek hozzáadása a tömböt:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Az URL-címeket, a szolgáltatás, amely ebben a példában cserélje le az URL-címeket, a tömb `http://localhost:3000` a Node.js-minta helyi szolgáltatás. Is `http://localhost:4400` a Ripple szolgáltatás-vagy valamilyen más URL-cím, az alkalmazás konfigurációjától függően.
8. Kattintson a lap tetején **olvasási/írási**, kattintson a **PUT** , mentse a módosításokat.

Is szüksége lesz, ugyanazon visszacsatolási URL-címek hozzáadása a CORS-engedélyezési beállítások:

1. Lépjen vissza a [Azure Portal].
2. Lépjen a Mobile Apps-háttéralkalmazást.
3. Kattintson a **CORS** a a **API** menü.
4. Adja meg az egyes URL-CÍMÉT az üres **engedélyezett eredetek** szövegmezőben.  Egy új szövegmező jön létre.
5. Kattintson a **mentése**

A háttérrendszer a frissítések után lesz az új visszacsatolási URL-címek használata az alkalmazásban.

<!-- URLs. -->
[Azure Mobile Apps alkalmazások gyors üzembe helyezési]: app-service-mobile-cordova-get-started.md
[Hitelesítés első lépései]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[A JavaScript SDK for Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
