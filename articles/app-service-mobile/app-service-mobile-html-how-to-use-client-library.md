---
title: A JavaScript SDK használata az Azure Mobile Apps szolgáltatásban
description: Az Azure Mobile Apps v használata
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
ms.openlocfilehash: ba2eb5a7f888e4cffcd798259afa8194b4021025
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>A JavaScript ügyféloldali kódtár használata az Azure Mobile Apps-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ez az útmutató útmutatást ad teszi a végrehajtását szolgáltatást a legújabb használó általános forgatókönyvhöz [JavaScript SDK az Azure Mobile Apps]. Ha most ismerkedik az Azure Mobile Apps, először végezzen [Azure Mobile Apps gyors üzembe helyezés] -háttéralkalmazás létrehozása, és hozzon létre egy táblát. Ebben az útmutatóban azt összpontosítani a mobil-háttéralkalmazások használatával HTML/JavaScript webes alkalmazásokhoz.

## <a name="supported-platforms"></a>Támogatott platformok
Jelenleg korlátozza a ismertebb böngésző aktuális és az utolsó verzióiban támogatott böngésző: Google Chrome, a Microsoft Edge, a Microsoft Internet Explorer vagy a Mozilla Firefox.  Várhatóan viszonylag modern böngészők függvényt SDK.

A csomag terjesztésekor modulként univerzális JavaScript, így támogatja a globális változók globals, AMD, és az CommonJS formátumú.

## <a name="Setup"></a>A telepítő és Előfeltételek
Ez az útmutató feltételezi, hogy létrehozott egy táblát a háttérkiszolgálón. Ez az útmutató feltételezi, hogy rendelkezik-e a tábla a táblák ugyanazon séma ezen oktatóprogram a.

Az Azure Mobile Apps JavaScript SDK telepítése megteheti a `npm` parancs:

```
npm install azure-mobile-apps-client --save
```

A könyvtár egy ES2015 modul, például Browserify és Webpack és egy AMD tárként CommonJS környezetekben is használható.  Példa:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Az SDK előre elkészített verziójának úgy, hogy letölti a CDN-ről is használhatja:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Hogyan: hitelesíti a felhasználókat
Az Azure App Service támogat hitelesítése és engedélyezése a felhasználók alkalmazás különböző külső Identitásszolgáltatók: Facebook, Google, a Microsoft Account és Twitter. A engedélyeket korlátozhatja a hozzáférést a megadott művelet csak a hitelesített felhasználók táblákon. Hitelesített felhasználók identitásának használhatja, ha az engedélyezési szabályok megvalósítását a kiszolgáló parancsfájlokat. További információkért lásd: a [Bevezetés a hitelesítés használatába] oktatóanyag.

Két hitelesítési forgalom támogatottak: a kiszolgáló folyamata és egy ügyfél folyamatában.  A kiszolgáló folyamata nyújt a legegyszerűbb felhasználói hitelesítés, a szolgáltató webes hitelesítés felület támaszkodnak. Az ügyféltanúsítvány-folyamat lehetővé teszi, hogy szorosabb integrációt eszközspecifikus képességeket például single-sign-on, szolgáltatói SDK-k támaszkodnak az.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Útmutató: a Mobile App Service konfigurálása külső átirányítási URL-címeket.
A JavaScript-alkalmazások számos különböző egy visszacsatolási funkció segítségével OAuth UI-forgalom kezelésére.  Ezek a képességek közé tartoznak:

* A szolgáltatás helyben fut
* Élő Újrabetöltés használata a ionos keretrendszer
* App Service hitelesítés irányít át.

Helyileg futó problémákat okozhat, mert alapértelmezés szerint az App Service hitelesítés csak engedélyezésére van konfigurálva a Mobile Apps-háttéralkalmazás való hozzáférést. Az alábbi lépések segítségével módosíthatja az App Service-beállítások a kiszolgáló a helyi futtatás során a hitelesítés engedélyezése:

1. Jelentkezzen be az [Azure Portalra]
2. Nyissa meg a mobilalkalmazás háttérrendszerének.
3. Válassza ki **erőforrás-kezelő** a a **FEJLESZTŐESZKÖZÖK** menü.
4. Kattintson a **Ugrás** az erőforrás-kezelő, a mobilalkalmazás háttérrendszerének egy új lapot vagy ablak megnyitásához.
5. Bontsa ki a **config** > **authsettings** csomópont az alkalmazásra vonatkozóan.
6. Kattintson a **szerkesztése** ahhoz, hogy az erőforrás Szerkesztés gomb.
7. Keresés a **allowedExternalRedirectUrls** elemmel rendelkezik, ami null értékűnek kell lennie. Adja hozzá az URL-címek tömbjét:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Az URL-címeket, a tömb cserélje le a szolgáltatást, amely ebben a példában az URL-címek `http://localhost:3000` a Node.js-mintát helyi szolgáltatás. Is `http://localhost:4400` a Fodrozás szolgáltatás vagy egy másik URL-cím, attól függően, hogy az alkalmazás konfigurálását.
8. Kattintson a lap tetején **olvasási/írási**, majd kattintson a **PUT** a módosítások mentéséhez.

Is szüksége lesz, ugyanazon visszacsatolási URL-címek hozzáadása a CORS-engedélyezési beállítások:

1. Lépjen vissza a [Azure Portalra].
2. Nyissa meg a mobilalkalmazás háttérrendszerének.
3. Kattintson a **CORS** a a **API** menü.
4. Adja meg az egyes URL-címet az üres **engedélyezett eredetet** szövegmezőben.  Egy új szövegmező jön létre.
5. Kattintson a **mentése**

Miután a háttér-frissítéseket, lesz, az új visszacsatolási URL-címek használatához az alkalmazás.

<!-- URLs. -->
[Azure Mobile Apps gyors üzembe helyezés]: app-service-mobile-cordova-get-started.md
[Bevezetés a hitelesítés használatába]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portalra]: https://portal.azure.com/
[JavaScript SDK az Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
