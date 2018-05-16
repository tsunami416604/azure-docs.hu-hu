---
title: Az Azure AD Node.js webes API-k első lépések |} Microsoft Docs
description: Hogyan hozhat létre a többi Node.js webes API-k, amely az Azure AD használatára a hitelesítéshez.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 24591c46858970724fbd1fe36336f7f2b0b2fefd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Az Azure AD Node.js webes API-k első lépések

Ez a cikk bemutatja, hogyan kell biztonságos egy [Restify](http://restify.com/) az API-végpont [Passport](http://passportjs.org/) használatával a [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modul Azure aktív kommunikáció kezelése Directory (AAD). 

Ez az oktatóanyag kiterjed fontos szempont vonatkozó biztonságossá tétele API-végpontokon. Bejelentkezés és a hitelesítési tokenek megőrzése fontos szempont itt nincsenek megvalósítva, és egy ügyfélalkalmazás felelőssége. Egy ügyfél megvalósítása körülvevő részletekért tekintse át a [Node.js webalkalmazás be- és kijelentkezési az Azure ad-val](active-directory-devquickstarts-openidconnect-nodejs.md).

A teljes kódminta, ez a cikk társított érhető el a [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>A minta-projekt létrehozása
A kiszolgálói alkalmazás támogatja a Restify és Passport, valamint a fiókot az aad-be továbbított adatokat néhány csomagfüggőségek igényel.

Első lépésként adja hozzá a következő kódot fájlba `package.json`:

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

Egyszer `package.json` jön létre, futtassa `npm install` a parancssorban a csomag függőségeinek telepítéséhez. 

### <a name="configure-the-project-to-use-active-directory"></a>A használatára az Active Directory-projekt konfigurálása

Először az alkalmazás konfigurálása néhány fiókspecifikus értékek vannak az Azure parancssori felület szerezhetők be. Ismerkedjen meg a parancssori felület a legegyszerűbben az Azure-felhő rendszerhéj használata.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A felhő rendszerhéj adja meg a következő parancsot: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

A [argumentumok](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create) a a `create` parancs tartalmazza:

| Argumentum  | Leírás |
|---------|---------|
|`display-name` | A regisztrációs rövid neve |
|`homepage` | URL-cím, ahol a felhasználók bejelentkezés és az alkalmazás használata |
|`identifier-uris` | Lemezterület-egymástól egyedi URI-azonosítók, amelyek az Azure AD alkalmazás |

Mielőtt az csatlakozna az Azure Active Directory, a következő információkat kell:

| Név  | Leírás | A konfigurációs fájlban változó neve |
| ------------- | ------------- | ------------- |
| Bérlő neve  | [Bérlő neve](active-directory-howto-tenant.md) a hitelesítéshez használni kívánt | `tenantName`  |
| Ügyfél-azonosító  | Ügyfél-azonosító az aad-ben használt OAuth-kifejezésnek _Alkalmazásazonosító_. |  `clientID`  |

A regisztrációs válasz az Azure-felhő rendszerhéj, másolja a `appId` értékét, és hozzon létre egy új fájlt `config.js`. Ezután adja hozzá az alábbi kódot, és cserélje le az értékeket a zárójeles jogkivonatokat:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```
Az egyes konfigurációs beállítások kapcsolatos további információkért tekintse át a [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modul dokumentációját.

## <a name="implement-the-server"></a>A kiszolgáló megvalósítása
A [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modul szolgáltatások két hitelesítési stratégiák: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) és [tulajdonosi](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) stratégiák. A kiszolgáló, a cikkben végrehajtott tulajdonosi stratégiát használja a biztonságos az API-végpontot.

### <a name="step-1-import-dependencies"></a>1. lépés: Import függőségek
Hozzon létre egy új fájlt `app.js` és illessze be a következő szöveget:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

Az Itt kódot:

- A `restify` és `restify-plugins` modulok hivatkozott ahhoz, hogy a Restify-kiszolgáló beállítása.

- A `passport` és `passport-azure-ad` modulok felelősek az AAD-ben való kommunikációhoz.

- A `config` változó értékeivel inicializálva van a `config.js` az előző lépésben létrehozott fájlt.

- Létrejön egy tömb `authenticatedUserTokens` felhasználói jogkivonatokhoz tárolja a védett végpontok feleltek meg.

- A `serverPort` vagy definiálva van a folyamat környezet vagy a konfigurációs fájlból.

### <a name="step-2-instantiate-an-authentication-strategy"></a>2. lépés: Példányosítható hitelesítési stratégia kialakítása
Biztonságos végpont, mivel meg kell adnia a felel annak meghatározásáért stratégiát, az aktuális kérelem hitelesített felhasználók származik-e. Itt a `authenticatonStrategy` változó egy példányát a `passport-azure-ad` `BearerStrategy` osztály. Az alábbi kód után adja hozzá a `require` utasításokat.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```
Ez a megvalósítás automatikus regisztráció használ a hitelesítési tokenek történő hozzáadásával a `authenticatedUserTokens` tömb, ha még nem léteznek.

A stratégia új példányának létrehozása után át kell adnia azt a Passport használatával a `use` metódust. Adja hozzá a következő kódot a `app.js` Passport stratégia használatára.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>3. lépés: Kiszolgáló konfigurálása
A hitelesítési biztosításával kapcsolatos stratégia definiált mostantól a Restify-kiszolgáló néhány alapvető beállításokkal beállítása és használni a Passport for biztonsági beállítása.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Ez a kiszolgáló inicializálása és engedélyezési fejléceket elemzése és konfigurált Passport használata akkor értéke.


### <a name="step-4-define-routes"></a>4. lépés: Útvonalak megadása
Ezután adja meg az útvonalakat és döntse el, amelyek védelmét az aad-ben. Ez a projekt tartalmazza két útvonalakat, ahol a gyökérszinten meg nyitva, és a `/api` útvonal van beállítva, hogy hitelesítésre van szükség.

A `app.js` adja hozzá a következő kódot a gyökér szintű útvonal:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

A legfelső szintű útvonal lehetővé teszi, hogy az útvonal keresztül összes kérelmet, és adja vissza egy üzenetet, amely tesztelése egy olyan parancsot tartalmaz a `/api` útvonal. Ezzel szemben a `/api` útvonal zárolva használatával [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Adja hozzá a következő kódot a legfelső szintű útvonal után.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Ez a konfiguráció csak lehetővé teszi a hitelesített kérelmek egy tulajdonosi jogkivonatot hozzáférést tartalmazó `/api`. A lehetőség a `session: false` letiltja az szükséges, hogy a jogkivonat az API minden egyes kérelemmel közlekednek munkamenetek.

Végül a kiszolgáló meghívásával a konfigurált port figyelésére van beállítva a `listen` metódust.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Minta futtatása

Most, hogy a kiszolgáló van telepítve, indítsa el a kiszolgálón nyissa meg egy parancssort, és írja be:

```Shell
npm start
```

A futtató kiszolgálóval elküldheti a kérelem a kiszolgálóra a teszteredmények. A legfelső szintű útvonal válaszát, nyissa meg a bash rendszerhéjat, és írja be a következő kódot:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Ha a kiszolgáló megfelelően van beállítva, a válasz hasonlóan kell kinéznie:

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

A következő tesztelheti az útvonalat, amely megköveteli a hitelesítést az alábbi parancs beírásával a bash rendszerhéjat:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Ha megfelelően konfigurálta a kiszolgálót, akkor a kiszolgáló állapota kell válaszolnia `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Most, hogy a biztonságos API létrehozott, egy ügyfelet, amelyben tudja átadni a hitelesítési tokenek az API-t is létrehozható.

## <a name="next-steps"></a>További lépések
Amint azt a bevezetés, meg kell valósítani egy ügyfél megfelelője, amely kezeli a bejelentkezés, a Kijelentkezés és a jogkivonatok kezelése a kiszolgálóhoz való csatlakozáshoz. Példák kódalapú nézze meg az ügyfélalkalmazások számára a [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) és [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Tekintse meg a következő cikk részletes oktatóanyaga:

> [!div class="nextstepaction"]
> [NODE.js-webalkalmazás be- és kijelentkezési az Azure ad szolgáltatással](active-directory-devquickstarts-openidconnect-nodejs.md)