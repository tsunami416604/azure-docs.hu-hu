---
title: Webes API védelme az Azure AD-vel | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Node.js-alapú REST webes API-t, amely az Azure AD-vel integrálható a hitelesítéshez.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ed159decb51d71e8c0beddb285f6c01ae264ed2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206667"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>Gyors útmutató: Védelem biztosítása webes API-t az Azure Active Directoryval

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Ebből a rövid útmutatóból megtanulhatja, hogyan biztosíthat védelmet a [Restify](http://restify.com/) API-végpont számára a [Passporttal](http://passportjs.org/) úgy, hogy a [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modult használja az Azure Active Directoryval (Azure AD) folytatott kommunikáció kezelésére.

Ez a rövid útmutató kiterjed az API-végpontok védelmével kapcsolatos problémákra. A bejelentkezéssel és hitelesítési jogkivonatok megtartásával kapcsolatos kérdéseket ez a cikk nem tárgyalja; ezekért az ügyfélalkalmazások felelnek. További információ az ügyfél-megvalósításról: [be- és kijelentkezés a Node.js-webalkalmazásból az Azure AD-vel](quickstart-v1-openid-connect-code.md) című cikket.

A cikkhez tartozó teljes kódminta elérhető a [GitHubon](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként teljesítse az alábbi előfeltételeket.

### <a name="create-the-sample-project"></a>A mintaprojekt létrehozása

A kiszolgálói alkalmazásnak szüksége van néhány csomagfüggőségre a Restify, a Passport, valamint az Azure AD-nek átadott fiókadatok támogatásához.

Kezdetnek adja hozzá a következő kódot a `package.json` nevű fájlhoz:

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

A `package.json` létrehozása után futtassa az `npm install` parancsot a parancssorban a csomagfüggőségek telepítéséhez. 

#### <a name="configure-the-project-to-use-active-directory"></a>A projekt konfigurálása az Active Directory használatára

Az alkalmazás konfigurálásának megkezdéséhez beszerezhet néhány fiókspecifikus értéket az Azure CLI-ből. A CLI használata az Azure Cloud Shell-lel kezdhető el a legegyszerűbben.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Írja be a következő parancsot a Cloud Shellbe:

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

A `create` parancs [argumentumai](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) a következők:

| Argumentum  | Leírás |
|---------|---------|
|`display-name` | A regisztráció rövid neve |
|`homepage` | Az URL-cím, ahol a felhasználók bejelentkezhetnek és használhatják az alkalmazást |
|`identifier-uris` | Szóközökkel elválasztott egyedi URI-k, amelyeket az Azure AD használhat az alkalmazáshoz |

Mielőtt csatlakozhatna az Azure Active Directoryhoz, szüksége lesz az alábbi információkra:

| Name (Név)  | Leírás | Változó neve a konfigurációs fájlban |
| ------------- | ------------- | ------------- |
| Bérlő neve  | A hitelesítéshez használni kívánt [bérlőnév](quickstart-create-new-tenant.md) | `tenantName`  |
| Ügyfél-azonosító  | Az ügyfél-azonosító az AAD _alkalmazásazonosítóra_ használt OAuth-kifejezés. |  `clientID`  |

Az Azure Cloud Shell regisztrációs válaszából másolja ki az `appId` értéket, és hozzon létre egy új fájlt `config.js` néven. Ezután adja hozzá a következő kódot, majd cserélje le az értékeket a zárójeles jogkivonatokra:

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

Az egyéni konfigurációs beállításokról a [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modul dokumentációjában talál további információt.

### <a name="implement-the-server"></a>A kiszolgáló megvalósítása

A [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modul két hitelesítési stratégiák funkciók: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) és [tulajdonosi](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) stratégiák. A cikkben megvalósított kiszolgáló a tulajdonosi stratégiát használja az API-végpont védelmére.

### <a name="step-1-import-dependencies"></a>1. lépés: Importálja a függőségeket

Hozzon létre egy új fájlt `app.js` néven, és illessze be a következő szöveget:

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

Ebben a kódszakaszban:

- A `restify` és a `restify-plugins` modulokra való hivatkozás egy Restify-kiszolgáló beállítására szolgál.
- A `passport` és a `passport-azure-ad` modulok felelnek az Azure AD-vel való kommunikációért.
- A `config` változó inicializálása az előző lépésben létrehozott `config.js` fájl értékeivel történik.
- A rendszer létrehoz egy tömböt az `authenticatedUserTokens` számára, amely a felhasználói jogkivonatokat tárolja a védett végpontoknak való átadáskor.
- A `serverPort` a feldolgozó környezet portjából vagy a konfigurációs fájlból lesz meghatározva.

### <a name="step-2-instantiate-an-authentication-strategy"></a>2. lépés: Hitelesítési stratégia vezérlőként

Amikor beállítja egy végpont védelmét, meg kell adnia egy stratégiát, amely meghatározza, hogy a jelenlegi kérés hitelesített felhasználótól származik-e. Az `authenticatonStrategy` változó itt a `passport-azure-ad` `BearerStrategy` osztályának egyik példánya. Adja hozzá a következő kódot a `require` utasítások után.

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

Ez a megvalósítás automatikus regisztrációt használ azáltal, hogy hitelesítési jogkivonatokat ad hozzá az `authenticatedUserTokens` tömbhöz, ha még nem léteznek.

Ha létrejön a stratégia egy új példánya, továbbítsa a Passportnak a `use` metódussal. A stratégia Passportban való használatához adja hozzá a következő kódot az `app.js` fájlhoz.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>3. lépés: Kiszolgálókonfiguráció

Ha meghatározta a hitelesítési stratégiát, beállíthatja a Restify kiszolgálót néhány alapvető beállítással, és azt is beállíthatja, hogy a Passportot használja a védelemhez.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Ez a kiszolgáló inicializálva van, és úgy konfigurálták, hogy elemezze az engedélyeztetési fejléceket, majd beállítsa a Passport használatát.

### <a name="step-4-define-routes"></a>4. lépés: Útvonalak megadása

Most már meghatározhat útvonalakat, és eldöntheti, hogy melyeket védje az Azure AD-vel. Ez a projekt két olyan útvonalat tartalmaz, ahol a gyökérszint nyitva van, az `/api` útvonal pedig úgy van beállítva, hogy hitelesítést igényeljen.

Az `app.js` fájlban adja hozzá a következő kódot a gyökérszintű útvonalhoz:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

A gyökérútvonal az összes kérést átengedi az útvonalon, és visszaad egy üzenetet, amely egy parancsot tartalmaz az `/api` útvonal teszteléséhez. Ezzel szemben az `/api` útvonal le van zárva a [`passport.authenticate`](http://passportjs.org/docs/authenticate) használatával. Illessze be a következő kódot a gyökérútvonal után.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Ez a konfiguráció csak azokat a hitelesített kéréseket engedi át, amelyek tulajdonosi jogkivonat hozzáférést tartalmaznak a `/api` útvonalhoz. A `session: false` kapcsolót a munkamenetek letiltására használják, hogy minden egyes kérés átadja a jogkivonatot az API-nak.

Végül pedig a kiszolgáló arra van beállítva, hogy figyelje a konfigurált portot a `listen` metódus meghívásával.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Minta futtatása

Most, hogy megvalósította a kiszolgálót, nyisson meg egy parancssort az elindításához, és írja be a következőt:

```shell
npm start
```

Ha a kiszolgáló fut, elküldhet egy kérést a kiszolgálónak az eredmények tesztelésére. A gyökérútvonalról érkező válasz bemutatásához nyisson meg egy bash felületet, és írja be a következő kódot:

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

Ha megfelelően konfigurálta a kiszolgálót, a válasz az alábbihoz hasonlóan néz ki:

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Következő lépésként tesztelheti a hitelesítést igénylő útvonalat. Ehhez írja be a következő parancsot a bash felületbe:

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

Ha megfelelően konfigurálta a kiszolgálót, akkor a kiszolgáló `Unauthorized` állapottal válaszol.

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

Most, hogy létrehozott egy biztonságos API-t, megvalósíthat egy ügyfelet, amely át tudja adni a hitelesítési jogkivonatokat az API-nak.

## <a name="next-steps"></a>További lépések

* A bejelentkezést, kijelentkezést és jogkivonat-kezelést végző kiszolgálóhoz való csatlakozáshoz meg kell valósítania egy megfelelő ügyfelet. Kódalapú példákért tekintse meg az ügyfélalkalmazásokat az [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)-es és az [androidos](https://github.com/MSOpenTech/azure-activedirectory-library-for-android) kódtárban.
* Részletes oktatóanyagért lásd: [Be- és kijelentkezés egy Node.js-webalkalmazásba az Azure AD-vel](quickstart-v1-openid-connect-code.md).
