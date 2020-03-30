---
title: A JavaScript SDK használata
description: A v használata az Azure mobile-alkalmazásokhoz
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461521"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>A JavaScript-ügyfélkódtár használata az Azure Mobile Apps alkalmazásokhoz
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogy a legújabb [JavaScript SDK for Azure Mobile Apps]használatával hajtson végre gyakori forgatókönyveket. Ha most jön az Azure Mobile Apps, először teljes [Azure Mobile Apps gyorsindítással] hozzon létre egy háttérrendszer, és hozzon létre egy táblázatot. Ebben az útmutatóban a mobil háttérrendszer HASZNÁLATÁRa összpontosítunk a HTML/JavaScript webalkalmazásokban.

## <a name="supported-platforms"></a>Támogatott platformok
A böngészők támogatását a főbb böngészők jelenlegi és utolsó verzióira korlátozzuk: Google Chrome, Microsoft Edge, Microsoft Internet Explorer és Mozilla Firefox.  Arra számítunk, hogy az SDK bármilyen viszonylag modern böngészővel működik.

A csomag univerzális JavaScript modulként kerül terjesztésre, így támogatja a globális, AMD és CommonJS formátumokat.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Telepítés és előfeltételek
Ez az útmutató feltételezi, hogy létrehozott egy háttérrendszer egy táblával. Ez az útmutató feltételezi, hogy a tábla ugyanazokkal a sémával rendelkezik, mint az oktatóanyagokban lévő táblák.

Az Azure Mobile Apps JavaScript SDK telepítése `npm` a következő paranccsal végezhető el:

```
npm install azure-mobile-apps-client --save
```

A könyvtár ES2015 modulként is használható, a CommonJS környezetekben, például a Browserify és a Webpack környezetekben, valamint AMD-könyvtárként.  Példa:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Az SDK előre elkészített verzióját úgy is használhatja, hogy közvetlenül a CDN-ről tölti le:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Útmutató: Felhasználók hitelesítése
Az Azure App Service támogatja az alkalmazásfelhasználók hitelesítését és engedélyezését különböző külső identitásszolgáltatók használatával: Facebook, Google, Microsoft-fiók és Twitter. A táblákra vonatkozó engedélyeket beállíthatja úgy, hogy az egyes műveletekhez csak hitelesített felhasználókra korlátozza a hozzáférést. A hitelesített felhasználók identitását is használhatja engedélyezési szabályok kiszolgálóparancsfájlokban való megvalósításához. További információt az Első lépések a [hitelesítéssel] oktatóanyagban talál.

Két hitelesítési folyamat támogatott: egy kiszolgálói folyamat és egy ügyfélfolyamat.  A kiszolgálófolyamat a legegyszerűbb hitelesítési élményt nyújtja, mivel a szolgáltató webes hitelesítési felületére támaszkodik. Az ügyfélfolyamat lehetővé teszi az eszközspecifikus képességekkel való mélyebb integrációt, például az egyszeri bejelentkezést, mivel szolgáltatóspecifikus SDK-kra támaszkodik.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Útmutató: Konfigurálja a mobilapp-szolgáltatást külső átirányítási URL-címekhez.
Többféle JavaScript-alkalmazás használja a visszacsatolási képességet az OAuth felhasználói felület folyamatainak kezeléséhez.  Ilyen képességek:

* A szolgáltatás helyi futtatása
* A Live Reload használata az ionos keretrendszerrel
* Átirányítás az App Service szolgáltatásba hitelesítéshez.

A helyi futtatás problémákat okozhat, mivel az App Service-hitelesítés alapértelmezés szerint csak úgy van beállítva, hogy engedélyezze a hozzáférést a mobilalkalmazás háttérrendszeréről. Az alábbi lépésekkel módosíthatja az App Service beállításait a hitelesítés engedélyezéséhez a kiszolgáló helyi futtatásakor:

1. Bejelentkezés az [Azure Portalra]
2. Nyissa meg a mobilalkalmazás-háttéradat.
3. Válassza a **FEJLESZTŐESZKÖZÖK** menü **Erőforrás-kezelő** parancsát.
4. Kattintson az **Ugrás** gombra, ha új lapon vagy ablakban szeretné megnyitni a mobilalkalmazás-háttérrendszer erőforrás-kezelőját.
5. Bontsa ki az alkalmazás **konfigurációs** > **hitelesítési beállítási** csomópontját.
6. Az erőforrás szerkesztésének engedélyezéséhez kattintson a **Szerkesztés** gombra.
7. Keresse meg a **allowedExternalRedirectUrls** elemet, amelynek null értékűnek kell lennie. Adja hozzá URL-címeit egy tömbhöz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Cserélje le a tömb URL-címeit a szolgáltatás URL-címeire, amely ebben a példában a helyi Node.js mintaszolgáltatásra szolgál. `http://localhost:3000` Az alkalmazás `http://localhost:4400` konfigurálásától függően használhatja a Ripple szolgáltatást vagy más URL-címet is.
8. A lap tetején kattintson az **Olvasás/írás**gombra, majd a PUT **gombra** a frissítések mentéséhez.

Ugyanazokat a visszacsatolási URL-címeket is hozzá kell adnia a CORS engedélyezési listájának beállításaihoz:

1. Keresse meg vissza az [Azure Portalon.]
2. Nyissa meg a mobilalkalmazás-háttéradat.
3. Kattintson a **CORS** elemre az **API** menüben.
4. Írja be az egyes URL-címeket az üres **Allowed Origins** szövegmezőbe.  Új szövegdoboz jön létre.
5. Kattintson a **MENTÉS gombra**

A háttérrendszer frissítése után használhatja az új visszacsatolási URL-eket az alkalmazásban.

<!-- URLs. -->
[Az Azure mobilalkalmazásokkal való gyors indítás]: app-service-mobile-cordova-get-started.md
[Bevezetés a hitelesítés használatába]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure-portál]: https://portal.azure.com/
[JavaScript SDK az Azure mobilalkalmazásokhoz]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
