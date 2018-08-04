---
title: Az Azure AD Node.js webes API-k első lépések |} A Microsoft Docs
description: Hogyan hozhat létre egy Node.js REST webes API-t, amely integrálható az Azure AD-hitelesítés.
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
ms.openlocfilehash: b05512804b35f7df3dfd2899eed975b93d36f951
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503383"
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Az Azure AD Node.js webes API-k első lépések

Ez a cikk bemutatja, hogyan teheti biztonságossá a [létrehozásához a restify programot](http://restify.com/) az API-végpont [Passport](http://passportjs.org/) használatával a [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modul kezelni a kommunikációt az Azure Active Directory (AAD). 

Ebben az oktatóanyagban kiterjed a aggályokat kapcsolatos biztonságossá tétele API-végpontokon. Bejelentkezés és a hitelesítési tokenek megőrzése fontos szempont nejsou implementovány itt, és egy ügyfélalkalmazás kell eljárnia. Egy ügyfél megvalósítása körülvevő részletekért tekintse át a [Node.js-webalkalmazás bejelentkezési és kijelentkezési az Azure ad-vel](quickstart-v1-openid-connect-code.md).

A teljes kódmintát, ez a cikk társított érhető el az [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>A mintaprojekt létrehozása
A Restify és Passport támogatása, valamint a fiókadatok AAD átadott néhány csomagfüggőségek szükséges.

Első lépésként adja a következő kódot egy fájlba nevű `package.json`:

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

Egyszer `package.json` jön létre, futtassa `npm install` a parancssorban a csomag függőségek telepítéséhez. 

### <a name="configure-the-project-to-use-active-directory"></a>Konfigurálja az Active Directory használatára

Első lépésként az alkalmazás konfigurálása nincsenek fiók-specifikus értékeket szerezhet be az Azure parancssori felület. A legegyszerűbben úgy, hogy a CLI használatának első lépései az Azure Cloud Shell használatához.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A cloud shellben adja meg a következő parancsot: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

A [argumentumok](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) számára a `create` parancsot tartalmazza:

| Argumentum  | Leírás |
|---------|---------|
|`display-name` | A regisztráció rövid neve |
|`homepage` | URL-címet, ahol a felhasználók jelentkezzen be és használják az alkalmazást |
|`identifier-uris` | Hely elválasztott egyedi URI-k, amelyek az Azure AD használható ehhez az alkalmazáshoz |

Mielőtt az csatlakozna az Azure Active Directoryhoz, szüksége van a következő információkat:

| Name (Név)  | Leírás | A konfigurációs fájlban változó neve |
| ------------- | ------------- | ------------- |
| Bérlő neve  | [A bérlő nevét](quickstart-create-new-tenant.md) a hitelesítéshez használni kívánt | `tenantName`  |
| Ügyfél-azonosító  | Ügyfél-azonosító az AAD OAuth kifejezés _Alkalmazásazonosító_. |  `clientID`  |

A regisztrációs válasz, az Azure Cloud shellben, másolja a `appId` értékét, és hozzon létre egy új fájlt `config.js`. Ezután adja hozzá az alábbi kódot, és cserélje le az értékeket a zárójeles jogkivonatokat:

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
Az egyes konfigurációs beállítások kapcsolatos további információkért tekintse át a [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modul dokumentációjában talál.

## <a name="implement-the-server"></a>A kiszolgáló megvalósítása
A [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modul funkciók két hitelesítési stratégiák: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) és [tulajdonosi](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) stratégiák. A kiszolgáló, ebben a cikkben végrehajtott biztonságossá tétele az API-végpont tulajdonosi stratégiát használja.

### <a name="step-1-import-dependencies"></a>1. lépés: Import-függőségek
Hozzon létre egy új fájlt `app.js` illessze be a következő szöveget:

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

Ebben a kód szakaszban:

- A `restify` és `restify-plugins` modulok hivatkozott annak érdekében, hogy a Restify kiszolgáló beállításához.

- A `passport` és `passport-azure-ad` modulok felelős az aad-vel való kommunikációhoz.

- A `config` változó értékeit inicializálva van a `config.js` az előző lépésben létrehozott fájlt.

- Létrejön egy tömb `authenticatedUserTokens` felhasználói jogkivonatok tárolására, a rendszer átad a biztonsági biztonságos végpontok.

- A `serverPort` vagy meghatározása a folyamat környezet port vagy a konfigurációs fájlból.

### <a name="step-2-instantiate-an-authentication-strategy"></a>2. lépés: Hozza létre a hitelesítési stratégia
Védelmét egy végpontot, ahogy meg kell adnia egy stratégia felelős megállapítani az aktuális kérelem egy hitelesített felhasználó származik-e. Itt a `authenticatonStrategy` változó egy példányát a `passport-azure-ad` `BearerStrategy` osztály. Az alábbi kód után adja hozzá a `require` utasításokat.

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
Ez a megvalósítás automatikus regisztráció használ a hitelesítési tokenek történő hozzáadásával a `authenticatedUserTokens` tömböt, ha azok nem léteznek.

Egy új példányát a stratégia létrehozása után előtt meg kell adnia a Passport használatával történő a `use` metódust. Adja hozzá a következő kódot a `app.js` Passport stratégia használatára.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>3. lépés: Kiszolgáló konfigurálása
A megadott hitelesítési stratégiát most is néhány alapvető beállításokkal a Restify-kiszolgáló beállítása és használni a Passport for security állítsa.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Ezen a kiszolgálón van inicializálva és konfigurálva elemezni a hitelesítési fejléceket, majd beállíthatja a Passport használata.


### <a name="step-4-define-routes"></a>4. lépés: Az útvonalak megadása
Mostantól útvonalak megadása, és döntse el, amelyek védelmét az aad-ben. Ez a projekt tartalmazza a két útvonal, ahol a gyökérszintű nyitva-e, és a `/api` útvonal hitelesítés úgy van beállítva.

A `app.js` adja hozzá a következő kódot a gyökér szintű útvonal:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

A legfelső szintű útvonal lehetővé teszi, hogy az útvonal keresztül, és a egy parancs teszteléséhez tartalmazó üzenetet adja vissza a `/api` útvonalat. Ezzel szemben a `/api` útvonal zárolva használatával [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Adja hozzá a következő kódot a legfelső szintű útvonal után.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Ez a konfiguráció csak lehetővé teszi a hitelesített kérelmek, amelyek egy tulajdonosi jogkivonat hozzáférést biztosít `/api`. A lehetőség a `session: false` letiltja az szükséges, hogy a jogkivonat minden egyes kérelemmel továbbítódik az API-munkameneteket.

Végül, a kiszolgáló meghívásával a konfigurált port figyelésére van beállítva a `listen` metódust.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Minta futtatása

Most, hogy a kiszolgáló van telepítve, a parancssort. Ehhez másolatot megnyitásával indítsa el a kiszolgálót, és írja be:

```Shell
npm start
```

Futó Server küldhet egy kérést az eredmények teszteléséhez. A legfelső szintű útvonal válasza bemutatása érdekében nyissa meg a bash felületet, és írja be a következő kódot:

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

Ezután tesztelheti az útvonal, amelyhez hitelesítés szükséges a bash rendszerhéjat a következő parancs beírásával:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Ha a kiszolgáló megfelelően van konfigurálva, akkor a kiszolgáló állapottal kell válaszolnia `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Most, hogy létrehozott egy biztonságos API-t, valósítható meg, amely képes hitelesítési jogkivonatok átadása az API-t az ügyfél.

## <a name="next-steps"></a>További lépések
Amint azt a bevezetés, meg kell valósítani egy ügyfél megfelelője, amely kezeli a bejelentkezés, jelentkezzen ki, majd a jogkivonatok kezeléséhez a kiszolgálóhoz való csatlakozáshoz. Példák kódalapú nézze meg az ügyfélalkalmazások a [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) és [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Részletes útmutató tekintse meg a következő cikket:

> [!div class="nextstepaction"]
> [NODE.js-webalkalmazás bejelentkezési és kijelentkezési az Azure ad-vel](quickstart-v1-openid-connect-code.md)